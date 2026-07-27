<div align="center">

# generative-engine

### **THE THIRD ENGINE** — the [Generative Charts](https://chromaticcoherence.ai/generative-charts) family grows into a full **WebGL2 graphics engine**.

[![licence: source-available](https://img.shields.io/badge/licence-source--available-1e88e5)](./LICENSE)
[![zero dependencies](https://img.shields.io/badge/dependencies-0-success)](#)

**[Live scenes →](https://chromaticcoherence.ai/generative-engine)** · **[The charts sibling →](https://www.npmjs.com/package/@chromatic-coherence/generative-charts)**

</div>

---

The charts library stays what it is — small, stable, chart-first. **generative-engine** is where
the family expands: the same API (`createWorld` · `face` · `line` · `glow` · `grow` · `draw*` ·
`fade`), so chart recipes port forward by changing one import — and underneath it, a real
graphics engine. Zero dependencies; the browser is the only runtime.

```bash
npm install @chromatic-coherence/generative-engine
```

```js
import { createWorld, v3, icosphere, heightfield } from "@chromatic-coherence/generative-engine";

const w = createWorld({ canvas, controls: true, shadows: true });
w.lights.push({ p: v3(-160, 320, 120), intensity: 1.2, falloff: 500 });
w.mesh(heightfield((x, z) => Math.sin(x * 0.01) * 20, 800, 800), { hue: 262, sat: 38, light: 26 });
w.meshMorph(icosphere(70, 3), myBlob, { hue: 350, sat: 30, light: 42, gloss: 0.5, group: "blob" });
w.grow((t) => { w.morph("blob", 0.5 + 0.5 * Math.sin(t)); w.setTransform("blob", { rotateY: t * 0.4 }); });
w.start();
```

## What the engine adds over the chart editions

| | |
|---|---|
| **WebGL2 + HDR post chain** | The scene renders to a floating-point target, then: **bloom** (soft-knee bright pass, half-res gaussian), **SSAO** (depth-based contact shading), one composite pass (ACES filmic, colour grade, vignette), and **FXAA**. All art-directable (`w.bloom`, `w.ssao`, `w.fxaa`), all on by default. `post: false` opts out. |
| **Real shadow maps** | A depth **texture** with hardware-comparison PCF (`sampler2DShadow`) — soft, exact, 2048px by default. `shadows: { size, softness, bias }`. |
| **Geometry stdlib** | `plane` · `box` · `icosphere` · `tube` (parallel-transport sweep, taperable) · `lathe` · `extrude` · `heightfield` · `transformMesh` · `mergeMesh` — all pure math, all unit-tested, uploaded in one `w.mesh(geo, opts)` call with smooth normals. |
| **Ribbons** | `w.ribbon(points, { width })` / `w.drawRibbon(...)` — screen-space-expanded strokes with **real pixel width**, feathered edges, depth-tested. The stroke aesthetic `gl.LINES` never allowed. |
| **Group transforms** | `w.setTransform(group, { translate, rotateY, scale })` — geometry moves rigidly in the vertex stage, zero CPU re-tessellation, shadow pass included. |
| **Morph targets** | `w.meshMorph(geoA, geoB, opts)` + `w.morph(group, t)` — blend between two vertex sets in the shader (breathing, becoming). Eased like `fade`. |
| **8 point lights** | Up from 4; `lights[0]` casts the shadows. HDR intensities welcome — bloom catches what exceeds 1.0. |
| **The carried light model** | Everything the chart edition learned rides along: GGX + Fresnel specular, procedural micro-relief, the perfusion term (`blood`, `bloodPulse`), pore noise, subsurface rim, coloured hemispheric ambient, fog — now resolving toward a real background colour the engine owns. |

**The ink edition** (`theme: "light"`) still renders any recipe on paper instead of night —
lightness inverts, strokes blend normally, SSAO becomes pencil shading. Bloom stays dark-only.

## Demo

`demo/index.html` — serve the package folder (`python -m http.server`) and open
`/demo/`. Drag to orbit, ctrl+wheel to zoom; `?theme=light`, `?post=0`, `?still`.

## Falling back

`createWorld` **throws** (never a blank page) when WebGL2 is missing. Catch it and build the
same recipe through the chart editions — the API is a subset, so the fallback chain
engine → charts-3d → charts-2d is three imports of the same code. Browsers cap live GL
contexts per page: on teardown call `dispose(true)` to release the context immediately, and
mount a **fresh** canvas if you rebuild (a lost context is dead on its canvas).

## Roadmap

Planar mirrors · instancing · depth of field · motion blur. The chart editions stay lean.

## Licence

**Source-available, not open source.** Free for individuals, charities and small businesses
(fewer than 25 people and under £1M annual revenue); a one-off **£100** covers any larger
organisation. Full terms in [LICENSE](./LICENSE); commercial licensing:
[hello@chromaticcoherence.ai](mailto:hello@chromaticcoherence.ai).

---

<div align="center">
<sub>© 2026 Chromatic Coherence. All rights reserved.</sub>
</div>
