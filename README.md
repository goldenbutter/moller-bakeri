# Møller Bakeri — Demo Site

A two-tier static website prototype for a fictional Norwegian family bakery in Bergen. Built to demonstrate what a real bakery client site can look like at two price points.

**Live demos:**
- Classic → [demo-bakeri-classic.vercel.app](https://demo-bakeri-classic.vercel.app)
- Premium → [demo-bakeri-premium.vercel.app](https://demo-bakeri-premium.vercel.app)

---

## Tiers

### Classic (~9 999 NOK)
A clean, fast marketing site with no frills and no framework overhead.

- 5 pages: Home, Menu, About, Gallery, 404
- Responsive at 768 px and 480 px breakpoints
- Mobile hamburger menu (dropdown card)
- Lightbox gallery with keyboard navigation
- Vipps pre-order modal (demo-safe locked phrase)
- Vercel Web Analytics on every page
- Norwegian only (EN toggle ready to unlock as a paid upgrade)

### Premium (~17 000–19 000 NOK)
Same baseline plus three signature features that justify the price gap.

- **Live bake counter** — animated count-up, time-of-day aware (bread/croissants/cakes)
- **Sourdough timeline** — 5-step interactive timeline that highlights the current production stage based on the hour
- **Animated croissant-icon dividers** — bespoke SVG section breaks that replace the classic flour-dividers
- **Video hero** — MP4 with JPG poster fallback; graceful degradation when video is absent
- **NO/EN language toggle** — active and visible (premium feature)
- **Floating product cards** — Dagens bakst and Vårt utvalg cards bob continuously with staggered keyframe timing (pure `translateY`, GPU-composited)
- **Gallery grayscale → colour** — all gallery images default to black & white; full colour reveals on hover or touch with a warm-gold border accent
- **Visitor-badge view counter** in footer (accent-colour matched)
- Liquid-glass mobile menu (backdrop-filter blur, rounded floating card)

---

## Stack

Vanilla HTML · CSS (custom properties, no framework) · JavaScript (no dependencies)

- No `npm install`. No build step. Open `index.html` in a browser.
- Single `css/style.css` + single `js/main.js` per tier
- Deployed on Vercel (static, zero config)

---

## Repo layout

```
bakeri-demo/
├── demo-bakeri-classic/     # Classic tier
│   ├── assets/
│   │   ├── images/          # All photos (JPEG/JPG)
│   │   └── videos/          # hero-bread.mp4 (premium only — copied here for parity)
│   ├── css/style.css
│   ├── js/main.js
│   ├── index.html
│   ├── menu.html
│   ├── about.html
│   ├── gallery.html
│   └── 404.html
└── demo-bakeri-premium/     # Premium tier (mirrors classic + extensions)
    └── ...
```

---

## Design tokens

| Token | Classic | Premium |
|---|---|---|
| Primary | `#6B3F1F` (dark brown) | same |
| Accent | `#B8842B` (golden crust) | `#E1BE82` (warm sand) |
| Background | `#FAF7F2` (paper cream) | same |
| Heading font | Fraunces (optical-size serif) | same |
| Body font | DM Sans | same |

---

## Branching / deploy

Each tier is a separate Vercel project pointing at its own subdirectory (`Root Directory` setting). A push to `main` rebuilds both. To avoid redundant rebuilds, each project's **Ignored Build Step** is:

```bash
git diff HEAD^ HEAD --quiet -- demo-bakeri-classic/   # classic project
git diff HEAD^ HEAD --quiet -- demo-bakeri-premium/   # premium project
```

---

## Photography

Placeholder photos are AI-generated (prototype-only). Real customer photos replace all assets at fork time before the site goes live on a custom domain.

---

## Credits

Developed by [Bithun](https://github.com/goldenbutter)
