# Supernova Artworks Website

Public website for Supernova Artworks, hosted via GitHub Pages.
Live URL: **https://novjean.github.io/supernova-artworks-website/**

## Structure

```
images/          # Book cover images (AVIF + original PNG/JPG)
layout-a.html    # Layout A preview — Bookshelf Showcase (dark navy, horizontal scrolling)
layout-b.html    # Layout B preview — Editorial Alternating (cinematic dark, full-bleed rows)
layout-c.html    # Layout C — ACTIVE DESIGN (cream/white, studio minimal, gallery grid)
index.html       # Homepage (update this to point to chosen layout)
```

## Active Layout: C (Studio Minimal)

Layout C is the chosen design. Key features:
- Cream/white background, Playfair Display serif headings, clean editorial feel
- 4-column coloring book grid with hover effects
- Featured spread for newest/highlighted book
- Story books: horizontal card layout
- Mini grid for remaining books
- Philosophy / About section with stats
- Newsletter signup
- Sticky nav with scroll shadow

### Festival Highlighting

Layout C includes a JavaScript festival date engine (`FESTIVALS` object in the `<script>` block at bottom of file). It automatically adds badges to book covers:

- **Green "Celebrate Now!" badge** — festival is within ±21 days (configurable `window` per book)
- **Gold "Coming Soon" badge** — festival is 22–60 days away
- **Black "New" badge** — books listed in `NEW_BOOKS` array (update manually when releasing a new book)

To update festival dates (for variable holidays like Easter, Vesak, Fasika), edit the `FESTIVALS` object each year:

```js
const FESTIVALS = {
  'songkran':     [{ month: 4, day: 13, window: 21 }],
  'vishu':        [{ month: 4, day: 14, window: 14 }],
  'vaisakhi':     [{ month: 4, day: 13, window: 14 }],
  'semana-santa': [{ month: 3, day: 29, window: 21 }, { month: 4, day: 5, window: 21 }],
  'fasika':       [{ month: 4, day: 19, window: 21 }],
  'vesak':        [{ month: 5, day: 10, window: 21 }],
  'easter':       [{ month: 4, day:  5, window: 14 }],
  'khmer':        [{ month: 4, day: 13, window: 14 }],
  'avurudu':      [{ month: 4, day: 13, window: 14 }],
};
```

Each entry in `FESTIVALS` maps `data-book="key"` on the `.book-cell` div to its festival dates.

## Image Pipeline

### Format: AVIF + PNG/JPG fallback

All cover images are stored in two formats in `images/`:
- `NAME.avif` — AVIF at CRF 18 (high quality, 85–90% smaller than PNG)
- `NAME.png` / `NAME.jpg` — original fallback for older browsers

The HTML uses `<picture>` elements:
```html
<picture>
  <source srcset="images/songkran.avif" type="image/avif">
  <img src="images/songkran.png" alt="Songkran" />
</picture>
```

### Converting a new cover to AVIF

Place the original cover (PNG or JPG) in `images/`, then run:

```bash
cd images/
ffmpeg -y -i BOOKNAME.png -c:v libsvtav1 -crf 18 -pix_fmt yuv420p BOOKNAME.avif
```

Use `crf 18` for high quality. Range is 1–63 (lower = better). Do NOT go above 25 for covers.

### Adding a new book to the website

1. Copy front cover to `images/BOOKNAME.png`
2. Convert to AVIF (command above)
3. Add a new `.book-cell` block in `index.html` (copy an existing one)
4. Set `data-book="BOOKNAME"` on the `.book-cell` div
5. **Set the Amazon ASIN link on the `.book-cell-buy` anchor** — this is what makes the whole card clickable (see Clickable Images below)
6. Add the festival date entry to `FESTIVALS` in the JS block
7. Add `BOOKNAME` to `NEW_BOOKS` array in the JS (for the "New" badge)
8. Commit and push — GitHub Pages deploys in ~1 minute

## Clickable Images

Every image on the site links to its Amazon page. The system works differently depending on which section the image is in:

### Book grid (`.book-cell`) — automatic via JS

The whole card becomes clickable automatically. No extra work needed — the JS reads the `href` from the `.book-cell-buy` anchor already inside each card and opens it when the card is clicked.

**Rule: always fill in the `.book-cell-buy` href with the direct Amazon ASIN link:**
```html
<div class="book-cell" data-book="eid">
  <div class="book-cell-img"> ... </div>
  ...
  <a href="https://www.amazon.com/dp/ASIN_HERE" target="_blank" class="book-cell-buy">
    Buy on Amazon ...
  </a>
</div>
```
If no ASIN exists yet, use the author page as a fallback:
`https://www.amazon.com/stores/Novjean-John-Kannathara/author/B0FJRNF1VR`

### Story cards (`.story-card`) — automatic via JS

Same as above — JS reads the `.story-card-buy` href and makes the whole card clickable. Always set the correct link on `.story-card-buy`.

### Mini grid (`.mini-cell`) — automatic via JS

JS reads the first `<a>` inside `.mini-cell` (the "Buy →" overlay link) and makes the cell clickable. Always set the `href` on that link.

### Hero images (`.hero-card`) — link is on the element itself

Hero cards are `<a>` tags, not `<div>` tags. When adding a new hero image, use:
```html
<a href="https://www.amazon.com/dp/ASIN_HERE" target="_blank" class="hero-card">
  <picture>...</picture>
  <div class="hero-card-label">Book Title</div>
</a>
```

### Featured spread (`.featured-img`) — link is on the element itself

The featured image wrapper is an `<a>` tag. Update its `href` when changing the featured book:
```html
<a href="https://www.amazon.com/dp/ASIN_HERE" target="_blank" class="featured-img">
  <picture>...</picture>
  <div class="featured-img-overlay"></div>
</a>
```

### Geo-routing

The JS geo-routing block automatically rewrites all `amazon.com` links to the user's local marketplace (e.g. `amazon.co.uk`, `amazon.de`). This applies to every link on the page — including hero and featured `<a>` tags — so no extra work is needed when adding new books.

## Deployment

The site is hosted via **GitHub Pages** from the `main` branch of this public repo (`novjean/supernova-artworks-website`).

```bash
git add .
git commit -m "Your message"
git push
# Live in ~60 seconds at https://novjean.github.io/supernova-artworks-website/
```

No build step, no CI, no servers. Pure static HTML.

## Font Stack

- Headings: **Playfair Display** (Google Fonts, serif)
- Body: **Nunito** (Google Fonts, sans-serif)
- Loaded via `<link>` in `<head>` — no local font files needed

## Color Palette (Layout C)

| Variable  | Value     | Use                        |
|-----------|-----------|----------------------------|
| `--cream` | `#FAF8F4` | Page background            |
| `--ink`   | `#111111` | Headings, nav, footer      |
| `--gold`  | `#C8992A` | Accents, links, badges     |
| `--gold2` | `#E8B84B` | Hover states               |
| `--muted` | `#888880` | Secondary text             |
| `--rule`  | `rgba(0,0,0,0.10)` | Dividers, grid lines |
