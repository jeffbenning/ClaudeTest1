# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Serving the project

Start the local dev server (port 3000) with:

```powershell
powershell -NonInteractive -ExecutionPolicy Bypass -File .claude/serve.ps1
```

Or use the launch configuration `.claude/launch.json` (name: `static`) which runs the same command. Files are served from the repo root — open `http://localhost:3000/dope.html` directly.

## Project structure

This is a zero-dependency, no-build vanilla HTML/JS project. The app is a single self-contained `.html` file at the repo root. There is no package.json, bundler, or framework.

- **`dope.html`** — Rifle ballistics DOPE (Data On Previous Engagements) calculator. Computes bullet drop and MRAD scope adjustments from 100–1000 yd using point-mass Euler integration with G1/G7 drag models. Persists cartridge and environment profiles to `localStorage`.

## Conventions

- **Dark theme**: `#141414` page background, `#1e1e1e` card background, `#333333` borders, `#e2e8f0` body text, `#ff7300` accent/highlight.
- **Font**: `'Courier New', monospace` throughout.
- **No external dependencies** — everything must work as a plain file opened in a browser or served by the static PowerShell server.
- All state is either in-page JS variables or `localStorage` (no backend, no cookies).

## dope.html ballistics engine

The physics lives entirely in the `<script>` block. Key details:

- **Two-pass simulation**: pass 1 integrates level-bore to find drop at zero distance → derives bore angle; pass 2 integrates with the tilted bore and records state at each 100-yd capture point.
- **Drag constants**: `K_G1 = 0.0002657`, `K_G7 = 0.0001461`. G1 uses McCoy 1999 Cd table; G7 uses Litz table.
- **Air density** is computed from temp (°F), pressure (inHg), and humidity (%) and multiplies the drag constant.
- **MRAD formula**: `mrad = -(bullet_above_LOS_inches) / (range_yd × 0.036)` — positive = dial UP.
- **Results div** (`#out`) must be shown with `style.display = 'block'`, not `style.display = ''` (the latter lets the CSS `display:none` rule win and keeps results hidden).
- Cartridge profiles stored under `localStorage` key `'dopeCartProfiles'`; environment profiles under `'dopeEnvProfiles'`.
