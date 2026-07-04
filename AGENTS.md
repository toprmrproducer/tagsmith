# TagSmith

Free, fully client-side gamer tag / username generator, monetized via
Google AdSense. Deliberately **single-purpose** — see "Single-tool
strategy" below.

## What this tool does

Generates gamer tags, usernames, and Discord handles by combining a
curated adjective with a gaming-flavored noun, then optionally decorating
the result with a two-digit number or a leetspeak-style `xX…Xx` wrapper.
Four vibes — Fantasy, Sci-Fi, Edgy, Cute — each pull from their own word
lists. Everything runs instantly in the browser from hardcoded word
lists; nothing is ever sent anywhere.

## Stack

- **AstroJS** (static output) + **Tailwind v4** (`@tailwindcss/vite`
  plugin, imported in `src/styles/global.css`)
- No runtime dependencies beyond Astro/Tailwind — the word lists are
  hardcoded TypeScript objects in `index.astro`'s script block, so there
  is nothing to fetch, load, or version.
- **@astrojs/sitemap** — sitemap generation for SEO.
- **Netlify** hosting.

## Design system (mandatory — do not deviate)

Same family look as every sibling standalone tool split out of the
original multi-tool site:

- Background: `#FAF6EC` (warm cream)
- Card/surface: `#FFFFFF`, with a warm `#E8DFC8` border (never gray)
- Headings text: `#2B2013` (warm espresso brown, not pure black)
- Body text: `#5C4F3D` (warm brown-gray)
- Accent / CTA / links / active states: `#C9982E` (warm gold), hover
  `#B8860B` (darker gold)
- Fonts: **Fraunces** (Google Font, soft-serif/display) for ALL headings
  — editorial/premium feel. **Inter** (Google Font) for body text,
  labels, buttons, and all interactive UI. Both loaded via Google Fonts
  `<link>` in `Layout.astro`'s `<head>` with `display=swap`. Headings use
  font-weight 600–700 and slightly negative letter-spacing
  (`tracking-tight` / `-0.015em`).
- Generous whitespace, soft shadows (never harsh), rounded-xl corners on
  cards/buttons, no gradients, no dark mode toggle.

This must read as a small, confident, premium product — a boutique SaaS
landing page, not a generic free-AI-tool dump.

## Single-tool strategy (why there's no nav to other tools)

This is a deliberate split-out from a larger multi-tool site. This repo
contains **only TagSmith** — no navigation to, or mention of, any other
tool. One keyword-matched domain per tool is the whole SEO strategy: a
visitor searching "gamer tag generator" lands on a site that is *only*
about that, with a URL/brand to match, rather than one tool buried in a
20-tool hub. Do not add a multi-tool nav bar or a tools index back to a
parent site.

## Structure

- `src/layouts/Layout.astro` — shared shell: simple header (brand name
  only, no nav to other tools), footer (About/Privacy/FAQ + copyright),
  SEO meta tags (title/description/canonical/OG/Twitter), cream/gold
  theme.
- `src/pages/index.astro` — the tool itself. Vibe select, numbers/
  decoration toggles, Generate button, result card with Copy, last-10
  history list with per-row Copy.
- `src/pages/about.astro`, `privacy.astro`, `faq.astro`, `404.astro` —
  required SEO/trust/AdSense-eligibility pages, linked from both the
  homepage body and the footer.
- `public/robots.txt` — sitemap reference for SEO.

## Security

Any user-controlled text rendered via `innerHTML` MUST go through the
`escapeHtml()` helper defined in `index.astro`'s script block first. In
this tool the generated tags come entirely from a hardcoded word list
(no user-typed input reaches `innerHTML`), so this is defense-in-depth
rather than a live vector — but the helper is kept in place for
consistency with the rest of the tool family and in case a future
feature (e.g. a custom word input) is added.

## Dev / test

- `npm run dev` — runs on **port 4359** (siblings already claim 4326,
  4331, 4332, 4340, 4341; concurrent sibling agent sessions were also
  actively racing for 4340, 4342, and even 4350 at build time — this
  landed on 4359, still inside the assigned 4340-4360 range but far
  enough from the cluster to avoid the live collisions observed).
- Test by clicking the Generate button and asserting `#result-tag`'s
  text content changes and is non-empty; toggle the vibe select and the
  numbers/decoration checkboxes and re-generate to confirm each option
  actually changes the output shape. History list should grow up to 10
  entries, each with a working Copy button.

## iCloud sync hygiene

This project lives in iCloud Drive
(`~/Library/Mobile Documents/com~apple~CloudDocs/website/tagsmith`).
`node_modules` is renamed to `node_modules.nosync` with a `node_modules`
symlink pointing at it, so iCloud does not try to sync hundreds of
thousands of tiny dependency files. `tsconfig.json`'s `exclude` array
includes **both** `"node_modules"` and `"node_modules.nosync"` — `tsc`
does not recognize the `.nosync` suffix as implicitly excluded, and
`astro check` will otherwise try to type-check the entire dependency
tree and crash.

## Deploy

Live at **https://tagsmith-629.netlify.app** — the plain `tagsmith` Netlify
name was already taken when this site was created, so Netlify assigned the
`-629` suffix automatically; site config (`astro.config.mjs`,
`Layout.astro`, `robots.txt`) all point at the real assigned URL.

Netlify site, deployed via the Netlify CLI using the token in
`~/.claude/credentials/netlify.env`:

```
source ~/.claude/credentials/netlify.env
export NETLIFY_AUTH_TOKEN=$NETLIFY_API_KEY
netlify deploy --prod --dir=dist
```

## Still needed before this earns anything (manual, Shreyas)

1. Buy/point a real domain (pick the domain after confirming the build
   works, which it now does).
2. Get real traffic before applying for AdSense.
3. Apply for AdSense, then add `public/ads.txt` with the real
   `pub-XXXXXXXXXXXXXXXX` line and the AdSense script + Auto Ads.
4. Submit to Google Search Console + Bing Webmaster Tools.
