






# Hungry-Delete-Button

<div align="center">

<img src="assets/banner.svg" alt="Hungry Delete: a delete button where the bin eats the label" width="100%">

<br>

![dependencies](https://img.shields.io/badge/dependencies-0-c8ff3a?style=for-the-badge&labelColor=14141a)
![built with](https://img.shields.io/badge/built_with-CSS_%2B_WAAPI-2c47ff?style=for-the-badge&labelColor=14141a)
![single file](https://img.shields.io/badge/single-file-ff3d8b?style=for-the-badge&labelColor=14141a)
![license](https://img.shields.io/badge/license-MIT-ff8a1f?style=for-the-badge&labelColor=14141a)

### Press it. The bin eats the word. 🗑️

**[▶ Live demo](https://www.instagram.com/reel/Dd-g6VkIcWt/)** · **[Quick start](#-quick-start)** · **[Customize](#-customize)** · **[How it works](#-how-it-works)**

</div>

---

## ✦ What is this?

A delete button that doesn't just *say* delete, it **does** it. Press it and the trash bin tips open, every letter of the label hops out and falls in, the pill squeezes into a disc, a ring spins while your request runs, then **seals**. If the request fails, the timeline **rewinds**: the bin spits the letters back, one by one.

One HTML file. No framework. No build step. No dependencies.

<div align="center">

<img src="assets/demo-success.svg" alt="Success animation: letters fall into the bin, the pill becomes a disc, ring seals, sparks burst" width="720">

<img src="assets/demo-fail.svg" alt="Failure animation: the bin spits the letters back and the button shakes" width="720">

</div>

## ✦ Highlights

| | |
|---|---|
| 🔤 **Letters are the progress meter** | Every landing raises the bin's level. The animation *is* the state, not a timer. |
| 🌀 **One clip, pill to disc** | The face and the hit target share one `clip-path`, so what you see is what you can click. |
| ⏪ **Failure = rewind** | Errors play the eat in reverse: letters pop back out and the label rebuilds. |
| 🎵 **Musical sound** | WebAudio synth, no files. Pitch climbs a pentatonic scale as the bin fills. Starts only after a click. |
| 📳 **Haptics** | Tiny vibration ticks on supported mobile browsers. |
| 🧲 **Magnetic tilt** | The button leans toward your pointer. |
| ✨ **Seal burst** | Colorful confetti shards on success. |
| ♿ **Accessible** | `aria-busy`, a live `role="status"` region, visible keyboard focus, `prefers-reduced-motion` support. |

## ✦ Quick start

```bash
git clone https://github.com/wearepixelflow/Hungry-Delete-Button.git
cd Hungry Delete Button
open index.html        # or just double-click it
```

That's it. Everything (markup, CSS, JS) lives in [`index.html`](index.html). The only external request is the Google Fonts link for the title, which falls back to a system font offline.

### Use it in your own page

1. Copy the `.gulp` rules from the `<style>` block.
2. Copy the `<template id="tpl">` block (the button markup).
3. Copy the `<script>` block.
4. Drop a placeholder where you want a button:

```html
<span class="gulp" data-state="idle"></span>              <!-- ink + lime -->
<span class="gulp" data-state="idle" data-tone="blue"></span>   <!-- electric blue -->
```

Each placeholder is filled from the template when the script runs.

### Wire up a real delete

The demo fakes the server with a wait. In the click handler, find this line:

```js
await sleep(quick ? 150 : 1100);
```

and replace it with your request, e.g.:

```js
const res = await fetch('/api/items/42', { method: 'DELETE' });
if (!res.ok) throw new Error('delete failed');
```

> **Heads up:** the demo's "fails" button is hard-wired to fail through the `fail` option passed to `Gulp()`. For a real button, branch on your request result instead: success runs the seal path, an error runs the rewind path (both already exist in the handler).

Want a different word? Change the text inside `<span data-label>Delete</span>` in the template. Every letter becomes its own animated element automatically.

## ✦ Customize

Everything is driven by CSS custom properties on `.gulp`:

| Variable | What it does | Default (ink) |
|---|---|---|
| `--fg` | Label, bin and fill color | `#c8ff3a` |
| `--face` | Button background (any gradient) | dark ink gradient |
| `--ring` | Focus / accent color | `#c8ff3a` |
| `--sh` | RGB triplet for the drop shadow | `20,20,26` |
| `--rr` | Corner radius of the pill | `48px` |
| `--t` | Pill → disc morph duration | `.44s` |

Make a new flavor in four lines:

```css
.gulp[data-tone="hot"]{
  --fg:#fff; --ring:#ffd1e6; --sh:255,61,139;
  --face:linear-gradient(120deg,#ff8a1f,#ff3d8b 60%,#8b5cff);
}
```

```html
<span class="gulp" data-state="idle" data-tone="hot"></span>
```

## ✦ How it works

The button is a tiny state machine driven by `data-state` on the root element. CSS owns how each state *looks*; a short async function owns *when* it changes.

| State | What happens |
|---|---|
| `idle` | Pill at rest. Pointer tilt active. |
| `eat` | Lid pops, bin tips, each letter flies on a Web Animations arc into the bin. Each finished flight raises `--p` (the fill level) and plays a note. |
| `furl` | Pill clips down to a disc, the bin slides to the center. |
| `pending` | An arc spins around the disc for as long as your request takes (with a minimum so it never flickers). |
| `done` | The ring seals, the bin empties, confetti bursts. |
| `back` | The same clip runs backwards to the pill and the label returns. On failure, letters are spat back in reverse order first. |

Nerdy bits worth stealing:

- `@property --p` and `--fx` are registered, so the browser **interpolates them natively**, with no `requestAnimationFrame` loop.
- Letter flights use the Web Animations API, and `animation.finished` is the progress signal.
- The bin's inside is a `clipPath`, so liquid is only ever drawn inside the bin.
- Failure is `playbackRate = -2.4` on the very same letter animations.

## ✦ Browser support

Built for current evergreen browsers (Chrome, Edge, Safari, Firefox). It relies on registered custom properties (`@property`), `:has()`, individual transform properties (`translate`, `rotate`, `scale`) and the Web Animations API. Older browsers may lose parts of the effect.

## ✦ Project structure

```
hungry-delete/
├── index.html              the whole component + demo page
├── assets/
│   ├── banner.svg          animated README banner
│   ├── demo-success.svg    animated success flow
│   └── demo-fail.svg       animated failure flow
├── LICENSE
└── README.md
```

## ✦ Roadmap

- [ ] Extract into a package with `Gulp(el, { request })`
- [ ] Press-and-hold to confirm
- [ ] Real letter physics (Matter.js) in the bin
- [ ] More verbs: **Send**, **Shred**, **Pay**
- [ ] React / Vue / Svelte wrappers

## ✦ Contributing

Ideas, new tones and new "eating" animations are welcome. Open an issue or PR.

## ✦ License

[MIT](LICENSE) © YOUR NAME

<div align="center">

<sub>Made with 🗑️ and a questionable appetite.</sub>

</div>
