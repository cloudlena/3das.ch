# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The marketing website for 3DAS, a Swiss 3D-printing company, deployed at https://3das.ch. It is a small set of **self-contained static HTML pages** — no build step, no bundler, no package.json.

## Pages and URL structure

Each offering has its own real, crawlable URL path (for SEO) rather than being a client-side-only route. Each is an independent, hand-maintained HTML file, not generated from a shared template:

- `/` → `index.html` — Home
- `/batch-druck/` → `batch-druck/index.html`
- `/schulklassen/` → `schulklassen/index.html`
- `/teambuilding/` → `teambuilding/index.html`
- `/kontakt/` → `kontakt/index.html` — reads an optional `?angebot=batch|workshop|team` query param (set by the CTA links on the offering pages) to preselect the "Interessiertes Angebot" dropdown on load.

Every page duplicates the same nav/footer markup, the global `<style>` reset, and a small inline `<script>` at the end of `<body>` — **there is no shared partial/include mechanism**. When changing the nav, footer, brand colors, or global styles, apply the change to all five files by hand. Each page carries its own `<title>`, `<meta name="description">`, and `<link rel="canonical">` in `<head>`.

Root-level `robots.txt` and `sitemap.xml` list all five URLs.

## Local preview

Open `index.html` (or any of the page files, e.g. `batch-druck/index.html`) directly in a browser. There is no dev server or build command.

## Deployment

`.github/workflows/deploy.yml` deploys to GitHub Pages on every push to `main`, uploading the repo root as-is (no build step runs in CI).

## Architecture: plain HTML/CSS + vanilla JS

Each page is plain static HTML with inline `style="..."` attributes (there is no external CSS file) and a small vanilla-JS `<script>` block at the end of `<body>`. There is no framework, no build-time or runtime JS dependency (React/Babel), and no template DSL — everything that ends up in the DOM is present in the page's raw HTML, so it's crawlable and visible with JS disabled.

- **Hover states**: since styling is inline, hover-only rules live in a per-page `<style>` block in `<head>` keyed by small shared class names — `.nav-link`, `.btn-orange`, `.btn-blue`, `.btn-outline`, `.card`, `.footer-link`, `.form-input` (the last for the `:focus` border on `kontakt/`'s inputs). Elements needing a hover/focus effect carry the matching class alongside their inline `style`; add a new class + rule for any new interactive element rather than reaching for a JS mouseenter/mouseleave handler.
- **Mobile nav menu**: `#mobilemenu` is a normal (always-present) `<div>` hidden via `display:none` in CSS and toggled open with a `.open` class from the inline script (`#mobilemenu.open{display:flex}`); the toggle button also flips its own `aria-expanded` attribute.
- **Kontakt form** (`kontakt/index.html`): plain `<form id="contactForm">` with a script that reads `?angebot=` from `location.search` to preselect the dropdown on load, `preventDefault()`s the submit, `fetch()`s the Formspree endpoint (`https://formspree.io/f/xwvdyovj`) as JSON, and swaps the form for `#successPanel` on success (or shows `#formError` on failure). No React state — plain DOM reads/writes by element id.
- Each page's inline script is duplicated by hand (same "no shared partial mechanism" convention as the nav/footer markup) rather than factored into a shared `/*.js` file.

When editing site content or behavior, edit the page's HTML and its trailing `<script>` directly with plain DOM APIs, following the class-based hover convention above rather than reintroducing inline event-handler attributes or a templating layer.

## Brand

- Primary: `#005c9f` (blueprint blue)
- Secondary: `#f39209` (filament orange)
- Fonts: IBM Plex Sans (body), IBM Plex Mono (labels/mono accents), loaded via Google Fonts in `<helmet>`.
- Visual language: blueprint-grid hero background, dark spec bar, numbered `[01] [02] [03]` offering cards.

## Assets

`assets/` holds `logo.svg`, `logo-footer.svg`, favicons, and `map.jpg`. Reference them (and other pages) with paths relative to the current file's depth — `assets/logo.svg` and `batch-druck/` from `index.html`, but `../assets/logo.svg` and `../batch-druck/` from `batch-druck/index.html`, `kontakt/index.html`, etc. The site currently deploys to a GitHub Pages project subpath (no custom domain yet), so root-relative paths (`/assets/...`) would 404; relative paths work under any base path. `<link rel="canonical">`, `og:*`, and the JSON-LD `url`/`image` fields are the exception — they intentionally stay absolute (`https://3das.ch/...`), pointing at the eventual production domain regardless of current hosting.
