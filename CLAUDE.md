# CLAUDE.md — Revive Thrive Codebase Guide

## Project Overview

**Revive Thrive** is a wholesale pharmaceutical and dietary supplement catalog website targeting clinics and traders in Egypt. The entire application is a single self-contained HTML file (`index.html`, ~485 KB) — no framework, no build step, no backend.

- **Language:** Arabic (RTL layout, `lang="ar" dir="rtl"`)
- **Contact channel:** WhatsApp `+201500343014`
- **Stack:** Vanilla HTML5 + CSS3 + ES6 JavaScript, zero dependencies

---

## Repository Structure

```
revive-thrive-complete/
├── index.html      # Entire application — HTML, CSS, and JS in one file
└── README.md       # Minimal project title only
```

All CSS lives in a `<style>` block in `<head>`. All JavaScript lives in a `<script>` block at the end of `<body>`. Product images are embedded as base64 data URIs.

---

## Architecture

### Sections (in document order)

| HTML ID | Purpose |
|---------|---------|
| `#hero` | Landing — headline, stats, CTA buttons |
| `#products` | Product catalog grid (4 categories, 24 products) |
| `#why` | Value proposition / advantages |
| `#how` | 3-step ordering process |
| `#ctaf` | Final call-to-action |
| `footer` | Copyright |
| `#fab` | Floating WhatsApp action button (fixed position) |

### Product Categories

| CSS Class | Name | Color accent | Products |
|-----------|------|--------------|----------|
| `.cx` | X-FIT | Red `#D94A4A` | 11 burn/appetite capsules |
| `.ch2` | Healthy Diet | Green `#2ECC71` | 4 diet capsules |
| `.ci2` | Premium Diet | Gold `#C6A84E` | 6 independent products |
| `.ca` | Ampoules | Blue `#4A90D9` | 3 injections |

Each category is a `.cb` block containing a `.ch` header and a `.pg` product grid.

### CSS Design Tokens (`index.html:11`)

```css
:root {
  --gold: #C6A84E;   /* primary accent */
  --gl:   #E8D48B;   /* gold light */
  --gd:   #9A7B2E;   /* gold dark */
  --dk:   #0A0A0A;   /* page background */
  --dc:   #111111;   /* card background */
  --ds:   #1A1A1A;   /* section background */
  --db:   #2A2A2A;   /* border/divider */
  --tp:   #F5F0E8;   /* primary text */
  --ts:   #A09882;   /* secondary text */
  --tm:   #6B6355;   /* muted text */
  --wa:   #25D366;   /* WhatsApp green */
}
```

Always use these variables — never hardcode color values.

### JavaScript (`index.html:200-212`)

The JS block is minimal and intentionally terse (minified style):

- **Scroll nav:** Adds `.sc` class to `#nav` when `scrollY > 50` → triggers frosted-glass background.
- **Mobile menu:** `tn()` toggles `.open` on `#nl`; links auto-close the menu on click.
- **Image fallback:** `error` handler on `.piw img` replaces broken images with category-specific emoji (`💊` X-FIT, `🥗` Diet, `⭐` Premium, `💉` Ampoules).
- **Scroll reveal:** `IntersectionObserver` adds `.v` to `.reveal` elements when 8% visible with a -30px root bottom margin.

---

## Fonts

Loaded from Google Fonts CDN:
- **Cairo** (weights 300, 400, 600, 700, 900) — headings, nav, labels
- **Tajawal** (weights 300, 400, 500, 700) — body text

---

## Key Conventions

### Editing CSS

- All selectors are minified (no spaces after `:`, properties on one line). Keep this style when adding new rules.
- Responsive breakpoint is `max-width: 768px`.
- Navigation, FAB, and noise overlay (`body::before`) are `position: fixed`.
- Section padding shorthand: `.sp { padding: 80px 5% }`.

### Adding or Updating Products

Each product card follows this pattern inside a `.pg` grid:

```html
<div class="product-card">
  <div class="piw">
    <img src="[data-URI or URL]" alt="[Product Name]" loading="lazy">
    <div class="pio"></div>  <!-- gradient overlay -->
  </div>
  <div class="pi">
    <h3 class="pn">[Product Name]</h3>
    <p class="pd">[Short description]</p>
  </div>
</div>
```

Place the card inside the appropriate category `.cb` block (`.cx`, `.ch2`, `.ci2`, or `.ca`). The category class on the ancestor drives card hover color and top-border gradient automatically.

### Scroll Reveal Animations

Add `class="reveal"` to any element that should fade/slide in on scroll. The `IntersectionObserver` adds `.v` once it's visible — CSS handles the transition. Elements inside `.reveal` are animated as a group.

### WhatsApp Links

The contact number `+201500343014` appears in three places:
- `#fab` href
- `#ctaf` WhatsApp button href
- `nav .ncta` href

When changing the contact number, update all three. The pre-filled message text is URL-encoded Arabic.

---

## Development Workflow

### Running Locally

No build step required. Open `index.html` directly in a browser, or serve it with any static file server:

```bash
python3 -m http.server 8080
# or
npx serve .
```

### Making Changes

1. Edit `index.html` directly — CSS in `<style>`, markup in `<body>`, JS in `<script>`.
2. Verify in a browser (test mobile at ≤768px and RTL layout).
3. Check that product image fallbacks work by temporarily breaking an `src`.
4. Commit and push.

### No Tests / No CI

There are no automated tests or CI pipelines. Manual browser verification is the only QA step.

---

## Deployment

This is a **static file** — deploy anywhere:

- **GitHub Pages:** push to `main`, enable Pages from root.
- **Netlify / Vercel:** drop the repo — zero config needed.
- **Traditional hosting:** upload `index.html` via FTP/cPanel.

No environment variables, no secrets, no server-side logic required.

---

## What NOT to Do

- Do not introduce a framework, bundler, or package manager unless the scope of changes genuinely requires it and the user explicitly agrees.
- Do not split the file into multiple files without explicit instruction — the single-file approach is intentional for portability.
- Do not add comments to the minified CSS/JS blocks; keep them terse as they are.
- Do not hardcode colors — always use CSS custom properties from `:root`.
- Do not change the RTL direction or Arabic content without explicit instruction.
