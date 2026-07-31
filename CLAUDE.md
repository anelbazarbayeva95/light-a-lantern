# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

**Light a Lantern** is a single self-contained meditation web app: one `index.html` (~65 KB) containing all HTML, CSS, and JS. There are **no dependencies, no build step, no package.json, and no external assets** — every visual is inline SVG/CSS and every sound is synthesized live via the Web Audio API, so the app runs fully offline by opening the file.

## Commands

- **Run/develop:** open `index.html` in a browser. No build, no server needed.
- **Deploy:** push to `main`. GitHub Pages serves from `main` / root at
  `https://anelbazarbayeva95.github.io/light-a-lantern/` (a "pages build and deployment" run publishes it automatically). Pages settings are managed in the repo UI, not in code — there is no workflow file.
- **No test/lint tooling exists.** To sanity-check that the inline script still parses:
  `node -e "const s=require('fs').readFileSync('index.html','utf8');new Function(s.match(/<script>([\s\S]*)<\/script>/)[1])"`
- **Visual verification** is done by driving headless Chromium with Playwright (installed globally). Launch with `executablePath:'/opt/pw-browsers/chromium'`. Note: audio is suspended without a gesture — pass `args:['--autoplay-policy=no-user-gesture-required']` and tap an `AnalyserNode` onto `AudioContext.prototype` (override `destination`) to measure real RMS when testing sound.

## Architecture (all inside `index.html`)

The whole app is one IIFE. Key concepts, in dependency order:

- **`state`** — single mutable object (current `style`, active `palette`, `density`, `sway`, `bell`, `volume`, `breath`, `pace`, `sounds` Set, timer handles). `muted` is a separate module-level `let`.
- **`STYLES`** — the registry of the **ten lantern cultures** (hoian, chinese, chochin, andon, korean, kandil, khomloi, moroccan, parol, farolito). Keep this count in sync with `STYLES` when adding one. Each entry defines `kind` (silhouette), `pal` (`[hex, hz]` pairs — each colour rings a different bell pitch), `sound` (signature soundscape), `sky` (a `SKY.*` gradient applied to `document.body.style.background`), `moon` (`{top,left,r}` — the moon glides/resizes per culture), `origin`/`note` (history shown in the Collection panel), and `proverb` (`{native, roman, en}` — a native-script saying + romanization + translation, surfaced on the scene). Adding a culture = add one `STYLES` entry (with its `proverb`) + one `styleChips` button + a `case` in `lanternSVG`, and usually a new `SKY.*` gradient.
- **`lanternSVG(kind, silk)`** — the render engine. A `switch(kind)` builds each culture's SVG from shared helpers (`barrel`, `vribs`, `core`, `goldCap`, `tassel`, plus the ornate finish: `fringe` bristles, and `sprig`/`flower` for the gold floral motif painted on the silk). The `g${id}` gradient is a two-tone ombre (bright warm crown → deep saturated base). The silk-barrel cultures (hoian, round/chinese, chochin, korean) get plump bodies + a `sprig` motif + `fringe`; the geometric ones (andon, kandil, moroccan, parol, farolito) and the floating khomloi keep their own silhouettes. Every lantern uses three toggled layers driven by CSS: `.silk` (body), `.core` (warm inner glow, shown only when lit), `.scrim` (dark overlay dimming the whole lantern when unlit). Lit state is the `.lit` class on the `.lantern` element; `#lanterns.breathing` drives the breathing pulse. The same function renders the mini icons inside the prev/next preview cards.
- **Web Audio** — `audio()` lazily creates the `AudioContext` + `master` gain (respecting the module-level `muted`). `bell()`/`gong()` are additive-partial synths; `builders.{rain,drone,chimes,night}` return `{stop()}` handles managed by `toggleSound()`. All sound is generated, never sampled.
- **Scene** — `createLanterns()` clears `#lanterns` and lays out `state.density` lanterns in a sparse shuffled-column arrangement (`makeLantern()` builds each element, wires tap-to-light, applies per-lantern CSS custom props `--cord`/`--halo`/sway timing). `buildScene(animated)` wraps it with a smooth swap: existing lanterns get `.leaving` (drift up + fade), then after ~430ms the new set is created with `.entering` (staggered rise-in via `--in-delay`) which hands off to `.sway` on `animationend`. Pass `buildScene(false)` for an instant rebuild (used on resize); reduced-motion also skips the animation. A `sceneToken` guards against overlapping swaps.
- **Navigation between cultures** — `applyStyle(id)` sets palette + sky + moon + signature sound + note, cross-fades the on-scene proverb via `setSceneNote(id)` (a fixed `#sceneNote` in the bottom-right, hidden below 760px), then `regenStars()` (repositions stars), `updateSwipeCards()` (fills the prev/next preview cards with a mini lit lantern + name), and `buildScene()`. Reached four ways: Collection panel chips, the on-screen **prev/next preview cards** (top corners, which idle-fade via `wakeCards()`), horizontal **swipe** on `#scene`, and **← →** arrow keys — all via `cycleStyle`. Swipe suppresses the lantern tap via `state._lastSwipe`.
- **Panels & UI** — `panelCollection / panelSound / panelRitual / panelConfigure / panelAbout` are mutually-exclusive slide-in panels (`togglePanel`/`closePanels`). Mood presets (`PRESETS`) and the `PALETTES` mood recolours are independent knobs from the Collection; selecting one clears the other's active chip.
- **First-run** — a `#intro` overlay shows once (localStorage key `lal_seen`), reopenable via the `?` help button.

## Conventions

- **Layout breathing room:** `createLanterns()` lays out one lantern per shuffled column, but insets the usable width by a horizontal margin (`marginX = min(160, W*0.09)`) so lanterns never hug the left/right screen edges. It also keeps the **top corners clear of the fixed prev/next preview cards**: any lantern whose body reaches into those zones (`x−size/2 < 120` or `x+size/2 > W−120`) is dropped to hang below them (`yTop ≥ 215`). The cards are position:fixed and don't scale with width, so this guard — not the width-proportional margin — is what prevents corner overlap on narrow screens. Keep both when changing the layout; the scene should feel airy, not crowded at the sides or clashing with the cards.
- **Responsive count & sizing:** the number of lanterns is `min(state.density, floor(usable/packW))` — phones land ~5–6, wide screens use the full density. Each lantern's size varies but is capped to `0.92 * colSpacing`; because a lantern's visible body is only ~70% of its SVG width, this cap guarantees neighbouring **bodies never overlap** while still allowing some to be bigger than others. On narrow screens lanterns also spread further down the viewport (`yTop` up to `0.62*H`) so the tall phone screen fills instead of clustering at the top.
- Respect `prefers-reduced-motion` (already gated via the `reduceMotion` flag and a CSS media block) when adding animation.
- Typography is a system serif stack for display + a mono stack for labels; the palette lives in `:root` CSS variables. Keep the deliberate single dark "night" aesthetic.
- Because everything ships in one file with a strict single-file constraint, do **not** introduce external requests, CDN links, fonts, or a build pipeline.
