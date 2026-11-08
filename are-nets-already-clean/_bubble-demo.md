```{=html}
<div class="dim-demo">
  <svg id="bub-svg" viewBox="0 0 560 330" role="img" aria-label="Gaussian samples drawn at their normalised distance from the centre as the dimension d sweeps from 2 to 10,000. The dots start as a filled disc and collapse into a thin ring at radius 1."></svg>
  <p class="dim-note">Gaussian samples at distance ‖x‖ / √<i>d</i>.</p>
</div>

<script>
(() => {
  const svg = document.getElementById("bub-svg");
  const NS = "http://www.w3.org/2000/svg";
  const el = (tag, attrs, parent = svg) => {
    const e = document.createElementNS(NS, tag);
    for (const k in attrs) e.setAttribute(k, attrs[k]);
    parent.appendChild(e);
    return e;
  };
  const cx = 280, cy = 140, R = 52;           // R pixels = radius 1
  const D0 = 2, D1 = 10000, L = Math.log(D1 / D0);

  el("circle", { cx, cy, r: R, fill: "none", style: "stroke:var(--accent2)", "stroke-opacity": 0.7, "stroke-dasharray": "3 3" });

  // Each dot keeps a fixed angle and a fixed normal quantile z, so it moves smoothly as d changes.
  // ‖x‖² / d is chi-squared / d; Wilson–Hilferty: chi²_d / d ≈ (1 − 2/(9d) + z·√(2/(9d)))³
  let seed = 7;
  const rand = () => (seed = (seed * 16807) % 2147483647) / 2147483647;
  const gauss = () => Math.sqrt(-2 * Math.log(rand())) * Math.cos(2 * Math.PI * rand());
  const dots = Array.from({ length: 700 }, () => ({
    z: gauss(), c: Math.cos(2 * Math.PI * rand()), s: 0,
    e: el("circle", { r: 1.5, style: "fill:var(--accent)", "fill-opacity": 0.65 }),
  }));
  dots.forEach(p => { p.s = Math.sign(rand() - 0.5) * Math.sqrt(1 - p.c * p.c); });

  // log-scale track for d
  const tx0 = 120, tx1 = 440, ty = 302;
  const T = d => tx0 + (tx1 - tx0) * Math.log(d / D0) / L;
  el("line", { x1: tx0, x2: tx1, y1: ty, y2: ty, style: "stroke:var(--surface)", "stroke-width": 4, "stroke-linecap": "round" });
  for (const [d, lab] of [[2, "2"], [10, "10"], [100, "100"], [1000, "1k"], [10000, "10k"]]) {
    el("line", { x1: T(d), x2: T(d), y1: ty - 6, y2: ty + 6, style: "stroke:var(--ink)" });
    el("text", { x: T(d), y: ty + 20, "text-anchor": "middle", "font-size": 11 }).textContent = lab;
  }
  const dot = el("circle", { cy: ty, r: 6, style: "fill:var(--accent)" });
  const lab = el("text", { x: tx0 - 14, y: ty + 4, "text-anchor": "end" });

  const draw = d => {
    const k = 2 / (9 * d), a = 1 - k, b = Math.sqrt(k);
    for (const p of dots) {
      const r = Math.sqrt(Math.max(0, (a + b * p.z) ** 3)) * R;
      p.e.setAttribute("cx", (cx + r * p.c).toFixed(1));
      p.e.setAttribute("cy", (cy + r * p.s).toFixed(1));
    }
    dot.setAttribute("cx", T(d));
    lab.textContent = "d = " + Math.round(d).toLocaleString("en");
  };

  const SWEEP = 7000, HOLD = 1500, P = 2 * (SWEEP + HOLD);
  const ease = s => s * s * (3 - 2 * s);
  if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) { draw(1000); return; }
  const tick = now => {
    const t = now % P;
    const s = t < SWEEP ? t / SWEEP : t < SWEEP + HOLD ? 1 : t < 2 * SWEEP + HOLD ? 1 - (t - SWEEP - HOLD) / SWEEP : 0;
    draw(D0 * Math.exp(L * ease(s)));
    requestAnimationFrame(tick);
  };
  requestAnimationFrame(tick);
})();
</script>
```
