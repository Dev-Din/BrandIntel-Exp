# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static homepage concept for BrandIntel (Kenyan corporate gifts, branded merchandise and workwear), built by Nurdin (nurdin.co.ke). There is no build step, package manager, test suite or linter. Open `index.html` directly in a browser (or serve the folder with any static server) to run it. Both pages are marked `noindex`.

Files:
- `index.html`: homepage (promo bar, header, hero, departments, popular products, process, branding methods, our work, contact CTA, footer).
- `quote-basket.html`: the quote basket and request form.
- `assets/grainmill-work.jpg`: the only image; used in the "Our work" case study tile.

## Architecture

**Styling is inline.** Every element carries its own `style="..."` attribute. The only `<style>` blocks hold the body background, link colours, the `[hidden]` rule, and the mobile media query in `index.html`. The media query uses `!important` to override inline styles and targets the `bi-tiles` / `bi-tile` classes (hero tiles) plus `h1` / `h2`. When changing a look, edit the inline style on the element, not a stylesheet. Palette used throughout: navy `#054061` / `#032b42`, yellow `#f2cb10`, green `#62b446` (text green `#3a7d27`), neutrals `#f4f6f1`, `#dde3da`, `#4f5f6b`. Fonts are Syne (headings) and DM Sans (body) from Google Fonts.

**The two pages share state through `localStorage`.** Key `brandintel-quote-basket` holds a JSON object of `{ productId: quantity }`. `index.html` adds or removes products (default quantity comes from `DEFAULTS[id].qty`); `quote-basket.html` changes quantities (steps of 10, minimum 10), removes items, and builds the request. Both pages wrap storage access in try/catch and fall back to an in-memory `memory` object, so they still work when storage is blocked (but state then does not survive navigation). Both re-render on `pageshow` so back/forward navigation shows current state.

**Rendering is string templates.** `renderHome()` and `renderBasket()` rebuild the product grid and basket list with `innerHTML` from the `PRODUCTS` array, then update the `[data-count]` badge (`index.html`) or the `[data-summary]` / `[data-units]` text (`quote-basket.html`). Interaction uses event delegation on the container (`data-toggle`, `data-more`, `data-less`, `data-remove`, `#sample`). After a re-render, focus is restored to the toggled button on the homepage.

**The quote form is a stub.** Submit only hides the form and shows the `#sent` confirmation; nothing is sent. The WhatsApp button is the real handoff: its `href` is rebuilt on each render as a `wa.me/254723081920` link with the basket lines URL-encoded into the message. The "Logo uploaded: brand-logo.ai" line is hardcoded placeholder copy.

## Keep in sync

`PRODUCTS` and `DEFAULTS` are copied verbatim into the `<script>` of both HTML files, along with `KEY`, `loadBasket` and `saveBasket`. Any product added, renamed or re-defaulted must be changed in both files, or the basket will silently drop or mis-render items (the basket filters `PRODUCTS` by what is stored). Every product id in `PRODUCTS` needs a matching `DEFAULTS` entry. Contact details (phone `+254723081920`, `info@brandintel.co.ke`) are repeated across the promo bar, hero, footer and WhatsApp links.

## Placeholders

Bracketed tokens are intentional unfilled client content: `[RESPONSE TIME]` (process step 3 on the homepage and the form note on the basket page) and `[MOQ]` (product cards and the basket quantity note). The basket's "Request sent" message separately hardcodes "3 hours". Several links (`#methods` branding method pills, case-study links to `#work`) are placeholders pointing at in-page anchors.
