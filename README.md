# 3das.ch

The website for 3DAS at <https://3das.ch>

## Structure

A small set of self-contained static HTML pages — no build step, no framework, no bundler, no package.json.

- `/` → `index.html` — Home
- `/batch-druck/` → `batch-druck/index.html`
- `/schulklassen/` → `schulklassen/index.html`
- `/teambuilding/` → `teambuilding/index.html`
- `/kontakt/` → `kontakt/index.html`

Each page is hand-maintained plain HTML with inline styles and a small vanilla-JS `<script>` at the end of `<body>` (mobile menu toggle, and the contact form's Formspree submission on `kontakt/`) — there is no framework, no shared partial/include mechanism, and no client-side JS dependency required to render content.

`assets/` holds the logo, favicons, and images. `robots.txt` and `sitemap.xml` list all five page URLs.

See `CLAUDE.md` for details on the hover-class CSS convention and the per-page inline scripts.

## Local preview

Open any page file (e.g. `index.html`, `batch-druck/index.html`) directly in a browser.

## Deployment

`.github/workflows/deploy.yml` deploys to GitHub Pages on every push to `main`, uploading the repo root as-is.
