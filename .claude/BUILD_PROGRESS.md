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

### Commit `19caacc` — Initial scaffold + images + hero video
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

### Commit `b65ed85` — Comment out visitor-badge in classic
- **Date:** 2026-05-01
- **Type:** chore
- **Files touched:** `demo-bakeri-classic/index.html`, `menu.html`, `about.html`, `gallery.html` (4 files)
- **What:** Wrapped the `<img>` element that renders the laobi.icu visitor-badge inside an HTML comment in all 4 footer instances. URL string preserved. 404.html had no badge to begin with.
- **Why:** Review feedback — page-view counter is a premium-tier feature; classic should not display it. Customer can pay to upgrade and re-enable.
- **How to revert:** `git revert b65ed85`, or hand-uncomment the 4 `<!-- ... -->` blocks (search for "Visitor badge — premium-only feature").

### Commit `725314e` — Comment out i18n + EN toggle in classic
- **Date:** 2026-05-01
- **Type:** chore
- **Files touched:** `demo-bakeri-classic/{index,menu,about,gallery,404}.html`, `js/main.js` (6 files)
- **What:**
  - HTML: lang-toggle and mobile-lang-toggle `<button>`s wrapped in HTML comments in all 5 pages.
  - JS: the entire i18n block (`translations` object + `t/applyTranslations/toggleLanguage/initLangToggle` functions) wrapped in a single `/* ... */` block comment. The two corresponding init calls in `DOMContentLoaded` line-commented.
  - All translation strings preserved verbatim — re-enable is a no-rewrite job.
- **Why:** Review feedback — classic ships Norwegian-only; bilingual is a paid premium upsell. Static Norwegian copy already lives in the HTML elements, so the visible site is unchanged for Norwegian users.
- **How to revert:** `git revert 725314e`. Or hand-revert: remove the wrapping `/* */` in main.js (1 opener, 1 closer), uncomment the two `DOMContentLoaded` calls, uncomment the EN toggle buttons in each HTML file.

### Commit `28a295e` — Strip animations from classic (static-website spec)
- **Date:** 2026-05-01
- **Type:** feat
- **Files touched:** `demo-bakeri-classic/css/style.css`, `js/main.js`
- **What:**
  - CSS: appended a CLASSIC STATIC OVERRIDE block to end of style.css that:
    - kills every transition + animation via universal `*, *::before, *::after`
    - forces `.reveal` and `.reveal-stagger` children visible
    - disables hover lifts, image scale-ups, button shadow intensify
    - keeps base shadows and palette intact
    - disables nav.scrolled box-shadow change
  - JS: in `DOMContentLoaded`, line-commented `initScrollAnimations()` and the `page-fade` class application.
- **Why:** Review feedback — classic must read like a fully static website. No card lifts, no fade-ups, no page fade, no hover transforms.
- **How to revert:** `git revert 28a295e`. The override block is a single contiguous region at end-of-file; deleting it alone restores full motion. JS calls just need uncommenting.

### Commit `aea58ef` — Redesign classic mobile menu (compact dropdown)
- **Date:** 2026-05-01
- **Type:** feat
- **Files touched:** `demo-bakeri-classic/css/style.css`, `js/main.js`
- **What:**
  - CSS: appended a CLASSIC MOBILE MENU REDESIGN block (after the static override). Inside `@media (max-width: 768px)`, replaces the full-screen overlay with a 240px-wide right-anchored dropdown card under the nav: white card, body-font links, hairline row dividers, soft drop shadow.
  - JS: rewrote the hamburger handler in `initNav()`:
    - dropped `document.body.style.overflow` lock (compact menu doesn't need it)
    - close on click-outside (event-bubbling-aware via stopPropagation on hamburger)
    - close on Escape key
    - kept close-on-link-click
- **Why:** Review feedback — old full-screen overlay with large centered serif links was disorienting. Static site needs an unobtrusive native-feeling dropdown.
- **How to revert:** `git revert aea58ef`. Or delete the CLASSIC MOBILE MENU REDESIGN block in CSS and restore the original `if (hamburger && mobileMenu)` body in JS.

### Commit `41f83ae` — Redesign premium mobile menu (320px liquid-glass)
- **Date:** 2026-05-01
- **Type:** feat
- **Files touched:** `demo-bakeri-premium/css/style.css`, `js/main.js`
- **What:**
  - CSS: appended a PREMIUM MOBILE MENU block at end-of-file. Inside `@media (max-width: 768px)`:
    - 320px-wide (capped at 86vw), full-height under the 72px nav, anchored right
    - background `rgba(251,246,236,0.55)` with `backdrop-filter: blur(24px) saturate(160%)` — liquid-glass tint
    - 1px warm-gold (rgba(225,190,130,0.35)) left border, `-12px 0 40px` brown shadow
    - slides in with `transform: translateX(100%) → translateX(0)`, `cubic-bezier(0.32, 0.72, 0.16, 1)` 0.42s spring
    - body-font links 1.05rem with soft warm-gold row dividers
    - lang-toggle (still present in premium) restyled to match glass aesthetic
  - JS: rewrote hamburger handler symmetrically with classic — close on outside-click / Escape / link-click, no body scroll lock. Lang-toggle clicks do not close the panel by design.
- **Why:** Spec — premium mobile menu must be 320px liquid-glass, transparent, partial-viewport. Visually distinct from classic.
- **How to revert:** `git revert 41f83ae`. Or delete the PREMIUM MOBILE MENU block in CSS and restore the original `if (hamburger && mobileMenu)` body in JS.

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
- **i18n:** NO/EN toggle **disabled** (Norwegian-only baseline) — strings preserved in JS for future upsell
- **Animations:** **disabled** — fully static. CSS override block at end of style.css nullifies all transitions, hover transforms, fade-up reveals, page fade
- **Mobile menu:** 240px right-anchored dropdown card, body-font links, drop shadow. No transform animation
- **Visitor badge:** **commented out** (premium-only feature)
- **Vipps modal trigger:** "Forhåndsbestill" buttons on each menu item — still works (modal show/hide is dialog interaction, not page animation)

### `demo-bakeri-premium/`
- Same 5 HTML pages + 3 signature features layered into `index.html`
- **i18n:** NO/EN toggle **active** (premium feature)
- **Animations:** classic baseline (kept) + croissant bobs, counter count-up, timeline ring-pulse + progress fill, ambient hero video
- **Mobile menu:** 320px liquid-glass side panel anchored right, slides in with cubic-bezier spring, backdrop-filter blur, warm-gold accents
- **Visitor badge:** rendered in footer (premium feature)
- **Hero video:** `assets/videos/hero-bread.mp4` (loaded with JPG poster fallback)
