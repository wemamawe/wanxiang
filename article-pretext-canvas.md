# Animating text like cherry blossoms with Pretext + Canvas 2D

*How a text-measurement library solved the hardest part of a petal-fall animation*

---

When I built a cherry blossom exhibit in the browser, the hardest problem wasn't drawing the petals. It was the text.

I wanted poem lines to drift down from the sky like blossoms — each one swaying, rotating, landing exactly in place. The problem: how do you know *where* "in place" is, if each line's position depends on how many characters fit per line at a given font size?

The naive answer is `ctx.measureText()`. But `measureText` only returns the width of a single run of text. For multi-line layout — wrapping, line heights, total block height — you're on your own. And the DOM-based alternative (`getBoundingClientRect`) triggers layout reflow: exactly what you don't want inside a 60 fps Canvas animation loop.

That's where [Pretext](https://github.com/chenglou/pretext) comes in.

---

## What Pretext does

Pretext is a pure JavaScript library for multiline text measurement and layout. Its key insight is a clean separation between the expensive work and the cheap work:

```js
import { prepareWithSegments, layoutWithLines } from '@chenglou/pretext';

// Expensive: done once. Segments the text, measures glyphs via canvas,
// builds a cache of widths. Returns an opaque handle.
const prepared = prepareWithSegments(text, font, { whiteSpace: 'pre-wrap' });

// Cheap: pure arithmetic over cached widths. No DOM. No canvas calls.
// Call this on every resize, or — in our case — to compute landing coords.
const { lines } = layoutWithLines(prepared, maxWidth, lineHeight);
```

`lines` is an array of `{ text, width }` objects — exactly one per rendered line. From those, you can compute where every line will land.

---

## The petal-fall pattern

Here's the core idea. Before the animation starts, measure the layout once:

```js
const POEM = '桃之夭夭，灼灼其华。\n之子于归，宜其室家。\n桃之夭夭，有蕡其实。\n之子于归，宜其家室。';
const FONT = '19px serif';
const LINE_HEIGHT = 32;
const MAX_WIDTH = 170;

const prepared = prepareWithSegments(POEM, FONT, { whiteSpace: 'pre-wrap' });
const { lines } = layoutWithLines(prepared, MAX_WIDTH, LINE_HEIGHT);

// Compute landing coordinates from Pretext output
const totalH  = lines.length * LINE_HEIGHT;
const anchorX = canvasWidth - 24;
const anchorY = canvasHeight - 52 - totalH;

// Each line becomes a physics particle with a known target
const particles = lines.map((line, i) => ({
  text: line.text,
  tx: anchorX,                           // ← Pretext-computed target X
  ty: anchorY + i * LINE_HEIGHT + LINE_HEIGHT * 0.78,  // ← target Y
  // Physics initial state
  x: anchorX + (Math.random() - 0.5) * 130,
  y: -24 - i * 20,
  rot: (Math.random() - 0.5) * 0.5,
  vx: (Math.random() - 0.5) * 1.1,
  vy: 1.6 + Math.random() * 1.2,
  vrot: (Math.random() - 0.5) * 0.03,
  phase: Math.random() * Math.PI * 2,   // for sinusoidal sway
  landed: false,
}));
```

During each animation frame, each particle follows a two-phase trajectory:

**Phase 1 — free fall**: sinusoidal horizontal sway + gentle rotation, like a real petal caught in a breeze.

**Phase 2 — spring homing**: once the particle comes within ~64px of its target, a spring-damper takes over and guides it smoothly to the landing spot.

```js
function spring(cur, target, vel, k, d) {
  const force = (target - cur) * k - vel * d;
  return { pos: cur + vel, vel: vel + force };
}

function tick(frame) {
  particles.forEach((p, i) => {
    const elapsed = frame - 140 - i * 18;  // staggered start
    if (elapsed <= 0) return;

    if (!p.landed) {
      const dist = Math.hypot(p.tx - p.x, p.ty - p.y);

      if (dist < 64) {
        // Spring phase: smooth convergence to Pretext's computed position
        const sx = spring(p.x, p.tx, p.vx, 0.12, 0.55);
        const sy = spring(p.y, p.ty, p.vy, 0.14, 0.58);
        p.x = sx.pos; p.vx = sx.vel;
        p.y = sy.pos; p.vy = sy.vel;
        p.vrot *= 0.82;
        if (dist < 1.5) { p.x = p.tx; p.y = p.ty; p.landed = true; }
      } else {
        // Free-fall phase: sway + rotate
        const sway = Math.sin(elapsed * 0.06 + p.phase) * 0.48;
        p.x += p.vx + sway;
        p.y += p.vy;
        p.rot += p.vrot;
      }
    } else {
      p.rot *= 0.88;  // residual tilt decays after landing
    }

    // Draw
    ctx.save();
    ctx.globalAlpha = Math.min(1, elapsed / 18);
    ctx.translate(p.x, p.y);
    ctx.rotate(p.rot);
    ctx.font = FONT;
    ctx.textAlign = 'right';
    ctx.strokeStyle = 'rgba(255,255,255,0.65)';
    ctx.lineWidth = 3;
    ctx.strokeText(p.text, 0, 0);
    ctx.fillStyle = 'rgba(45,28,24,0.92)';
    ctx.fillText(p.text, 0, 0);
    ctx.restore();
  });
}
```

The key detail: `p.tx` and `p.ty` come from Pretext's output. The animation doesn't guess or hardcode where lines land — it trusts the measurement library to tell it the truth.

---

## The museum label problem

The second demo extends this to a more common real-world case: a card whose height depends on how much text wraps inside it.

Without Pretext, the usual approach is to render the text into a hidden `<div>`, read its `offsetHeight`, then use that number to size a Canvas card. That's a DOM read + layout reflow every time the text or column width changes.

With Pretext, the height is computed before any rendering:

```js
const sections = [
  { text: 'Cherry Blossom Viewing Canopy', font: 'bold 14px Palatino,serif' },
  { text: 'Prunus serrulata · Rosaceae',   font: 'italic 12px Palatino,serif' },
  { text: 'Installed 2024. This specimen blooms for approximately two weeks each spring...', font: '13px Palatino,serif' },
  { text: 'Please do not approach.',        font: 'italic 13px Palatino,serif' },
].map(s => ({
  ...s,
  lines: layoutWithLines(prepareWithSegments(s.text, s.font), COLUMN_WIDTH, LINE_HEIGHT).lines,
}));

// Sum up the real card height from Pretext's line counts
let cardH = PADDING * 2;
sections.forEach((s, i) => {
  cardH += s.lines.length * LINE_HEIGHT;
  if (i < sections.length - 1) cardH += SECTION_GAP;
});
```

Now `cardH` is exact. The card border drawn around it always fits the content — at any font size, in any locale, with any body text length.

The card itself then falls with a tilt-and-spring animation, pivoting around its center:

```js
ctx.translate(cardCenterX, cardY + cardH / 2);
ctx.rotate(fall.rot);   // slight tilt during fall
ctx.translate(-cardCenterX, -(cardY + cardH / 2));
// ... draw card at (cardX, cardY) ...
```

---

## What Pretext brings to Canvas animation

A summary of where the library earns its keep:

| Situation | Without Pretext | With Pretext |
|-----------|-----------------|--------------|
| Multi-line text in Canvas | Guess widths with `measureText`, manual word-wrap loop | `layoutWithLines` gives exact lines and widths |
| Card/container height | Hidden DOM element + reflow | Pure arithmetic from line count |
| Landing coordinates for animation | Hardcoded or approximated | Computed once from layout, used as ground truth |
| Resize handling | Re-measure DOM on every resize | Re-run `layoutWithLines` (fast, no DOM) |
| CJK / mixed-script text | `measureText` is unreliable across fonts | Pretext handles segmentation per script |

The library's design — expensive `prepare()` once, cheap `layout()` many times — maps naturally onto the animation pattern: measure at init, animate at 60 fps.

---

## Live demo

👉 **[See both demos running →](https://wemamawe.github.io/wanxiang/pretext-demo.html)**

Source: [`pretext-demo.html`](https://github.com/wemamawe/wanxiang/blob/main/pretext-demo.html) — a single self-contained file with no build step, loading Pretext via `esm.sh`.

The full cherry blossom exhibit (six scenes, Web Audio, 3D sakura) lives at [wemamawe.github.io/wanxiang/home.html](https://wemamawe.github.io/wanxiang/home.html).

---

## Installation

```sh
npm install @chenglou/pretext
# or
bun add @chenglou/pretext
```

Or for a no-build HTML file:

```html
<script type="module">
  import { prepareWithSegments, layoutWithLines }
    from 'https://esm.sh/@chenglou/pretext';
  // ...
</script>
```

---

*Pretext is authored by [@chenglou](https://github.com/chenglou). The cherry blossom exhibit was built for fun — contributions and forks welcome.*
