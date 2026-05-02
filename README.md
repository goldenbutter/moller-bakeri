# Møller Bakeri — Demo Site

A two-tier static website prototype for a fictional Norwegian family bakery in Bergen. Built to demonstrate what a real bakery client site can look like at two price points.

**Live demos:**
- Classic → [demo-bakeri-classic.ibithun.com](https://demo-bakeri-classic.ibithun.com)
- Premium → [demo-bakeri-premium.ibithun.com](https://demo-bakeri-premium.ibithun.com)

> Both sites ship with `<meta name="robots" content="noindex, nofollow">` and a `Disallow: /` `robots.txt`. Demo content should not compete in search results with the eventual real client site.

---

## Tiers

### Classic
A clean, fast marketing site with no frills and no framework overhead.

- 5 pages: Home, Menu, About, Gallery, 404
- Responsive at 768 px and 480 px breakpoints
- Mobile hamburger menu (dropdown card)
- Lightbox gallery with keyboard navigation
- Vipps pre-order modal (demo-safe locked phrase)
- Vercel Web Analytics on every page
- Norwegian only (EN toggle ready to unlock as a paid upgrade)

### Premium
Same baseline plus three signature features.

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
- Deployed on Vercel — each tier has a `vercel.json` with 1-year immutable cache for `/assets/*`, 1-day cache for css/js, and security headers (HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy)
- Vercel Web Analytics enabled on both projects

---

## Repo layout

```
bakeri-demo/
├── demo-bakeri-classic/     # Classic tier
│   ├── assets/images/       # All photos (JPEG/JPG)
│   ├── css/style.css
│   ├── js/main.js
│   ├── index.html
│   ├── menu.html
│   ├── about.html
│   ├── gallery.html
│   └── 404.html
└── demo-bakeri-premium/     # Premium tier (classic + signature features)
    ├── assets/
    │   ├── images/          # Same set as classic
    │   └── videos/          # hero-bread.mp4 (premium-only animated hero)
    └── ... (mirrors classic page set)
```

---

## Design tokens

| Token | Both tiers |
|---|---|
| Background | `#FBF6EC` (paper cream) |
| Background alt | `#F4E8D0` (warm sand) |
| Background deep | `#ECDCBC` (deeper sand) |
| Primary | `#B8842B` (golden crust) |
| Primary dark | `#8C5F1F` |
| Accent | `#6B3F1F` (dark chocolate) |
| Accent light | `#E1BE82` (warm sand highlight) |
| Text | `#3A2618` |
| Text light | `#7B5A3F` |
| Heading font | Fraunces (optical-size serif) |
| Body font | DM Sans |

Both tiers share the identical palette and font stack — the price-tier divergence is animations, interactivity, and copy depth, not aesthetic.

---

## Branching / deploy

Each tier is a separate Vercel project pointing at its own subdirectory (`Root Directory` setting). A push to `main` rebuilds both. To avoid redundant rebuilds, each project's **Ignored Build Step** is:

```bash
git diff HEAD^ HEAD --quiet -- demo-bakeri-classic/   # classic project
git diff HEAD^ HEAD --quiet -- demo-bakeri-premium/   # premium project
```

---

## Photography

Placeholder photos are generated for prototype use only. Real customer photos replace every asset at fork time before the site goes live on a custom domain. Norwegian Markedsføringsloven and EU disclosure rules around generated imagery in commercial contexts make real photos the only safe default for production.

---

## Credits

Developed by [Bithun](https://github.com/goldenbutter)
