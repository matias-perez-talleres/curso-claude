# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A vinyl turntable music player with two visual variants, each a fully self-contained HTML file. No build tools, no dependencies, no package manager.

| File | Description |
|---|---|
| `tocadiscos.html` | Original design — warm wood cabinet, purple/magenta tones |
| `tocadiscos-neon.html` | Modern redesign — "VΛLTRON Reference One", matte black + RGB LED aesthetic |

To use: open either file directly in a browser.

Both files share identical JavaScript logic and HTML structure. Only CSS and minor HTML additions differ.

## Architecture

Everything lives in one file with three sections:

1. **CSS** (inline `<style>`) — all visual design, animations, and layout.
2. **HTML** — two main blocks:
   - `.cabinet`: the turntable visual (platter, vinyl record, tonearm, decorative knobs).
   - `.controls`: playback controls, progress bar, volume, source selector, and playlist.
3. **JavaScript** (inline `<script>`) — no frameworks, vanilla JS only.

### Key layout geometry

The cabinet is `580×370px`. Critical absolute positions (used to align the tonearm with the record):

| Element | Position | Center |
|---|---|---|
| Platter | `left: 74px, top: 52px, 282×282px` | `(215, 193)` |
| Vinyl | `left: 12px, top: 12px` inside platter | same center |
| Tonearm pivot | `left: 413px, top: 51px` | `(424, 62)` |
| Tonearm element | `left: 416px, top: 62px, 16×162px` | rotates around `(424, 62)` |

### Tonearm animation

The tonearm rotates via CSS `transform: rotate()` with `transform-origin: 50% 0%` (pivots at its top-center). The `.playing` class is toggled by JS:

- **Stop/pause**: `rotate(-35deg)` — needle rests outside the record
- **Playing**: `rotate(22deg)` — needle sits on the outer groove

Transition: `0.9s cubic-bezier(0.38, 0.02, 0.18, 1.0)`.

### Vinyl spin

Controlled via `animation-play-state: paused | running` on `#vinyl`. The `@keyframes vinylSpin` is a simple `rotate(0deg → 360deg)` on the vinyl element itself (no wrapping div needed since there is no translate on that element).

### Audio sources

Two modes toggled by source tabs:
- **Local files** (`#fileInput`): multiple files create a `playlist[]` array of `{name, src (blob URL), duration}`. Blob URLs are revoked when replaced.
- **Online URL** (`#urlInput`): a single entry is loaded directly as `audio.src`. Subject to browser CORS restrictions — only direct audio URLs work (no streaming services).

### State

All state is in module-level variables: `playlist[]`, `currentIdx`, `isPlaying`. The `<audio id="audio">` element is the source of truth for playback position/duration.

### Keyboard shortcuts

`Space` = play/pause, `→` = next, `←` = previous (disabled when focus is on an `<input>`).

## Neon variant — extra elements (`tocadiscos-neon.html`)

Additional HTML elements not present in the original:

- `.led-strip-bottom`, `.led-strip-left`, `.led-strip-right` — animated LED edge strips on the cabinet
- `.platter-glow` — outer ring div centered with the platter (`left:61px, top:39px, 308×308px`) that emits a pulsing cyan glow when playing
- `.spectrum#spectrum` — decorative spectrum visualizer (20 `.spec-bar` divs injected by JS), positioned `right:44px, bottom:42px` inside the cabinet
- `.corner-dots` — three small decorative dots near the top-right of the cabinet

Additional CSS variables (`:root`): `--cyan: #00f0ff`, `--mag: #ff2d78`, `--green: #39ff14`.

Fonts used (Google Fonts): `Bebas Neue` (brand), `Rajdhani` (UI text), `JetBrains Mono` (times, data).

The `.cabinet.playing` class activates all LED glow effects simultaneously (strip brightness, ring animation, spectrum opacity, brand filter).
