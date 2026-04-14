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
3. Add a new `.book-cell` block in layout-c.html (copy an existing one)
4. Set `data-book="BOOKNAME"` on the `.book-cell` div
5. Add the festival date entry to `FESTIVALS` in the JS block
6. Add `BOOKNAME` to `NEW_BOOKS` array in the JS (for the "New" badge)
7. Commit and push — GitHub Pages deploys in ~1 minute

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
