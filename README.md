# GetMyInvoices Website Header

Animated header composition for the GetMyInvoices website. Visualizes the
portal-to-system invoice flow with three regions:

| Region | Content | Animation |
|---|---|---|
| **Portal box** | Inner DHL logo + 8 portal logos | Logos orbit along the dashed border (60s/cycle), draggable |
| **Sync hub** | Download icon, "IN" slips, two sync bubbles | Static composition |
| **Smartphone** | 4 accounting export logos | Vertical infinite scroll (16s/cycle), wheel/drag, pause on hover |

Available in **four variants** that share the same animation logic:

| Variant | File | Layout | Theme |
|---|---|---|---|
| Desktop Dark | `index.html` | horizontal (824×344) | dark |
| Desktop Light | `index-light.html` | horizontal (824×344) | light |
| Mobile Dark | `index-mobile.html` | portrait (380×700) | dark |
| Mobile Light | `index-mobile-light.html` | portrait (380×700) | light |

Live preview:
- https://wanjadoering.github.io/GMI_Portals_Header_Animation/
- https://wanjadoering.github.io/GMI_Portals_Header_Animation/index-light.html
- https://wanjadoering.github.io/GMI_Portals_Header_Animation/index-mobile.html
- https://wanjadoering.github.io/GMI_Portals_Header_Animation/index-mobile-light.html

---

## File structure

```
Website_Header/
├── README.md                       this file
├── index.html                      Desktop Dark entry point
├── index-light.html                Desktop Light entry point (body.light)
├── index-mobile.html               Mobile Dark entry point
├── index-mobile-light.html         Mobile Light entry point (body.light)
├── style.css                       Desktop styles + theme tokens
├── style-mobile.css                Mobile styles (portrait layout)
├── script.js                       Shared animation loop + drag/wheel/touch handlers
└── assets/
    ├── common/                     used by ALL variants
    │   ├── dhl.png                 center logo on the portal box (★ swap target)
    │   └── *.svg                   8 portal logos: google, facebook, adobe,
    │                               amazon, openai, spotify, vodafone
    ├── dark/                       used by Desktop Dark + Mobile Dark
    │   ├── invoices-en.webp        "IN" invoice slips, English ("IN")
    │   ├── invoices-de.webp        "RE" invoice slips, German ("Rechnung")
    │   ├── return-arrow.webp       sync bubble icon (dark version)
    │   ├── datev.webp              phone-scroll: DATEV (dark variant)
    │   ├── lexware-office.webp     phone-scroll: Lexware Office
    │   ├── addison.webp            phone-scroll: Addison
    │   └── fastbill.webp           phone-scroll: FastBill
    └── light/                      used by Desktop Light + Mobile Light
        ├── invoices-en.webp        "IN" slips, English (soft grey)
        ├── invoices-de.webp        "RE" slips, German (soft grey)
        ├── return-arrow.webp       sync bubble (cyan-filled circle)
        ├── arrow.svg               polished arrow used between regions
        ├── datev.webp              phone-scroll: DATEV (light variant)
        ├── lexware-office.webp     phone-scroll: Lexware Office (black for white BG)
        ├── addison.webp            phone-scroll: Addison (black)
        └── fastbill.webp           phone-scroll: FastBill (black)
```

**Note on filenames:** files within `dark/` and `light/` use the same names.
The variant is selected by the folder path, keeping the HTML readable.

---

## Quick start

### Open locally

```bash
open index.html               # Desktop Dark
open index-light.html         # Desktop Light
open index-mobile.html        # Mobile Dark
open index-mobile-light.html  # Mobile Light
```

No build step required — pure HTML/CSS/JS/SVG/WebP, runs in any modern browser.

### Embed in Axure RP

1. Add an **Inline Frame** widget (Default library → Inline Frame).
2. Size: **824 × 344 px** for desktop, **380 × 700 px** for mobile.
3. Double-click the frame → **Frame Target** → **Link to URL** → paste a GitHub Pages URL above.
4. **Show scrollbars: Never**.

---

## ★ Dynamic swap targets

The HTML has two places where assets are intended to change per page. Both
are marked with a `★ … SWAP TARGET ★` comment so they're easy to find.

### 1. Portal logo (per portal subpage)

The DHL logo in the centre of the portal box is the **portal slot**: on the
Amazon subpage it should show Amazon, on the Google subpage Google, etc.
The other 8 orbiting logos stay constant.

Marker comment: `★ DYNAMIC SWAP TARGET ★` near the `<image href="assets/common/dhl.png" …>` line in all four index files.

**Hugo example:**
```html
<image href='{{ .Params.portalLogo | default "assets/common/dhl.png" }}' ... />
```

```yaml
# content/portals/amazon/index.md
portalLogo: /assets/common/amazon.svg

# content/portals/google/index.md
portalLogo: /assets/common/google.svg
```

**React / Lovable example:**
```jsx
<image href={portalLogo || "/assets/common/dhl.png"} ... />
```

For a global swap (one logo for all pages), just replace the file at
`assets/common/dhl.png` — no code change needed.

### 2. Invoice slips language (per page language)

The "IN" letters on the invoice slips reflect the page language:
- **English page** → `assets/dark/invoices-en.webp` (or `light/`)
- **German page** → `assets/dark/invoices-de.webp` (or `light/`)

Marker comment: `★ LANGUAGE SWAP TARGET ★` near the `<image href="…/invoices-en.webp" …>` line.

**Hugo example** (uses the active language code from Hugo's i18n):
```html
<image href='{{ printf "assets/dark/invoices-%s.webp" .Site.Language.Lang }}' ... />
```

**React / Lovable example:**
```jsx
<image href={`/assets/dark/invoices-${lang}.webp`} ... />
```

Add more languages later by dropping additional files like
`invoices-fr.webp`, `invoices-es.webp` into the dark/ and light/ folders —
the templating expression resolves them automatically.

---

## Customization guide

### Swap a portal logo (orbit)

The 8 orbiting logos live in `/assets/common/`. The fastest way to change one:

**Option A — replace the file** (works for all four variants at once):
```bash
cp my-new-google.svg assets/common/google.svg
```

**Option B — change the path** (point to a different file):
```html
<div class="logo-anchor" style="--start-frac: 0.05;">
  <div class="logo-box"><img src="assets/common/google.svg" alt="Google"></div>
</div>
```

Supported formats: SVG, PNG, JPG, WEBP, GIF.

### Swap a phone-scroll logo

`assets/dark/` holds the variants visible on the dark phone background,
`assets/light/` the variants for the white phone.

When **adding** or **removing** a logo, edit both the original set AND the
duplicate set inside `.phone-scroll` (they must stay in sync for the seamless loop):

```html
<div class="phone-scroll">
  <img src="assets/dark/lexware-office.webp" alt="Lexware Office">
  <img src="assets/dark/datev.webp"          alt="DATEV">
  <!-- … -->
  <!-- duplicate set -->
  <img src="assets/dark/lexware-office.webp" alt="" aria-hidden="true">
  <img src="assets/dark/datev.webp"          alt="" aria-hidden="true">
  <!-- … -->
</div>
```

### Animation speeds

Edit the `:root` block in `style.css` (or `style-mobile.css`):

```css
:root {
  --orbit-duration:  60;    /* seconds per orbit cycle (logos around the box) */
  --scroll-duration: 16;    /* seconds per scroll cycle (phone)               */
  --glow-color:      rgba(68, 164, 220, 0.9);   /* logo-box halo color        */
}
```

These values are read by `script.js` at startup.

---

## How the four variants differ

| Element | Desktop Dark / Mobile Dark | Desktop Light / Mobile Light |
|---|---|---|
| `<body>` class | (none) | `light` |
| Page background | `transparent` (overlays parent page) | `transparent` (overlays parent page) |
| Smartphone fill | `#060608` | `#ffffff` |
| Center circle fill | dark gradient | `#ffffff` |
| Portal box fill | dark gradient | `none` (transparent) |
| All strokes | `#3c6e9d` (dark blue) | `#dde1ea` (soft grey-blue) |
| Box-to-box arrows | inline white SVG paths | external `assets/light/arrow.svg` |
| Invoice slips | `assets/dark/invoices-{lang}.webp` | `assets/light/invoices-{lang}.webp` |
| Sync bubbles | `assets/dark/return-arrow.webp` | `assets/light/return-arrow.webp` |
| Phone logos | `assets/dark/*.webp` (colored) | `assets/light/*.webp` (black) |
| 8 portal logos | `assets/common/*.svg` | `assets/common/*.svg` |
| Portal centre logo | `assets/common/dhl.png` | `assets/common/dhl.png` |

| Element | Desktop (824×344) | Mobile (380×700) |
|---|---|---|
| Layout direction | horizontal (Portal → Centre → Phone) | vertical stack |
| Return-arrow bubble placement | left/right of centre circle | above/below centre circle |
| Between-region arrows | point right (→) | point down (↓), original arrow rotated 90° + scaled 0.7 |
| Stylesheet | `style.css` | `style-mobile.css` |

---

## Developer integration notes (Hugo / Lovable / React)

The code is framework-agnostic. To migrate into a build system:

- **Hugo:** Drop the folder into `static/header/` or wrap it as a partial.
  The 8 portal logos can be moved to `data/portals.yaml` and the markup
  generated with a `range` block. Pick the desktop/mobile variant via a
  media query in the surrounding page, not by switching the file.
- **Lovable / React:** Convert each region (Portal box, Centre, Phone) into
  its own component. Animation logic in `script.js` can be ported into a
  React hook or remain as a side-effect on mount.
- **Asset bundling:** Tools like Vite/Webpack will handle webp imports
  automatically as long as the directory layout is preserved.

### Why WebP?

Phone-scroll logos, invoice slips, sync bubbles and the DATEV asset were
exported as WebP for a smaller payload than PNG/SVG without quality loss.
WebP is supported in all modern browsers (Chrome 32+, Firefox 65+,
Safari 14+, Edge 18+) — no fallback needed for our target audience.

### Browser features used

- **CSS `offset-path`** — orbital logo motion (Chrome 55+, Firefox 72+, Safari 16+)
- **CSS `mask-image`** — top/bottom fade on the phone scroll (Chrome 120+, Safari 16+)
- **SVG filters** (`feGaussianBlur` + `feFlood`) — cyan glows
- **`requestAnimationFrame`** — JS animation loop (universal)

All stable in modern browsers (2024+). No polyfills required.

### Accessibility notes

- The SVG composition is marked `aria-hidden="true"` — purely decorative.
- Portal and phone-scroll `<img>` tags have meaningful `alt` text on originals;
  duplicate set images use `alt=""` + `aria-hidden="true"`.
- Drag interactions use `cursor: grab` / `grabbing` for visual feedback.

---

## Maintenance checklist

When updating the header:

- [ ] Test all four variants after structural changes
- [ ] If a logo changes: prefer file-replace in `/assets/common/` so all variants update at once
- [ ] When editing `.phone-scroll`: keep the duplicate set in sync with the original set
- [ ] When adding a new language variant: add `invoices-{lang}.webp` to BOTH `assets/dark/` and `assets/light/`
- [ ] When adding new theme-switching tokens: declare in `:root` AND override in `body.light`
- [ ] After GitHub Pages deploys, hard-refresh (Cmd+Shift+R) to bypass CDN cache
