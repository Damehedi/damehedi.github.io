<div align="center">

# damehedi.github.io

**Personal portfolio of Md Rahmath Ullah Mehedi**
Commercial analysis · turning business questions into data-driven decisions

[**→ View the live site**](https://damehedi.github.io)

[![Pages](https://img.shields.io/badge/GitHub_Pages-live-1E3A5F?style=flat-square)](https://damehedi.github.io)
[![Build](https://img.shields.io/badge/build-none_required-B07C2E?style=flat-square)](#running-it-locally)
[![Dependencies](https://img.shields.io/badge/runtime_dependencies-1-1E3A5F?style=flat-square)](#dependencies)

</div>

---

## What this is

A single-page portfolio site, built from scratch, with no framework and no build step. It ships as one `index.html` file plus an `assets/` folder of images. You can open it by double-clicking the file.

That constraint was deliberate. A portfolio that needs `npm install` to render is a portfolio that breaks in two years. This one will still work when I have forgotten how it was made.

**Live at → [damehedi.github.io](https://damehedi.github.io)**

---

## Table of contents

- [Features](#features)
- [Repository structure](#repository-structure)
- [Architecture](#architecture)
- [Running it locally](#running-it-locally)
- [Making changes](#making-changes)
- [Adding images](#adding-images)
- [Design system](#design-system)
- [Dependencies](#dependencies)
- [Browser support](#browser-support)
- [Deployment](#deployment)
- [Contact](#contact)

---

## Features

| | |
|---|---|
| **Tabbed SPA routing** | Seven sections swap in place with hash-based URLs (`#work`, `#projects`). Shareable links, working back button, no page reloads. |
| **WebGL background** | A `three.js` torus knot that drifts, scales and fades per tab, so it never competes with the text it sits behind. |
| **Animated experience cards** | Roles expand on tap. Metrics count up from zero, bullet points cascade in, and an accent bar wipes down the card edge. |
| **Interactive portfolio pie** | An SVG donut of a live investment allocation. Hovering a slice pops it out, dims the rest, and renders the holding's logo and weight in the centre. |
| **Evidence-based skills** | No self-assigned percentages. Each skill states its level, where it was used, and what proves it. |
| **Certificate lightbox** | Awards and certifications open into modals; proof images expand full-screen. |
| **Contact with copy-to-clipboard** | Email and phone open a popup with a one-click copy button, plus direct mail, dial and WhatsApp actions. |
| **Fully responsive** | Mobile nav closes on outside tap or `Esc`. Honours `prefers-reduced-motion`. |

---

## Repository structure

```
damehedi.github.io/
├── index.html          # The entire site: markup, styles, and logic
├── assets/             # Images: certificates, awards, event photos
│   └── README.txt      # Which filenames the site expects
└── README.md           # You are here
```

That is the whole thing. One HTML file.

---

## Architecture

Everything lives in `index.html`, in three blocks:

```
<style>    ~330 lines   Design tokens, layout, component styles, animations
<body>     ~280 lines   Semantic markup for each tab
<script>   ~450 lines   Data arrays, renderers, router, WebGL, interactions
```

### Data-driven content

Content is **not** hand-written into the markup. It lives in JavaScript arrays near the top of the script block and is rendered into the DOM:

| Array | Renders |
|---|---|
| `EXPERIENCE` | Experience tab cards |
| `VOLUNTEERING` | Volunteering tab cards |
| `AWARDS` | Recognition tab, plus modal contents |
| `HOLDINGS` | Investment allocation pie and its legend |

Both experience arrays are rendered by the same `renderRoles()` function, so the two tabs stay visually consistent by construction rather than by discipline.

**To update your experience, you edit an array, not HTML.** That is the point.

### Routing

`showTab()` toggles a single `.active` class across `.page` elements, resets scroll, re-triggers reveal animations via `IntersectionObserver`, and repositions the WebGL object. State is mirrored to `location.hash`.

### Progressive enhancement

Every external asset fails gracefully. Company logos come from Clearbit; if a request fails, an `onerror` handler swaps in a coloured tile with the ticker or initials. Missing certificate images hide their own thumbnails. Nothing renders as a broken image icon.

---

## Running it locally

**No build step. No dependencies to install.**

```bash
git clone https://github.com/damehedi/damehedi.github.io.git
cd damehedi.github.io
open index.html          # macOS
# start index.html       # Windows
# xdg-open index.html    # Linux
```

If you would rather serve it over HTTP (recommended, so that fonts and CDN assets load exactly as they do in production):

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

---

## Making changes

### Add or edit a job

Find the `EXPERIENCE` array in the `<script>` block and add an object:

```js
{
  meta: "Jan 2025 – Present",           // Date range
  role: "Job Title",
  org:  "Company Name",
  badge: "Optional pill",               // e.g. "12-month placement"
  logo: "company.com",                  // Domain, for the Clearbit logo
  fallback: "CN",                       // Initials, if the logo 404s
  points: [
    "Bullet with <b>bold</b> allowed.",
    "Another bullet."
  ],
  stats: [                              // Optional: these count up on expand
    { v: 40, suf: "%", lbl: "Waste reduced" },
    { v: 15, pre: "10–15", lbl: "Staff supervised" }
  ],
  proof: [                              // Optional: click to enlarge
    { src: "assets/photo.jpg", label: "Caption" }
  ]
}
```

**On `stats`:** use `v` for a number that should animate from zero, and `pre` for a value that should be printed literally (a range like `10–15` cannot be counted up to).

### Add a certification

Append to the `AWARDS` array. `detail` accepts HTML, so you can list modules with `<ul><li>`, and `cert` points at a certificate image that renders inside the modal.

### Change the colours

Every colour is a CSS custom property in `:root`. Change it once, it changes everywhere. See [Design system](#design-system).

---

## Adding images

Drop files into `assets/` using the **exact** filenames the site expects. `assets/README.txt` lists them all. The site looks for, among others:

```
assets/ife-2026.jpg                   IFE London stand photo
assets/employee-of-the-year-2024.jpg  Award
assets/treehouse-listening-team.jpg   Nomination
assets/forage-bcg.jpg                 BCG certificate
assets/powerbi-cert.jpg               Power BI certificate
```

Any file that is absent simply does not render, so you can add them one at a time. Keep each under roughly 1 MB; phone photos are often 5 MB or more and will make the page crawl.

---

## Design system

All tokens are defined once in `:root`:

```css
--bg:      #F7F5F0    /* Warm bone background          */
--card:    #FFFFFF    /* Card surfaces                 */
--ink:     #1A2233    /* Primary text                  */
--navy:    #1E3A5F    /* Primary accent                */
--gold:    #B07C2E    /* Secondary accent, italics     */
--display: "Plus Jakarta Sans"   /* Headings           */
--sans:    "Inter"               /* Body               */
--mono:    "JetBrains Mono"      /* Labels, data       */
```

The palette is deliberately narrow: two accents, one background, one surface. Earlier iterations used a four-colour gradient and it read as noise.

---

## Dependencies

Exactly one runtime dependency, loaded from a CDN:

| Library | Version | Why |
|---|---|---|
| [three.js](https://threejs.org) | r128 | The WebGL background object |

Plus Google Fonts (Plus Jakarta Sans, Inter, JetBrains Mono) and the Clearbit Logo API for company logos. **No npm. No bundler. No framework.**

If `three.js` fails to load, the site still renders correctly, it just loses the background object.

---

## Browser support

Works in all current versions of Chrome, Firefox, Safari and Edge. Requires support for CSS custom properties, `IntersectionObserver`, and ES6 template literals. WebGL is optional, as noted above.

`prefers-reduced-motion: reduce` is honoured throughout: it disables the count-ups, the cascading bullets, and the entrance animations.

---

## Deployment

GitHub Pages serves the `main` branch from the repository root. There is no CI, no build, and no deploy script.

```bash
git add .
git commit -m "Update experience section"
git push
```

Live within about a minute.

---

## Contact

|  |  |
|---|---|
| **Site** | [damehedi.github.io](https://damehedi.github.io) |
| **LinkedIn** | [in/damehedi](https://www.linkedin.com/in/damehedi/) |
| **Email** | mdrahmathullahmehedi@gmail.com |

Open to conversations about commercial analysis, sales, consulting and graduate schemes.

---

<div align="center">
<sub>Built by hand. No framework, no build step, no excuses.</sub>
</div>
