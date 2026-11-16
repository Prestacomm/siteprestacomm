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

**Sections in order:** `#nav` → `#hero` → `.marquee-wrap` → `#stats` → `#services` → `#gallery` → `#process` → `#testimonials` → `#pricing` → `#faq` → `#booking` → `footer`

## Sections added since initial build

**`#pricing`** (dark burgundy bg `--fg`): 3 pricing cards (Essentiel / Standard / Premium) with JS tab switcher (Mensuel / Trimestriel / Annuel). Tab logic is a self-invoking function at the bottom of `<script>`. Prices are placeholders — update directly in the HTML.

**`#faq`** (bg `--bg-3`): 6 `<details>`/`<summary>` accordion items. No JS needed — native HTML. Icon rotates 45° on `details[open]` via CSS.

**`#booking`** (3 intent cards): Clicking a card shows one of three panels below — `#panel-questionnaire`, `#panel-calendly`, `#panel-contact`. Calendly script (`assets.calendly.com/assets/external/widget.js`) is lazy-loaded only when the calendly card is selected. Calendly URL: `https://calendly.com/contact-prestacomm/30min`. Forms use `action="mailto:contact.prestacomm@gmail.com"`.

## Key JS patterns

- **`.js-ready .reveal`** — elements start visible; GSAP hides them (`opacity:0, y:36px`) only after `js-ready` is added to `<html>`. Always test with a real server, not `file://`.
- **Pricing tabs** and **intent cards** are both self-invoking `(function(){…})()` blocks at the end of `<script>`, after the GSAP `load` listener.
- **Counter animation** runs on `.counter[data-target]` elements inside `#stats`.
