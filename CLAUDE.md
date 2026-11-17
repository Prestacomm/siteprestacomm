# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Dev server

Launch via the browser pane (`.claude/launch.json` defines two configs):

```bash
python3 -m http.server 3000
# or
npx serve . -p 3000 --no-clipboard
```

Open `http://localhost:3000` to preview with JS (GSAP, ScrollTrigger) fully active. The file:// protocol renders as a static snapshot — animations and counters won't fire.

## Architecture

Single-file static site: **`index.html`** contains all CSS, HTML, and JS inline.

**Brand tokens (`:root` in `<style>`):**
- `--bg` `#FAF8F5` — warm white (main background; charte cream `#FDEAAD` kept only as text on burgundy sections)
- `--bg-2` `#F1F1F1` — light gray (services section)
- `--bg-3` `#F2EDE4` — desaturated warm cream (process, faq)
- `--fg` `#7C0F27` — dark burgundy (primary text, headings, stats bg)
- `--accent` `#87AFE9` — blue
- `--gold` `#E64F61` — coral/pink (secondary accent, italic emphasis)
- `--border` `rgba(124,15,39,0.12)`

**Fonts:** Cormorant Garamond (headings, nav, titles) + Lora (body, labels) — both loaded from Google Fonts.

**JS (inline `<script>`):** GSAP + ScrollTrigger for scroll reveals, hero entrance, parallax glow, counter animation, gallery tilt. Reveal elements use `.js-ready .reveal` so they're visible without JS — `js-ready` class is added to `<html>` only after GSAP loads.

**Sections in order:** `#nav` → `#hero` → logo slider (clients, inside the hero block) → `#stats` → `#services` → `#gallery` → `#values` → `#process` → `#testimonials` → `#booking` → `#faq` → `footer`

There is no pricing section and no platform marquee anymore (both were removed on purpose; see git history if they ever need to come back).

## Section notes

**`#stats`** — heading + 4 pastel tiles (`.stat-tile`), each with an icon, a counter (`.counter[data-target]`) and a unit in `<sub>`. Counters are animated by the "Stats counter" block in the GSAP `load` listener. Inspired by 21st.dev "Numbers Stats Grid".

**`#gallery`** ("Nos réalisations") — fan carousel (`.fan-layout` / `.fan-card`), a vanilla-JS port of the 21st.dev `card-fan-carousel` React component (GSAP; entry elastic animation, hover pushes neighbours aside, arrows + dots only if more than 7 cards). Without JS (`.js-ready` missing) cards fall back to a wrapped grid. Cards use the real photos in `assets/gallery/`; captions show on hover. To add a project, add another `<figure class="fan-card">`.

**`#values`** — 4 cards (`.v-card`): icon + `NN` marker with 4 small bars (`.v-bars`, one `.on`, previous ones `.lit`). Counter and bars animate on scroll; hover lifts the card with the CSS `translate` property (not `transform`, which GSAP owns).

**`#process`** ("Notre méthode") — 4 steps with solid burgundy dots (`.p-num`) joined by a line (`.process-steps::before`, driven by the CSS variable `--line`). A GSAP timeline draws the line and pops each step in; `.p-step` intentionally has no `.reveal` class.

**`#booking`** ("Quelle est votre situation ?", sits before the FAQ): 3 intent cards. Clicking a card shows one of three panels below — `#panel-questionnaire`, `#panel-calendly`, `#panel-contact`. Calendly script (`assets.calendly.com/assets/external/widget.js`) is lazy-loaded only when the calendly card is selected. Calendly URL: `https://calendly.com/contact-prestacomm/30min`. Forms use `action="mailto:contact.prestacomm@gmail.com"`.

**`#faq`** (bg `--bg-3`): 6 `<details>`/`<summary>` accordion items. No JS needed — native HTML. Icon rotates 45° on `details[open]` via CSS. One answer ("Y a-t-il un engagement de durée ?") still talks about monthly/quarterly/annual formulas that no longer exist on the site; rewrite it when the offer is decided.

## Key JS patterns

- **`.js-ready .reveal`** — elements start visible; GSAP hides them (`opacity:0, y:36px`) only after `js-ready` is added to `<html>`. Always test with a real server, not `file://`.
- **GSAP owns `transform`** on animated elements. For CSS hover lifts use the `translate` / `rotate` individual properties instead, or they get overridden.
- **Intent cards** is a self-invoking `(function(){…})()` block at the end of `<script>`, after the GSAP `load` listener. The fan carousel and the other scroll animations live inside the `load` listener.
- **Animations respect `prefers-reduced-motion`** (process, values, fan carousel); keep that when adding new ones.
- **Stats counter** runs on `.counter[data-target]` elements inside `#stats`.

## Working notes

- In the sandbox the GSAP CDN may be blocked; when testing animations locally, intercept `gsap.min.js` / `ScrollTrigger.min.js` with copies from npm (`gsap@3.12.5`).
- Statistics in `#stats` (20+, 47 %, 4 M+, 96 %) and the sample copy of the section headings are placeholders to confirm with the client.
