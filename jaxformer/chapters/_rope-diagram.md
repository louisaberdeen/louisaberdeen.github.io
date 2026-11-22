```{=html}
<div class="rope-demo">
  <svg id="rope-svg" viewBox="0 0 600 340" role="img" aria-label="The rotation matrix R for d = 8: four 2 by 2 blocks on the diagonal, zeros elsewhere. Each block holds a dial rotated by (n − m) times its own theta, as n − m sweeps from 0 to 500 and back."></svg>
  <p class="rope-note">R<sub>n−m</sub> for d = 8, base = 10000.</p>
</div>

<style>
.rope-demo { border:1px solid var(--surface); border-radius:8px; padding:1rem; margin:1.5rem 0; font-size:.9rem; }
.rope-demo svg { width:100%; height:auto; display:block; font-family:Lato, Helvetica, Arial, sans-serif; }
.rope-demo svg text { fill:var(--ink); font-size:12px; }
.rope-demo .rope-note { color:var(--accent2); font-size:.85rem; margin:.6rem 0 0; }
</style>

<script>
(() => {
  const svg = document.getElementById("rope-svg");
  const NS = "http://www.w3.org/2000/svg";
  const el = (tag, attrs, parent = svg) => {
    const e = document.createElementNS(NS, tag);
    for (const k in attrs) e.setAttribute(k, attrs[k]);
    parent.appendChild(e);
    return e;
  };
  const d = 8, base = 10000, C = 34, x0 = 40, y0 = 14;
  const colours = ["var(--accent)", "var(--accent2)", "#2aa198", "#d33682"];
  const thetas = Array.from({ length: d / 2 }, (_, i) => base ** (-2 * i / d));

  // off-diagonal zeros
  for (let r = 0; r < d; r++)
    for (let c = 0; c < d; c++)
      if (Math.floor(r / 2) !== Math.floor(c / 2)) {
        el("rect", { x: x0 + c * C + 1, y: y0 + r * C + 1, width: C - 2, height: C - 2, rx: 2, style: "fill:var(--surface)" });
        el("text", { x: x0 + c * C + C / 2, y: y0 + r * C + C / 2 + 4, "text-anchor": "middle", style: "fill:var(--muted)" }).textContent = "0";
      }

  // diagonal blocks, each with a dial
  const dials = thetas.map((theta, i) => {
    const bx = x0 + 2 * i * C, by = y0 + 2 * i * C, cx = bx + C, cy = by + C, R = C - 8;
    el("rect", { x: bx + 1, y: by + 1, width: 2 * C - 2, height: 2 * C - 2, rx: 3, style: `fill:${colours[i]}`, "fill-opacity": 0.2 });
    el("circle", { cx, cy, r: R, fill: "none", style: "stroke:var(--muted)" });
    el("line", { x1: cx, y1: cy, x2: cx + R, y2: cy, style: "stroke:var(--muted)", "stroke-dasharray": "3 3" });
    const hand = el("line", { x1: cx, y1: cy, style: `stroke:${colours[i]}`, "stroke-width": 2.5, "stroke-linecap": "round" });
    const tip = el("circle", { r: 3.5, style: `fill:${colours[i]}` });
    const label = el("text", { x: x0 + d * C + 30, y: cy + 4 });
    return { theta, cx, cy, R, hand, tip, label, i };
  });

  // n − m track
  const MAX = 500, tx0 = 120, tx1 = 440, ty = 318;
  const T = v => tx0 + (tx1 - tx0) * v / MAX;
  el("line", { x1: tx0, x2: tx1, y1: ty, y2: ty, style: "stroke:var(--surface)", "stroke-width": 4, "stroke-linecap": "round" });
  for (const v of [0, 100, 200, 300, 400, 500]) {
    el("line", { x1: T(v), x2: T(v), y1: ty - 6, y2: ty + 6, style: "stroke:var(--ink)" });
    el("text", { x: T(v), y: ty + 20, "text-anchor": "middle", "font-size": 11 }).textContent = v;
  }
  const dot = el("circle", { cy: ty, r: 6, style: "fill:var(--accent)" });
  const lab = el("text", { x: tx0 - 14, y: ty + 4, "text-anchor": "end" });

  const draw = off => {
    dot.setAttribute("cx", T(off));
    lab.textContent = "n − m = " + Math.round(off);
    for (const { theta, cx, cy, R, hand, tip, label, i } of dials) {
      const a = off * theta, x = cx + R * Math.cos(a), y = cy - R * Math.sin(a);
      hand.setAttribute("x2", x); hand.setAttribute("y2", y);
      tip.setAttribute("cx", x); tip.setAttribute("cy", y);
      const deg = ((a * 180 / Math.PI) % 360).toFixed(1);
      label.textContent = `θ${"₀₁₂₃"[i]} = ${+theta.toPrecision(3)}   →   ${deg}°`;
    }
  };
  // sweep n − m up and back with an ease, holding at each end
  const SWEEP = 10000, HOLD = 1500, P = 2 * (SWEEP + HOLD);
  const ease = s => s * s * (3 - 2 * s);
  if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) { draw(5); return; }
  const tick = now => {
    const t = now % P;
    const s = t < SWEEP ? t / SWEEP : t < SWEEP + HOLD ? 1 : t < 2 * SWEEP + HOLD ? 1 - (t - SWEEP - HOLD) / SWEEP : 0;
    draw(MAX * ease(s));
    requestAnimationFrame(tick);
  };
  requestAnimationFrame(tick);
})();
</script>
```
