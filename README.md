# Light a Lantern 🏮

A quiet, single-file meditation web app: a **minimal moonlit night** of
silk lanterns — inspired by the lantern-lit streets of **Hội An,
Vietnam** — that you tap to light, with warm ambient sound synthesized
live in your browser. It's a small ritual for slowing down, and for
letting go.

The design is deliberately spare and editorial: a deep-navy sky, a moon,
a handful of glowing lanterns with room to breathe, an elegant serif
headline and tiny monospace labels. Controls live in unobtrusive panels
(Sound · Ritual · Configure) so the night stays calm.

**No dependencies. No external assets. No build step.** Everything —
including every sound — lives in one `index.html` and is generated at
runtime with the Web Audio API, so it works fully **offline**.

## Try it

Open `index.html` in any modern browser. That's it.

```bash
# clone, then just open the file
open index.html          # macOS
xdg-open index.html      # Linux
```

Or, if this repo is served with **GitHub Pages**, it runs live at
`https://anelbazarbayeva95.github.io/light-a-lantern/`.

## What it does

**Lanterns**
- Tap any lantern to kindle a warm glow and a soft bell tone.
- Six silk colours, each tuned to a different pitch, so lighting the
  alley plays a gentle scale.
- Lanterns hang in an organic cluster with a gentle sway; the cluster
  regenerates on load and on window resize.

**Gestures**
- **Kindle all** / **Dim all** — light or quiet the whole alley.
- **Release** — lit lanterns drift up to the sky and fade away, a
  "letting go" gesture. The alley quietly refills afterward.

**Ambient soundscapes** (layer as many as you like, all synthesized)
- 🌧 **Rain**
- 🛕 **Temple drone**
- 🔔 **Chimes**
- 🦗 **Night / crickets**

**Mood presets** — one tap sets colour, density, breathing and sound:
- **Focus** · **Unwind** · **Sleep** · **Festival**

**Customize panel**
- Colour mood: Festival / Warm / Cool / Moonlight
- Lantern density
- Gentle-sway toggle
- Bell-on-tap toggle
- Master volume

**Breathing guide**
- A pulsing glow plus a centre cue ring with in/hold/out prompts.
- **Box (4·4·4·4)** or **4·7·8** patterns, with an adjustable pace
  (quick → deep).

**Session timer**
- 5 / 10 / 20 / 45 minutes, closing with a deep resonant gong as the
  sounds fade and the lanterns dim.

## Notes

- Respects `prefers-reduced-motion` (sway, twinkle, flicker and drift
  are disabled).
- Responsive, and designed as a deliberate single dark "night" world —
  there's no light theme by intent.
- Audio starts on your first interaction, per browser autoplay rules.

## License

MIT — do what you like; a kind word is appreciated.
