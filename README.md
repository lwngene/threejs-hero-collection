# threejs-hero-collection

**Drop-in animated hero backgrounds built with Three.js — particles, waves, aurora, terrain and orb scenes, each a single HTML file**

[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE) [![Live demos](https://img.shields.io/badge/demos-live-2563EB)](https://alebrito124356.github.io/threejs-hero-collection/)

Every "add WebGL to your landing page" tutorial ends with a bundler, a `node_modules` folder and thirty files. This repo goes the other way: five production-ready hero backgrounds, each one a **complete, standalone HTML file** — markup, styles, scene code and hero copy included. Copy a file, change the text and the color constants at the top of the script, and ship it. Each scene also demonstrates a different core Three.js technique (points, vertex displacement, fragment shaders, procedural noise, physical materials), with comments written to teach, not just to label.

## Demos

| Page | What it contains | Source | Live |
|------|------------------|--------|------|
| Gallery | Cards linking to all five heroes | [index.html](index.html) | [live](https://alebrito124356.github.io/threejs-hero-collection/index.html) |
| Particles | ~2,000-point drifting field, mouse parallax, optional connection lines (off by default) | [particles.html](particles.html) | [live](https://alebrito124356.github.io/threejs-hero-collection/particles.html) |
| Waves | Wireframe plane displaced by three superposed sine waves, crest-colored vertices | [waves.html](waves.html) | [live](https://alebrito124356.github.io/threejs-hero-collection/waves.html) |
| Aurora | Fullscreen-quad fragment shader: fbm noise, domain warping, blue-to-cyan palette | [aurora.html](aurora.html) | [live](https://alebrito124356.github.io/threejs-hero-collection/aurora.html) |
| Terrain | Retro-futurist wireframe landscape scrolling toward the camera, value-noise heights, horizon glow | [terrain.html](terrain.html) | [live](https://alebrito124356.github.io/threejs-hero-collection/terrain.html) |
| Orb | MeshPhysicalMaterial sphere + additive fresnel shell, orbiting point light | [orb.html](orb.html) | [live](https://alebrito124356.github.io/threejs-hero-collection/orb.html) |

## Usage

1. Copy any hero file into your project.
2. Replace the `<h1>`, subtitle and CTA with your own copy.
3. Tweak the `COLORS` and settings constants at the top of the `<script type="module">` block — every scene keeps its knobs there.

### The only dependency

These pages import **three.js 0.170.0, pinned via import map** from unpkg — that is the single external request the whole repo makes (no fonts, no CSS frameworks, no analytics):

```html
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.170.0/build/three.module.js"
  }
}
</script>
```

**To self-host** (recommended for production): download the file once and point the import map at your copy — no other change needed, no build step involved:

```bash
curl -o js/three.module.js https://unpkg.com/three@0.170.0/build/three.module.js
```

```html
<script type="importmap">
{ "imports": { "three": "./js/three.module.js" } }
</script>
```

Import maps require a modern browser (Chrome/Edge 89+, Firefox 108+, Safari 16.4+) — the same baseline WebGL-heavy pages already assume.

## Techniques

Reading the source top to bottom, you will find:

- **BufferGeometry from scratch** — typed arrays for positions, per-vertex colors and CPU-side velocities; wrap-around bounds that keep particle density constant (particles).
- **Vertex displacement without shaders** — cache the flat base coordinates once, then write `height(x, z, t)` into the position attribute each frame; superposed sines (waves) vs. fbm value noise (terrain).
- **The fullscreen-quad idiom** — a vertex shader that writes clip space directly, turning Three.js into a pure fragment-shader canvas (aurora).
- **Value noise and fbm, twice** — the identical construction implemented in GLSL (aurora) and in plain JavaScript (terrain), so you can compare them line by line.
- **Domain warping** — feeding one noise field into another's coordinates to turn flat clouds into flowing curtains (aurora).
- **The scrolling-terrain illusion** — the mesh never moves; only the noise-sampling offset advances, so the landscape flows forever with no loop seam (terrain).
- **Fresnel rim lighting** — a view-angle power function on a slightly larger additive shell, the standard "atmosphere" trick (orb).
- **Zero-asset texturing** — a radial gradient painted on a 2D canvas and used as a `CanvasTexture` for the horizon glow (terrain).
- **Respectful animation loops** — a shared loop skeleton with clamped deltas, `visibilitychange` pause, live `prefers-reduced-motion` handling and resize logic (every page).

## Performance notes

- **Device pixel ratio is capped at 2** (`renderer.setPixelRatio(Math.min(devicePixelRatio, 2))`). Phones report DPR 3+; uncapped, a full-screen canvas renders more than twice the pixels for no visible gain on a background — the aurora page suffers most, since fragment-shader cost scales directly with pixel count.
- **The loop pauses when the tab is hidden.** `requestAnimationFrame` already throttles background tabs, but cancelling the loop on `visibilitychange` drops CPU/GPU usage to zero and stops laptop fans from spinning over a tab nobody is looking at.
- **`prefers-reduced-motion` renders a single static frame.** Users who ask the OS for less motion still get a designed scene — it just does not move. The pages also react live if the preference changes while open.
- **Dispose what you create.** These standalone pages let the browser reclaim everything on navigation, but if you embed a scene in a SPA you must call `geometry.dispose()`, `material.dispose()`, `texture.dispose()` and `renderer.dispose()` on unmount — WebGL resources are not garbage collected while the context lives. Each file ends with a comment listing exactly what to dispose.
- **Particle connection lines are O(n²) — and off by default.** Drawing a line between every pair of nearby particles means checking every pair: 2,000 particles is ~2,000,000 distance checks *per frame*, which no amount of GPU helps with because it happens on the CPU. If you flip `SHOW_CONNECTIONS` to `true`, drop `PARTICLE_COUNT` to ~300 (300² = 90,000 checks — fine). The implementation preallocates one `LineSegments` buffer and uses `setDrawRange` to avoid per-frame allocation, which is the right pattern if you do turn it on.

## Related repos

- [css-animation-cookbook](https://github.com/AleBrito124356/css-animation-cookbook) — pure-CSS animation recipes
- [svg-animation-lab](https://github.com/AleBrito124356/svg-animation-lab) — SVG line, morph and motion techniques
- [scroll-story-lab](https://github.com/AleBrito124356/scroll-story-lab) — scroll-driven storytelling patterns
- [easing-playground](https://github.com/AleBrito124356/easing-playground) — interactive easing-curve explorer
- [tailwind-landing-sections](https://github.com/AleBrito124356/tailwind-landing-sections) — copy-paste Tailwind landing-page sections
- [remotion-video-templates](https://github.com/AleBrito124356/remotion-video-templates) — programmatic video templates in React

## License

[MIT](LICENSE)
