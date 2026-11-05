```{=html}
<div class="dim-demo">
  <svg id="ang-svg" viewBox="0 0 560 300" role="img" aria-label="Distribution of the angle between two random unit vectors as the dimension d sweeps from 2 to 10,000. The curve starts flat and narrows into a spike at 90 degrees."></svg>
  <p class="dim-note">The angle between two random unit vectors as <i>d</i> sweeps from 2 to 10,000, evenly on a log scale.
  In 2D every angle is equally likely; as <i>d</i> grows the angle piles up at 90°. The faint curves mark each decade.</p>
</div>

<style>
.dim-demo { border:1px solid #2f3c4b; border-radius:8px; padding:1rem; margin:1.5rem 0; }
.dim-demo svg { width:100%; height:auto; display:block; font-family:Lato, Helvetica, Arial, sans-serif; }
.dim-demo svg text { fill:#c9d4dc; font-size:12px; }
.dim-demo .dim-note { color:#afd2e4; font-size:.85rem; margin:.6rem 0 0; }
</style>

<script>
(() => {
  const svg = document.getElementById("ang-svg");
  const NS = "http://www.w3.org/2000/svg";
  const el = (tag, attrs, parent = svg) => {
    const e = document.createElementNS(NS, tag);
    for (const k in attrs) e.setAttribute(k, attrs[k]);
    parent.appendChild(e);
    return e;
  };
  const x0 = 40, x1 = 520, y0 = 30, y1 = 200;
  const X = deg => x0 + (x1 - x0) * deg / 180;
  const Y = v => y1 - (y1 - y0) * v * 0.95;
  const D0 = 2, D1 = 10000, L = Math.log(D1 / D0);

  // axes
  for (const deg of [0, 45, 90, 135, 180]) {
    el("line", { x1: X(deg), x2: X(deg), y1: y0, y2: y1, stroke: "#2f3c4b" });
    el("text", { x: X(deg), y: y1 + 18, "text-anchor": "middle" }).textContent = deg + "°";
  }
  el("line", { x1: x0, x2: x1, y1: y1, y2: y1, stroke: "#c9d4dc" });
  el("text", { x: (x0 + x1) / 2, y: y1 + 38, "text-anchor": "middle" }).textContent = "angle between two random unit vectors";
  el("text", { x: 18, y: (y0 + y1) / 2, "text-anchor": "middle", transform: `rotate(-90 18 ${(y0 + y1) / 2})` }).textContent = "how often";

  // p(θ) ∝ sin(θ)^(d-2), scaled to peak 1
  const N = 1800;
  const curve = d => {
    let s = "";
    for (let i = 0; i <= N; i++) {
      const t = Math.PI * i / N, v = Math.pow(Math.sin(t), d - 2);
      s += (i ? "L" : "M") + X(180 * i / N).toFixed(1) + " " + Y(v).toFixed(1);
    }
    return s;
  };
  for (const d of [2, 10, 100, 1000, 10000])
    el("path", { d: curve(d), fill: "none", stroke: "#84b4d8", "stroke-opacity": 0.25, "stroke-width": 1 });
  const live = el("path", { fill: "none", stroke: "#f8ba7d", "stroke-width": 2 });

  // log-scale track for d
  const tx0 = 120, tx1 = 440, ty = 272;
  const T = d => tx0 + (tx1 - tx0) * Math.log(d / D0) / L;
  el("line", { x1: tx0, x2: tx1, y1: ty, y2: ty, stroke: "#2f3c4b", "stroke-width": 4, "stroke-linecap": "round" });
  for (const [d, lab] of [[2, "2"], [10, "10"], [100, "100"], [1000, "1k"], [10000, "10k"]]) {
    el("line", { x1: T(d), x2: T(d), y1: ty - 6, y2: ty + 6, stroke: "#c9d4dc" });
    el("text", { x: T(d), y: ty + 20, "text-anchor": "middle", "font-size": 11 }).textContent = lab;
  }
  const dot = el("circle", { cy: ty, r: 6, fill: "#f8ba7d" });
  const lab = el("text", { x: tx0 - 14, y: ty + 4, "text-anchor": "end" });

  const draw = d => {
    live.setAttribute("d", curve(d));
    dot.setAttribute("cx", T(d));
    lab.textContent = "d = " + Math.round(d).toLocaleString("en");
  };

  // sweep log d up and back with an ease, holding at each end
  const SWEEP = 7000, HOLD = 1500, P = 2 * (SWEEP + HOLD);
  const ease = s => s * s * (3 - 2 * s);
  if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) { draw(1000); return; }
  const tick = now => {
    const t = now % P;
    let s = t < SWEEP ? t / SWEEP : t < SWEEP + HOLD ? 1 : t < 2 * SWEEP + HOLD ? 1 - (t - SWEEP - HOLD) / SWEEP : 0;
    draw(D0 * Math.exp(L * ease(s)));
    requestAnimationFrame(tick);
  };
  requestAnimationFrame(tick);
})();
</script>
```
