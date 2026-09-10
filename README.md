# NOORWAY Logistics — Website (Phase 1)

Single-file, dependency-free marketing site with a quote form, WhatsApp path, route explorer and CMS-ready content blocks. Open `index.html` in a browser to preview; deploy the folder to any static host.

```
noorway/
├── index.html          # the whole site: tokens, components, config, logic
├── assets/noorway-logo.png
├── server/quote.js     # reference Node/Express endpoint for the quote form (phase-1 email + rate limit)
├── sitemap.xml
├── robots.txt
└── README.md
```

## 1. Product strategy (and what needs confirming)

**Strategy.** Lead with the shipment problem, make *Book a Truck* and *WhatsApp* the two obvious paths, and never show a number the business hasn't verified. The page is built so that today's honest empty states (sample availability, unverified corridors, no testimonials, pending contact details) become real data by editing one object — `NOORWAY_CONFIG` at the top of the script — not the markup.

**Assumptions to confirm with the business**

| # | Assumption | Where it lives |
|---|-----------|----------------|
| 1 | The fleet is 12-wheeler, full-truck-load only (no part loads) | hero meta, proof strip, status card |
| 2 | Berhampore → Kolkata / Durgapur / Siliguri are real, served corridors | `corridors[].verified: true` |
| 3 | Patna, Ranchi, Guwahati are aspirational — shown as "example" | `corridors[].verified: false` |
| 4 | No distances or transit times are published until operations supplies them | `distanceKm`, `transitWindow` = `null` |
| 5 | Load types listed in the form are the ones the business will accept | `loadTypes` |
| 6 | The eight customer segments and their one-line descriptions are accurate | `customerTypes` |
| 7 | FAQ answers (documents, loading responsibility, communication) match actual practice | `faq` |
| 8 | Response-time promise is deliberately blank until ops can commit | `form.responseExpectation` |

## 2. Sitemap and journeys

Phase 1 is a single page with anchors that map 1:1 to the future routes, so splitting into pages is a copy-paste, not a rewrite.

| Future route | Current anchor | Section |
|---|---|---|
| `/` | `#top` | Hero + status card + route pulse |
| `/about` | `#about` | Story |
| `/services` | `#services` | Services + Why NOORWAY |
| `/routes` | `#routes` | Route explorer |
| `/book` | `#book` | Quote form |
| `/business` | `#business` | Customer segments + proof |
| `/contact` | `#contact` | Contact cards |

**Journeys**
- *Quick quote (mobile broker):* Hero → Book a Truck → 8 fields → confirmation, or WhatsApp float at any point.
- *Route check (manufacturer):* Hero → Explore Our Routes → pick corridor → "Request a quote for this route" (pre-fills pickup/drop).
- *Recurring business:* Business solutions → segment → "Discuss recurring transport" → form (company field).

## 3. Design tokens and components

Tokens are the brief's `:root` variables, with two accessibility corrections noted inline in the CSS:
- `--color-muted` darkened from `#71807C` (4.1:1) to `#5E6E6A` (5.0:1) for body-size text.
- `--color-gold-text: #8A6420` added for gold *text* (5.0:1); `--color-gold` is kept for rules, dots and the gold button.
- Focus ring uses primary on light surfaces (11:1) and champagne on the dark section/footer (8:1), because gold on ivory is only 2.4:1.

Type: Plus Jakarta Sans throughout; DM Serif Display is used exactly twice (the "It's a promise." statement and the footer tagline). Fluid scale `--step--1` to `--step-4`.

Material split is roughly 70% clear ivory content, 20% glass (nav capsule, status card, WhatsApp float), 10% liquid (hero atmosphere, route pulse, gold light trail). Geometric star pattern is at 4.5% opacity in the Story section only.

Components: nav capsule · mobile menu dialog · hero scene (SVG) · status card · route ribbon · service card · why item · timeline · route explorer · quote form (+ error summary, status panel) · segment tabs · proof strip · testimonial (with empty state) · FAQ · contact card · footer · WhatsApp float.

## 4. Making changes (non-technical owner)

Everything editable is in `NOORWAY_CONFIG` near the bottom of `index.html`:

- **Contact details:** `business.phone`, `business.whatsappNumber` (digits only, e.g. `9198…`), `business.email`, `business.address`. Leaving any blank shows an honest "to be confirmed" state and hides broken links.
- **Form delivery:** `form.endpoint` → your server URL (see `server/quote.js`). Blank = the form keeps the user's data and offers WhatsApp/phone fallback instead.
- **Live availability:** switch `liveStatus.mode` to `"live"` only once the desk actually updates it.
- **Corridors:** set `verified: true` and fill `distanceKm` / `transitWindow` per corridor once confirmed.
- **Testimonials:** add objects with `permission: "granted"`; anything else is never rendered.

## 5. Phase-2 readiness

The quote form posts a flat JSON object (`pickup, drop, loadType, weight, loadingDate, name, company, phone, consent, startedAt`). That payload is the seed of a `QuoteRequest` record: Quote request → Operations dashboard → Vehicle availability → Driver assignment → Booking → WhatsApp updates → Delivery confirmation. `server/quote.js` shows the phase-1 endpoint shape; swap the email step for a database write when the dashboard exists.

## 6. Accessibility and motion — what was implemented

Skip link · landmarks · single H1 · visible 3px focus · keyboard-complete mobile menu (focus trap, Escape, focus return, scroll lock) · tab pattern with arrow keys for segments · labelled fields with `autocomplete`, inline errors, `aria-invalid`, error summary with links · `aria-live` only on form status, route summary and segment panel · alt text on the logo, `role="img"` label on the hero scene, empty alt on decorative marks · ribbon pauses on hover/focus/touch plus a Pause button · `prefers-reduced-motion` and `prefers-reduced-transparency` respected · tilt only on fine pointers · no autoplay media, no flashing.

Tested in headless Chromium at 1400px and 390px: no console errors, validation summary appears and focuses, Escape closes the menu and returns focus, active nav state updates on scroll.

## 7. Launch checklist

- [ ] Fill `business.*` with verified phone, WhatsApp, email and street address
- [ ] Replace every `PLACEHOLDER_*` in the JSON-LD block and the `noorwaylogistics.example` domain in canonical/OG/sitemap
- [ ] Deploy `server/quote.js` (or equivalent) with SMTP credentials **server-side**, then set `form.endpoint`
- [ ] Confirm the three "verified" corridors and decide whether Patna/Ranchi/Guwahati stay as examples
- [ ] Add distances / transit windows only from operations data
- [ ] Decide `form.responseExpectation` (or leave blank)
- [ ] Write Privacy Policy, Terms and Accessibility Statement pages (footer links are anchors today)
- [ ] Add a real OG image (1200×630) — the logo is used as a placeholder
- [ ] Optional: replace the SVG hero scene with a licensed/produced photo, keeping the `role="img"` label and reduced-motion fallback
- [ ] Add privacy-aware analytics (e.g. Plausible/Umami) and submit `sitemap.xml` to Search Console
- [ ] Run Lighthouse and a screen-reader pass (NVDA / VoiceOver) on the deployed URL
- [ ] Keep `liveStatus.mode: "sample"` until a data source exists
