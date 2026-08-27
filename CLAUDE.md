# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a **deploy-artifact repository**, not a source repository. It holds only the
production build output of a Vite + React + React Router + Tailwind single-page app — the
"Stitch Design System" (design tokens and component reference for the Stitch tech-pack
platform by Viora Retail).

There is no application source here: no `package.json`, no build tooling, no tests. Tracked
files are the compiled bundle plus static hosting assets:

- `index.html` — Vite entry; references content-hashed assets under the `/Stitch-DesignSystem/` base path
- `assets/index-*.js`, `assets/index-*.css` — the compiled bundle (hashed filenames, change every build)
- `Logo-backfill.ico` — favicon (referenced by `index.html`)
- `privacy-policy.html`, `terms-of-service.html` — standalone legal pages (plain HTML, not part of the SPA)
- `robots.txt`, `.nojekyll` — static hosting files (`.nojekyll` stops GitHub Pages running Jekyll)

## How this repo gets updated

Commits on `main` are almost all machine-generated, authored by `pranpr`, with messages like
`Update design system docs (from Revia_Stitch@<sha>)`. The real source lives in a separate
repo (`pranpr/Revia_Stitch`); an automation builds it and pushes the `dist/` output here
(roughly every week or two). Those sync commits already handle asset cleanup — they rename/
replace the old hashed `assets/index-*` files so only one JS + one CSS bundle is ever present.

Practical consequences:

- **Your local checkout goes stale.** Always `git fetch` and rebase before doing anything; the
  remote will have moved. A push from a stale base will be rejected as non-fast-forward.
- **Hand edits here are fragile.** Anything you change that also exists in the source project
  (`index.html`, `assets/*`) will be overwritten by the next sync. Durable hand edits are
  limited to files the sync doesn't touch (`CLAUDE.md`, and historically the legal pages).
- `placeholder.svg` is Vite `public/` starter cruft — if a future sync re-emits it, it can be
  deleted again; nothing references it.

## Hosting

Served via **GitHub Pages** for `mana2202/Stitch-DesignSystem`, so every absolute URL carries
the base path `/Stitch-DesignSystem/` (see `index.html` and the React Router `basename`). Any
regenerated build must keep Vite `base: '/Stitch-DesignSystem/'` or the page 404s on its own assets.

App routes (client-side): `/`, `/foundations/{colors,typography,spacing,shadows-radius}`,
`/components/{badge,button,card,dialog,input,select,table,tabs}`. Fonts load from Google Fonts
(DM Mono, Playfair Display) via an `@import` in the CSS.

## Secrets

History was squashed once (ancestor commit `e001fc1`) to purge a leaked Google/Firebase API
key that a previous build had baked into its JS. Before committing any hand-made build here,
grep the bundle for `apiKey`, `firebase`, `authDomain`, `VITE_` — client config must be
injected at runtime by the source project, never committed into `assets/*.js`.
