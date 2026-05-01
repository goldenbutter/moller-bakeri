# BUILD PROGRESS — bakeri-demo

> **Purpose:** authoritative running log of every change made to this prototype, tied to a git commit. Any future agent or collaborator picking up this repo should be able to read this top-to-bottom and know exactly what state the build is in, why each decision was made, and how to revert.
>
> **Rules for keeping this file healthy:**
> 1. Append a new entry for every commit. Never edit a past entry except to fix typos.
> 2. The `Commit` field is filled in **after** the commit lands (by reading `git rev-parse HEAD`).
> 3. Always describe **what changed**, **why** (referencing the customer / spec / review feedback), and **how to revert** (which file ranges were affected).
> 4. Update `## Current state of each tier` at the bottom whenever a tier-level rule changes (e.g. "classic now has no animations", "premium now has X feature").

---

## Project context

- **Brand inside the prototype:** Møller Bakeri (Bergen, fictional family bakery, est. 1962)
- **Customer category:** small Norwegian bakery — generic enough to fork into any real bakery customer
- **Architecture verdict:** vanilla HTML + CSS + JS (per `_docs/_skill/ARCHITECTURE-DECISION-GUIDE.md`)
- **Two tiers ship together:**
  - `demo-bakeri-classic/` — ~9999 NOK target price, simple static marketing site
  - `demo-bakeri-premium/` — ~17000–19000 NOK target price, classic + signature interactive features
- **Locked across both tiers (per family rules):**
  - Vipps demo modal phrase (NO): *"Dette er en demo — Vipps-betaling aktiveres når din konto er satt opp."*
  - Footer credit: *"Utviklet av Bithun"* (NO) / *"Developed by Bithun"* (EN where applicable)
  - Real `favicon.svg` (wheat-stalk glyph)
  - Vercel Web Analytics snippet on every page
- **Locked premium-only signature features:**
  1. Daily-bake live counter (animated count-up, time-of-day aware)
  2. Sourdough timeline (5-step, highlights current stage by hour-of-day)
  3. Animated croissant-icon dividers
  4. Animated hero (MP4 video with JPG poster fallback)

---

## Change log (newest at bottom — append, do not reorder)

### Commit `<pending>` — Initial scaffold + images + hero video
- **Date:** 2026-05-01
- **Type:** initial commit
- **What:** First commit of the prototype repo. Captures everything built across the prior conversation:
  - `demo-bakeri-classic/` — 5 HTML pages (index, menu, about, gallery, 404), single `css/style.css`, single `js/main.js`, favicon, full asset set (48 image files including renamed copies for the menu and gallery slots)
  - `demo-bakeri-premium/` — fork of classic + 3 signature features (live bake counter, sourdough timeline, animated croissant dividers) + hero video element + premium-only CSS/JS extensions. 49 image files + `assets/videos/hero-bread.mp4`
  - `.claude/Nano-Banana-prompt.md` — image generation prompts (gitignored — internal)
  - `.claude/BUILD_PROGRESS.md` — this log (tracked)
  - `.gitignore` — ignores `.claude/*` except `BUILD_PROGRESS.md`
- **Why:** baseline before customer-style review and refinement. Lets every subsequent change be auditable.
- **How to revert:** `git reset --hard <this commit>` resets to the exact "as-shipped after Nano Banana run" state.

### Future entries

> Template for the next commit log entry — copy and fill in:
>
> ```
> ### Commit `<sha>` — <short title>
> - **Date:** YYYY-MM-DD
> - **Type:** feat | fix | chore | refactor | revert
> - **Files touched:** <list>
> - **What:** <factual description of the change>
> - **Why:** <link to review feedback / spec / customer ask>
> - **How to revert:** `git revert <sha>` (or specific lines if hand-revert is preferred)
> ```

---

## Current state of each tier

### `demo-bakeri-classic/`
- 5 HTML pages, single CSS, single JS
- **i18n:** NO/EN toggle is **active** (will change in next commit per review — Norwegian-only baseline)
- **Animations:** card hover lifts, fade-up reveals, page fade, button hover transforms — all **active** (will be stripped in upcoming commit)
- **Mobile menu:** full-screen overlay with large serif links — **flagged as ugly**, redesign pending
- **Visitor badge:** rendered in footer — **will be removed in next commit** (premium-only feature)
- **Vipps modal trigger:** "Forhåndsbestill" buttons on each menu item

### `demo-bakeri-premium/`
- Same 5 HTML pages + 3 signature features layered into `index.html`
- **i18n:** NO/EN toggle active (stays — bilingual is part of premium tier)
- **Animations:** classic baseline + croissant bobs, counter count-up, timeline ring-pulse + progress fill, ambient hero video
- **Mobile menu:** same full-screen overlay as classic — **flagged as ugly**, will be redesigned to a 320px liquid-glass side panel
- **Visitor badge:** rendered in footer — **stays** (premium feature)
- **Hero video:** `assets/videos/hero-bread.mp4` (loaded with JPG poster fallback)
