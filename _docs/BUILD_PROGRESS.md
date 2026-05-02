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

### Commit `8652231` — Dedupe menu + gallery images (review round 2)
- **Date:** 2026-05-01
- **Type:** fix(content)
- **Files touched:** `demo-bakeri-classic/{menu,gallery}.html`, `demo-bakeri-premium/{menu,gallery}.html`, `.claude/Nano-Banana-prompt.md`, plus 60 image deletions (30 per tier)
- **What:**
  - All 18 menu-item `<img>` `src=` attributes in each tier's `menu.html` now reference the 15 unique source filenames directly (no more `menu-sourdough-N.jpg` indirection). 3 wide-banner shots (`banner-menu`, `banner-about`, `banner-gallery`) re-used at most twice each, in non-adjacent items across different sections — no other source appears more than once in the menu.
  - All 12 gallery-item `<img>` `src=` attributes in each tier's `gallery.html` now reference 12 unique source filenames. `cat-cake.jpg` (the strawberry cake) and `hero-bread.jpg` each appear exactly once where they previously appeared twice.
  - Deleted 30 orphan rename-copies from each tier's `assets/images/` (`menu-sourdough-1..6.jpg`, `menu-pastry-1..6.jpg`, `menu-cake-1..6.jpg`, `gallery-1..12.jpg`).
  - `Nano-Banana-prompt.md`: rewrote the "Reuse note" to document the new direct-reference approach.
- **Why:** Review feedback round 2 — same image was used 3-4× across menu items at different prices, and `cat-cake` plus `hero-bread` each appeared twice in the gallery. User asked for variety.
- **How to revert:** `git revert 8652231` restores all `menu-*-N.jpg` and `gallery-N.jpg` references AND re-creates the deleted copies (since the commit recorded their deletion).

### Commit `ccb54bc` — Face-friendly cropping on mobile
- **Date:** 2026-05-01
- **Type:** fix(css)
- **Files touched:** `demo-bakeri-classic/css/style.css`, `demo-bakeri-premium/css/style.css`
- **What:** Both tiers gain a FACE-FRIENDLY CROPPING block at end of style.css:
  - `.menu-item-image`, `.bake-card-image`, `.category-card img` get `object-position: center 30%` so portraits with people (Anna at the ovens, Selma shaping pastries, Anna+Lars at the counter) keep their faces in the safe area when these containers crop with `object-fit: cover`.
  - `@media (max-width: 768px)` `.gallery-item img`: `aspect-ratio: 4/5; object-fit: cover; object-position: center 30%`. Forces a uniform portrait-leaning crop on mobile so the masonry doesn't render extreme-tall items in single column. Desktop layout unchanged.
- **Why:** Review feedback round 2 — faces clipped on the mobile gallery / cards.
- **How to revert:** `git revert ccb54bc` or delete the FACE-FRIENDLY CROPPING block from each style.css.

### Commit `f7bc7a7` — Premium mobile menu shrunk to floating glass card
- **Date:** 2026-05-01
- **Type:** feat(premium)
- **Files touched:** `demo-bakeri-premium/css/style.css`
- **What:** The previous 320px-wide full-height side panel (commit `41f83ae`) was too big. Replaced its rule block with a compact floating glass card:
  - `top: 88px; right: 1rem` — floats 16px below the nav bar, anchored to the right edge with margin
  - `width: 320px; height: auto` — only as tall as its content (~5 rows + lang toggle)
  - `border-radius: 18px` (rounded corners), `1px solid rgba(225,190,130,0.4)` warm-gold border, `0 16px 40px` brown shadow
  - `rgba(251,246,236,0.62)` background with `backdrop-filter: blur(22px) saturate(170%)` (kept glass effect)
  - opens with `transform: translateY(-8px) scale(0.97) → 0/1` and `opacity 0 → 1`, cubic-bezier spring
  - lang-toggle + link styling carried over from previous design
- **Why:** Review feedback round 2 — premium hamburger menu was too big; user requested a small 320px rounded glass card like the classic dropdown but with the glass effect kept.
- **How to revert:** `git revert f7bc7a7` restores the full-height side-panel block.

### Commit `be32404` — Reposition hero/gallery crops + remap 11 wrong menu images
- **Date:** 2026-05-01
- **Type:** fix(images)
- **Files touched:** `demo-bakeri-{classic,premium}/css/style.css`, `demo-bakeri-{classic,premium}/{index,menu,gallery}.html` (8 files). `Nano-Banana-prompt.md` updated (gitignored).
- **What:**
  - CSS (both tiers): `.hero-bg` mobile background-position `50%→65%` so bread subject (right of centre in hero-bread.jpg) no longer clips on narrow screens.
  - CSS premium: `.hero-video` mobile `object-position: 65% 50%` override added.
  - CSS (both tiers): default `object-position` for `.menu-item-image`, `.bake-card-image`, `.category-card img` changed from `center 30%` (face-biased) to `center 50%` (neutral) since most slots now hold food shots, not face photos.
  - CSS (both tiers): added 4 focal-point utility classes (`.focal-lower`, `.focal-low`, `.focal-right`, `.bg-focal-right`) with `!important` to allow per-image overrides without inline styles.
  - HTML index.html (both): `cat-sourdough.jpg` category card → `class="focal-lower"` (55%); `cat-cake.jpg` → `class="focal-low"` (70%).
  - HTML menu.html (both): `cat-sourdough` sour3 → `focal-lower`; `cat-pastry` past2 (mandelcroissant) → `focal-lower`; `cat-cake` cake1 (bløtkake) → `focal-low`. 11 wrong image srcs replaced: sour4→`menu-01-olive-rosemary`, sour5→`menu-02-buckwheat`, sour6→`menu-03-roll`, past3→`menu-04-pain-chocolat`, past4→`menu-05-kanelsnurr`, past6→`menu-06-eplekrans`, cake2→`menu-07-truffle-cake`, cake3→`menu-08-lemon-pistasj`, cake4→`menu-09-bringebær`, cake5→`menu-10-brownie`, cake6→`menu-11-lemon-tart`.
  - HTML gallery.html (both): gallery item 8 (hero-bread.jpg) → `class="focal-right"` (65% horizontal).
  - HTML premium gallery.html: page-banner-bg → `class="bg-focal-right"` to shift banner-gallery.jpg left.
  - Nano-Banana-prompt.md: all 18 existing image headers numbered [01]–[18] + [V01] for video; 11 new prompts [19]–[29] added for the new menu-NN filenames.
- **Why:** Review round 3 — hero image cropped from right on mobile; multiple menu items showed bakery counters / person portraits / mill machinery instead of the food item; category/menu card focal points were top-biased (30%) causing white background and wall to dominate over bread/cake.
- **How to revert:** `git revert be32404`. Nano-Banana-prompt.md changes are manual only (gitignored).

### Commits `8937220` — Wire 11 new food images — menu + gallery
- **Date:** 2026-05-01
- **Type:** feat(content)
- **Files touched:** `demo-bakeri-{classic,premium}/menu.html`, `demo-bakeri-{classic,premium}/gallery.html`, 22 new `.jpeg` image assets
- **What:**
  - Fixed all 11 new menu image extensions from `.jpg` → `.jpeg` (Nano Banana exports JPEG format)
  - Added 11 new gallery items (img13–img23) to both gallery pages with Norwegian captions: Olive & Rosmarin Surdeig, Glutenfri Bokhvete, Pølse-rull, Pain au Chocolat, Kanelsnurr, Eplekrans, Sjokolade-trøffelkake, Sitron-pistasjekake, Bringebær-mandelfirkant, Sjokoladebrownie med havsalt, Sitronpai
  - Committed 22 new `.jpeg` assets to git (11 per tier)
- **Why:** 11 new food photos (menu-01 through menu-11) generated in Nano Banana by Bithun were placed in both asset folders. HTML referenced `.jpg` but files landed as `.jpeg`. Gallery needed the new shots added.
- **How to revert:** `git revert 8937220` removes all 22 image files and reverts the 4 HTML files.

### Commit `a2ca9b9` + `94a4a83` — focal-upper class for Smørcroissant
- **Date:** 2026-05-01
- **Type:** fix(crop)
- **Files touched:** `demo-bakeri-{classic,premium}/css/style.css`, `demo-bakeri-{classic,premium}/menu.html`
- **What:**
  - Added `.focal-upper { object-position: center 10% !important; }` utility class to both stylesheets
  - Applied `class="focal-upper"` to `today-croissant.jpg` in both menu.html files
  - Initial value was `center 25%` (commit `a2ca9b9`), tightened to `center 10%` after screenshot review showed the back croissant crust still clipping (commit `94a4a83`)
- **Why:** Review round 3 — Smørcroissant image cropped from the top on mobile; back croissant's crust tips were cut off.
- **How to revert:** `git revert 94a4a83 a2ca9b9` (in that order) or delete `.focal-upper` from both stylesheets and the class from both menu files.

### Commit `d22c853` — Premium gallery grayscale-to-color + floating cards
- **Date:** 2026-05-01
- **Type:** feat(premium)
- **Files touched:** `demo-bakeri-premium/css/style.css`, `demo-bakeri-premium/js/main.js`
- **What:**
  - **Gallery (premium only):** All `.gallery-item img` now default to `filter: grayscale(100%)`. On hover/touch they reveal full colour with `filter: grayscale(0%)` + `transform: scale(1.04)`. Touch support via `.active` class toggled by `touchstart` in `initGallery()`. `::after` overlay changes from a dark tint to a `2px solid var(--color-accent-light)` border highlight on hover/active.
  - **Floating cards (premium only):** `@keyframes card-float` uses pure `translateY` only (no `rotate` — avoids Paint repaints, confirmed by IDE linter hints). `.bake-card:nth-child(1/2/3)` animate at 4.2s / 5.1s / 3.8s with 0.9s / 1.0s / 1.1s positive delays (delays cover the 0.6s reveal transition). `.categories-grid .category-card:nth-child(1/2/3)` animate at 4.7s / 5.5s / 4.1s with 1.2s / 1.4s / 1.6s delays. Staggered durations + start phases prevent cards syncing.
  - Removed `transform: translateY(-4px)` from `.bake-card:hover` and `.category-card:hover` (animation owns `transform`; kept `box-shadow` lift on hover instead).
  - Added `@media (prefers-reduced-motion: reduce) { .bake-card, .category-card { animation: none; } }` fallback.
- **Why:** User request — inspired by fiskeier-demo premium About section (grayscale → color on hover). Floating cards requested as "random alive feel" for the Dagens bakst and Vårt utvalg sections.
- **Gotcha fixed:** IDE linter warned that `rotate()` inside `@keyframes` triggers Paint (not just Composite). Stripped `rotate()` from all keyframe steps — pure `translateY` is GPU-composited only.
- **How to revert:** `git revert d22c853`. Restores the old gallery saturate hover, old card hover transform, and removes float animation.

### Commit `c2fd7c8` — Review-round-4: parity, polish, animation pause-on-hover
- **Date:** 2026-05-01
- **Type:** feat
- **Files touched:** 16 files across both tiers (HTML, CSS, JS, favicon, .gitignore)
- **What (CLASSIC):**
  - Restructured gallery into the same 3 explicit `.gallery-col` wrappers as premium (8 / 7 / 8 split, items distributed by `nth-child` modulo 3 for visual variety). `display: flex` replaces `columns: 3` multi-column.
  - Added comprehensive `:hover`-killer block at end of CSS — flips menu cards, FAQ buttons, nav links, footer links, buttons, mobile-menu rows, lang-toggle, modal close, and image-scale hovers all back to base appearance. The earlier `* { transition: none }` rule killed *animation* of hover changes; this block kills the *end-state* changes too.
  - About photo grid: `grid-template-rows: 230px / 230px / 540px`, default `object-position: center 28%` to keep faces in frame, storefront tile uses `object-fit: contain` so the whole 16:9 building shows (slight letterboxing absorbs the source ratio mismatch).
  - Added 4th category card on the homepage (Kafé & disk → about-photo-2.jpg). Heading changed to "Alt under samme tak". Grid now 4-col desktop / 2-col tablet / 1-col mobile.
  - Synced inline body copy with the longer premium JS translations: `today1/2/3.text`, `story.p1/p2`, `about.story.p1/p2`, `menu.sour.intro`. Both tiers now read identically in shared sections.
  - Favicon: rounded cream tile (`#FBF6EC`) wraps the wheat glyph so it's legible on dark browser tabs.
  - Gallery banner: `bg-focal-right` class added so framing matches premium.
- **What (PREMIUM):**
  - Fixed `og:image` URL (was pointing at `demo-bakeri-classic.vercel.app`). Added `og:url`.
  - Nav hover: replaced underline with per-link colored text-shadow glow. Tones: `#B8842B` (Home) / `#C8704F` (Meny) / `#D4A24A` (Om oss) / `#A8454F` (Galleri) — all warm bakery palette, no cool outliers (replaced the original sage-green that read as out-of-place).
  - rameezscripts pause-on-hover pattern for all floating cards (`.bake-card`, `.category-card`, `.menu-grid .menu-item`): `:hover { animation-play-state: paused !important; }` PLUS a back-side amber ring (`box-shadow: 0 0 0 2px ...`, 38px blur). Critical detail: the `:hover` rule must come **after** the `:nth-child` float-shorthand rules in source order, otherwise the shorthand resets `animation-play-state` to `running`.
  - Sourdough timeline: per-icon `step-icon-float` keyframe, 5 different durations (4.0s–5.2s) and delays (0.2s–1.4s) so step icons bob independently from the active-step ring pulse.
  - Mobile menu redesign: 240px wide (was 320px), `rgba(251,246,236,0.40)` background with `blur(30px) saturate(180%)` backdrop filter, inset highlight on top edge for genuine liquid-glass feel. Tighter padding.
  - Gallery: restructured into 3 explicit `.gallery-col` wrappers (8 / 7 / 8 split). Each column animates: col 1 drifts down, col 2 up, col 3 down (`gallery-drift-down/up` keyframes, 9 s, staggered delays). `columns: unset` overrides the original multi-column rule. Mobile collapses to single column with animation off.
  - Added 4th category card (same content as classic — parity).
  - Fixed `'baker hver kveld natt'` typo in `story.p2` (NO) — now `'baker hver natt'`.
  - About photo grid: same restructure as classic; storefront tile uses `object-fit: contain` (no crop).
  - Inline HTML synced with JS translations so SEO crawlers and no-JS users see the rich copy.
- **Why:** Customer-style review round 4 — long list of cosmetic and parity asks: identical menu/text/colors across tiers, classic must read static, premium gets richer interactions, About-page collage redo, gallery balance, glassier mobile menu, rameezscripts-inspired pause-on-hover pattern.
- **Gotcha fixed:** Original menu pause-on-hover wasn't working because the `:nth-child` `animation:` shorthand later in the file was resetting `animation-play-state` back to `running`. Solved by reordering source so `:hover` comes after `:nth-child`, plus `!important` for safety.
- **How to revert:** `git revert c2fd7c8`. Big diff — easier to revert the whole commit than to hand-roll. The classic `:hover`-killer block can be dropped on its own (search for "CLASSIC — KILL ALL :hover STATE CHANGES") to restore classic's old hover-state-without-transitions behaviour.

### Commit `d410981` — Deploy: vercel.json, robots, sitemap, canonical og URLs
- **Date:** 2026-05-01
- **Type:** chore
- **Files touched:** `demo-bakeri-{classic,premium}/{vercel.json,robots.txt,sitemap.xml,index.html}` (8 files)
- **What:**
  - `vercel.json` per tier: `/assets/*` → `Cache-Control: public, max-age=31536000, immutable`; `*.css/*.js` → `max-age=86400, must-revalidate`; security headers on all routes (X-Content-Type-Options, X-Frame-Options SAMEORIGIN, Referrer-Policy strict-origin-when-cross-origin, Permissions-Policy locking camera/mic/geolocation, HSTS 1y includeSubDomains).
  - `robots.txt` per tier: initially `Allow: /` + sitemap pointer (later changed — see next commit).
  - `sitemap.xml` per tier: 4 URLs (later removed — see next commit).
  - `og:image` and new `og:url` on both `index.html` files now use the canonical `*.ibithun.com` custom domain instead of the auto-generated `*.vercel.app` preview URL.
- **Why:** Sites went live on `demo-bakeri-{classic,premium}.ibithun.com`. Vercel doesn't require `vercel.json` for static sites but the custom cache + security headers are deploy hygiene.
- **How to revert:** `git revert d410981`. Falls back to Vercel's default cache + bare HTTP headers.

### Commit `<pending>` — Demo: noindex / nofollow, drop sitemap
- **Date:** 2026-05-01
- **Type:** chore
- **Files touched:** `demo-bakeri-{classic,premium}/{robots.txt,index.html,menu.html,about.html,gallery.html,404.html}` (12 files), removed `sitemap.xml` from both tiers, `README.md`
- **What:**
  - `robots.txt` flipped from `Allow: /` to `Disallow: /` on both tiers — search engines should not crawl demo content.
  - `<meta name="robots" content="noindex, nofollow" />` added to every HTML page on both tiers (5 × 2 = 10 pages).
  - Deleted `sitemap.xml` from both tiers (no point listing pages we're telling crawlers to ignore).
  - README updated: `*.vercel.app` URLs replaced with `*.ibithun.com`; added a note about the noindex stance and Vercel deploy hygiene (cache headers, security headers, analytics).
- **Why:** User point — demo content shouldn't compete with the real client site in search results, and shouldn't appear in Google when prospects search the customer's actual brand later. Belt-and-suspenders: meta-robots + robots.txt.
- **How to revert:** `git revert <sha>`. Restores indexability and re-creates the sitemaps.

### Commit `<pending>` — Move build log out of .claude/, fully untrack that dir
- **Date:** 2026-05-01
- **Type:** chore
- **Files touched:** `.claude/BUILD_PROGRESS.md` → `_docs/BUILD_PROGRESS.md` (rename), `.gitignore`
- **What:**
  - Moved this file from `.claude/BUILD_PROGRESS.md` to `_docs/BUILD_PROGRESS.md`. Git rename detection preserves history.
  - `.gitignore` simplified: the previous `.claude/*` plus `!.claude/BUILD_PROGRESS.md` exception was replaced with a flat `.claude/` so the entire directory is ignored.
- **Why:** Bithun's global rule — codebase must not visibly expose AI tool names. `.claude/` showing in the GitHub file tree violated that. Build log itself is fine to keep (no AI mentions inside), just at a neutral path.
- **How to apply going forward:** new commit log entries continue in `_docs/BUILD_PROGRESS.md`. Anything an AI tool wants to leave behind locally goes in `.claude/` and stays gitignored.
- **How to revert:** `git revert <sha>` and `git mv _docs/BUILD_PROGRESS.md .claude/BUILD_PROGRESS.md` plus restore the `.gitignore` exception.

### Commit `130d76c` — Pre-skill parity cleanup
- **Date:** 2026-05-02
- **Type:** chore
- **Files touched:** `.gitignore`, `README.md`, `demo-bakeri-{classic,premium}/{index,menu,about,gallery}.html`, `demo-bakeri-{classic,premium}/js/main.js`
- **What:**
  - `.gitignore`: added `_public/` (Nano Banana staging dir, family-wide convention) and `Thumbs.db`.
  - `README.md`: corrected design-tokens table to match actual `:root` (background `#FBF6EC`, both tiers identical palette); dropped false claim that classic carries `videos/` for parity (it does not — premium-only); rephrased the Photography note to drop the explicit "AI-generated" wording per global no-AI-attribution rule.
  - Modal CTA email consolidated from `post@bakeridemo.no` → `post@mollerbakeri.no` across 6 HTML pages and 4 translations entries (NO + EN per tier). One email per customer simplifies fork. Demo-CTA inbox idea moves to a footer link instead.
  - Footer tagline normalized: `menu.html`, `about.html`, `gallery.html` in BOTH tiers were carrying the truncated form (`Surdeigsbrød og bakverk siden 1962.`); now match `index.html`'s full form (`...siden 1962. Bakt for hånd, daglig.`). JS i18n already overrides on premium so only the static fallback drifted.
  - Open Graph + Twitter Card meta block added to `menu.html`, `about.html`, `gallery.html` in both tiers (6 pages — see follow-up commit for the 2 × 404). Each page picks its own banner image so a direct share renders the page-specific preview, not the home hero.
- **Why:** pre-skill parity audit (2026-05-02) flagged these as accidental drift before the prototype gets productised as `bakeri-skill`. Fixing them in the prototype means the per-customer fork procedure inherits the correct shape, instead of every fork having to re-do the same fixes.
- **How to revert:** `git revert 130d76c`. Each change is also reversible by hand from the diff stat.

### Commit `340084a` — Add OG meta to 404 pages for parity
- **Date:** 2026-05-02
- **Type:** chore
- **Files touched:** `demo-bakeri-{classic,premium}/404.html`
- **What:** Added the same OG/Twitter Card block to both 404 pages. `og:image` falls back to `hero-bread.jpg` (no dedicated 404 banner); `og:url` points at the page's own canonical URL.
- **Why:** completeness — every HTML page now carries OG meta. Even with `noindex/nofollow`, a direct share of a 404 URL should preview cleanly rather than render empty.
- **How to revert:** `git revert 340084a`.

### Commit `054145f` — Rename docs/ → _docs/ for family-wide underscore-prefix convention
- **Date:** 2026-05-02
- **Type:** chore
- **Files touched:** `docs/BUILD_PROGRESS.md` → `_docs/BUILD_PROGRESS.md` (git mv with rename detection at 98%); 4 self-references inside the file updated; CLAUDE.md (gitignored) + bakeri-skill SKILL.md + 5 project memory files also updated to point at the new path
- **What:** `git mv docs _docs` to align with Bithun's family-wide pattern: every prototype folder name starts with `_` except the deployable `demo-*` subfolders. Rename detection preserved git history of the file. All references in Bakery's own files + the productised skill + the project memory updated in lockstep.
- **Why:** see "Family-wide build-log convention discussion" at the bottom of this log for the full context. Bakery was the only prototype using `docs/` (no underscore); other prototypes' agent-internal artifacts already lived in `_docs/`. Bringing bakery in line + fixing the family means every prototype has a consistent `_docs/BUILD_PROGRESS.md` location going forward.
- **How to revert:** `git revert 054145f` or `git mv _docs docs` plus reverse the reference updates.

### Commit `ab2852b` — Gitignore _docs/* with BUILD_PROGRESS.md exception
- **Date:** 2026-05-02
- **Type:** chore
- **Files touched:** `.gitignore`
- **What:** Added a 4-line block:
  ```gitignore
  # Agent-internal artifacts (lead research, prospect lists, search prompts, decisions)
  # Exception: BUILD_PROGRESS.md is the project's tracked change log — keep visible on GitHub
  _docs/*
  !_docs/BUILD_PROGRESS.md
  ```
  Verified with `git check-ignore -v _docs/BUILD_PROGRESS.md _docs/sample.pdf` — build log NOT ignored (exception fires); other files ignored.
- **Why:** Bakery doesn't currently have lead-research files in `_docs/` (it's the architectural reference, not a lead-gen target), but the same gitignore rule needs to live in every prototype going forward so future agents inherit the pattern uniformly. Adding it here means a future agent who copies bakery's `.gitignore` as a template gets the rule automatically.
- **How to revert:** `git revert ab2852b` or remove the 4 lines.

---

## Family-wide build-log convention discussion (2026-05-02)

> This entry captures cross-project work done from inside this Bakery session because the discussion + decision originated from auditing Bakery's own setup. The actual changes in other prototype repos are committed in those repos with their own commit logs; this entry is the cross-reference so a future Bakery agent reading top-to-bottom understands the full picture and can trace decisions back to the conversation that produced them.

### The mismatch Bithun surfaced

After the bakery prototype was productised as `bakeri-skill` and Bithun went looking at the build-log file, three inconsistencies surfaced across the family of already-shipped prototypes:

1. **Mismatched location.** Bakery's build log lived at `docs/BUILD_PROGRESS.md` (no underscore prefix). Begravelsesbyrå's lived at `.claude/BUILD_PROGRESS.md` (gitignored, local-only — invisible on GitHub, lost on laptop death). Fiskeier and interiørdesigner had no build log at all in obvious places. Bithun went looking in `.claude/` first because that's where begravelsesbyrå's was — and found nothing in bakery there.
2. **Mismatched naming.** Other prototypes use `_docs/` (with underscore prefix) for agent-internal artifacts (lead research, prospect PDFs, search prompts). Bakery's `docs/` was the odd one out and broke Bithun's "every folder starts with `_` except `demo-*`" pattern.
3. **Stale documentation.** The Architecture Decision Guide §"Step 4" still mandated `.claude/build-progress.md` (lowercase, gitignored) — the OLD rule that bakery had reversed in its own commit `a233cc8` (because `.claude/` showing in the GitHub file tree violates Bithun's no-AI-tool-names rule). The KICKOFF PROMPTS Rule 3 had been updated today (2026-05-02) but the Decision Guide hadn't.

### Decision agreed with Bithun

After surfacing the three issues + showing the per-prototype audit table, Bithun decided:

1. **One canonical location:** every prototype keeps a single `_docs/BUILD_PROGRESS.md` (uppercase, TRACKED in git). No `.claude/` build logs anywhere.
2. **`_docs/*` gitignored with one exception** — `_docs/* + !_docs/BUILD_PROGRESS.md` — so agent-internal artifacts (lead research, prospect lists, search prompts, internal decisions) stay off GitHub while the build log itself ships with the repo and is recoverable from any clone. Mirrors the original `.claude/* + !.claude/BUILD_PROGRESS.md` exception bakery had at the very start (commit `19caacc`), now applied to `_docs/`.
3. **Backfill stubs** for the prototypes that never had a build log (fiskeier, interiørdesigner) so future agents looking for change history land in the same place every time.
4. **Migrate** the gitignored build logs that DO exist (begravelsesbyrå `.claude/BUILD_PROGRESS.md`, interiørdesigner `.claude/plan/build-progress.md`) into `_docs/` so they become tracked + recoverable.
5. **Delete duplicates** ruthlessly — exactly one `BUILD_PROGRESS.md` per prototype, only at `_docs/`. After Bithun caught a duplicate-stub mistake on interiørdesigner (the agent created a stub at `_docs/` without first noticing the real 91-line build log already at `.claude/plan/`), the rule became: always filesystem-search for any change-log style file anywhere in the prototype before creating a new one.
6. **Update both source-of-truth documents** so the next agent doesn't re-encounter the inconsistency: ARCHITECTURE-DECISION-GUIDE.md §"Step 4" + BUSINESS-KICKOFF-PROMPTS.md Rule 3.

### Changes applied in OTHER projects (not Bakery)

| Project | What changed | Commit | Pushed to |
|---|---|---|---|
| **begravelsesbyrå** | Migrated `.claude/BUILD_PROGRESS.md` (272 lines) → `_docs/BUILD_PROGRESS.md`. Added `_docs/* + !_docs/BUILD_PROGRESS.md` to `.gitignore`. Updated header to reflect new location. Deleted stale `.claude/` copy after migration. | `1584a67` (migration) + `f07b99d` (gitignore) | `goldenbutter/minnero.begravelsesbyra` `main` |
| **fiskeier** | Created `_docs/BUILD_PROGRESS.md` stub (no prior build log existed; git log is authoritative source for pre-stub history). Added `_docs/* + !_docs/BUILD_PROGRESS.md` to `.gitignore` plus `_public/` for Nano Banana staging consistency. | `081557a` | `goldenbutter/orklaElveFiskeBooking` `dev` |
| **interiørdesigner** | First created a stub at `_docs/` (commit `32bf66f`) — Bithun then caught that a real 91-line build log existed at `.claude/plan/build-progress.md` (gitignored). Migrated the full real history into `_docs/BUILD_PROGRESS.md` (commit `d596909`), preserving status table + 4 build sessions of recent activity + repo-state-at-handoff. Added a session-5 entry documenting today's family-wide migration. Deleted `.claude/plan/build-progress.md` after migration. Same `_docs/*` gitignore exception added. | `32bf66f` (stub + gitignore) + `d596909` (real history migration) | `goldenbutter/interior-atelier` `master` |
| **restaurant** | Edited `.gitignore`: changed `_docs/` (flat ignore) → `_docs/* + !_docs/BUILD_PROGRESS.md` so a future build log lands tracked. **NOT committed** — restaurant has in-flight `style.css` work in both tiers + a `.vscode` line already staged; left for Bithun to bundle. | (uncommitted) | (n/a) |
| **`_docs/_skill/ARCHITECTURE-DECISION-GUIDE.md`** | §"Step 4" updated: replaced stale `.claude/build-progress.md` (lowercase, gitignored) rule with `_docs/BUILD_PROGRESS.md` (uppercase, TRACKED) + a one-line note pointing at bakery commit `a233cc8` as the reversal. | (local edit; not a git repo at this path) | (n/a) |
| **`_docs/_prompt/BUSINESS-KICKOFF-PROMPTS.md`** | Rule 3 expanded: added the verbatim `_docs/* + !_docs/BUILD_PROGRESS.md` gitignore block + the verification command + a note that it's locked across the four shipped prototypes. Also rewrote 10 references from `docs/BUILD_PROGRESS.md` → `_docs/BUILD_PROGRESS.md` across the per-business prompts. | (local edit) | (n/a) |

### Verification

After all changes: `find c:/Project/prototypes/{bakery,begravelsesbyrå,fiskeier,interiørdesigner}-demo -iname "BUILD_PROGRESS*"` returns exactly four hits — one per prototype, all at `_docs/BUILD_PROGRESS.md`. No duplicates, no `.claude/` build logs anywhere.

### Lesson recorded

**Before creating any new file, filesystem-search the whole prototype tree for files with similar names.** The interiørdesigner stub mistake came from listing only `.claude/` top level (which showed `plan/` as a folder) without peeking inside. Per-prototype build logs may sit at `.claude/`, `.claude/plan/`, `_docs/`, `docs/`, `notes/`, or any other agent-internal location depending on which session built the prototype. Always run `find <prototype-root> -iname "BUILD_PROGRESS*" -o -iname "build-progress*" -o -iname "CHANGELOG*"` before adding a new one.

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
- **Live URL:** https://demo-bakeri-classic.ibithun.com (custom domain via Vercel)
- **i18n:** NO/EN toggle **disabled** (Norwegian-only baseline) — strings preserved in JS for future upsell
- **Animations:** **disabled** — fully static. CSS override block + dedicated `:hover`-killer block at end of style.css nullify all transitions, hover transforms, hover colour shifts, hover bg fills, fade-up reveals, page fade. Click interactions (FAQ accordion, lightbox, Vipps modal, mobile hamburger) remain functional
- **Mobile menu:** 240px right-anchored dropdown card, body-font links, drop shadow. No transform animation
- **Visitor badge:** **commented out** (premium-only feature)
- **Vipps modal trigger:** "Forhåndsbestill" buttons on each menu item — still works (modal show/hide is dialog interaction, not page animation)
- **Menu images:** 18 menu items — sour1/2/3 use today-sourdough/hero-bread/cat-sourdough; sour4/5/6 → menu-01-olive-rosemary/menu-02-buckwheat/menu-03-roll (`.jpeg`); past1/2 → today-croissant/cat-pastry; past3/4/5/6 → menu-04-pain-chocolat/menu-05-kanelsnurr/today-cardamom/menu-06-eplekrans (`.jpeg`); cake1 → cat-cake; cake2/3/4/5/6 → menu-07-truffle-cake/menu-08-lemon-pistasj/menu-09-bringebær/menu-10-brownie/menu-11-lemon-tart (`.jpeg`)
- **Categories (homepage):** 4 cards — Surdeigsbrød, Bakverk, Kaker, Kafé & disk. Heading: "Alt under samme tak". Grid: 4-col desktop / 2-col tablet / 1-col mobile
- **Gallery:** 3 explicit `.gallery-col` wrappers (8 / 7 / 8 split, items distributed by `nth-child` modulo 3). Same layout as premium but full colour, no animation
- **About photo grid:** `230px / 230px / 540px` rows, default `object-position: center 28%` to keep faces visible, storefront tile uses `object-fit: contain` to avoid cropping
- **Mobile focal point classes:** `.focal-upper` (10%), `.focal-lower` (55%), `.focal-low` (70%), `.focal-right` (65% x), `.bg-focal-right`; applied per-image in HTML. Default `object-position: center 50%`
- **Deploy:** `vercel.json` with 1y immutable cache for `/assets/*`, 1d for css/js, security headers (HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy)
- **Robots:** `Disallow: /` + `<meta name="robots" content="noindex, nofollow">` on every page (demo-stance, no SEO competition with future real client site)
- **LOCKED** — no further changes planned for classic tier

### `demo-bakeri-premium/`
- Same 5 HTML pages + signature features layered into `index.html`
- **Live URL:** https://demo-bakeri-premium.ibithun.com (custom domain via Vercel)
- **i18n:** NO/EN toggle **active** (premium feature)
- **Animations:** classic baseline (kept) + croissant bobs, counter count-up, timeline ring-pulse + progress fill, per-icon timeline float, ambient hero video, gallery column parallax drift
- **Nav hover:** per-link warm-palette text-shadow glow (no underline). Hjem `#B8842B` → Meny `#C8704F` → Om oss `#D4A24A` → Galleri `#A8454F`
- **Mobile menu:** 240px liquid-glass floating card (`rgba(251,246,236,0.40)` + `blur(30px) saturate(180%)` backdrop filter), inset top-edge highlight, anchored top-right with 0.85rem margin, 80px from top
- **Visitor badge:** rendered in footer (premium feature)
- **Hero video:** `assets/videos/hero-bread.mp4` (loaded with JPG poster fallback)
- **Categories (homepage):** 4 cards — same as classic + float animation. Pause-on-hover pattern (back-side amber ring, neighbours keep bobbing)
- **Pause-on-hover pattern (rameezscripts):** applied to `.bake-card`, `.categories-grid .category-card`, `.menu-grid .menu-item` — hover pauses *that* card's float and adds an amber ring (`box-shadow: 0 0 0 2px rgba(184,132,43,0.50), 0 0 38px rgba(225,190,130,0.50), 0 14px 38px rgba(184,132,43,0.22)`). Critical: `:hover` must source-order *after* the `:nth-child` `animation:` shorthand or the shorthand resets `animation-play-state` to running. Uses `!important` for safety.
- **Menu / mobile-cropping:** same image set + focal-point classes as classic
- **Gallery:** 3 explicit `.gallery-col` wrappers (8 / 7 / 8 split, identical to classic). Default `filter: grayscale(100%)`. Hover/touch reveals full colour + `scale(1.04)` + `var(--color-accent-light)` border highlight on `::after`. Each column drifts independently — col 1 down 9s, col 2 up 9s 0.4s delay, col 3 down 9s 0.8s delay
- **About photo grid:** identical to classic (`230 / 230 / 540`, `object-fit: contain` on storefront)
- **Floating cards:** `.bake-card` (Dagens bakst) — 3 cards, durations 4.2/5.1/3.8s with 0.9/1.0/1.1s delays. `.categories-grid .category-card` (Vårt utvalg) — 4 cards, 4.7/5.5/4.1/5.0s with 1.2/1.4/1.6/1.8s delays. `.menu-grid .menu-item` — 18 cards on a 6-pattern `nth-child(6n+1..6)` cycle, 4.1–5.7s × 0.8–1.8s delays. Pure `translateY`, GPU-composite only. `prefers-reduced-motion` fallback included
- **Deploy:** identical `vercel.json` config to classic
- **Robots:** identical noindex/nofollow stance — demo content excluded from search
- **LOCKED** — no further changes planned for premium tier
