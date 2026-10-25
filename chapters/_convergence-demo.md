```{=html}
<div class="cv-demo" id="cv-demo">
  <div class="cv-row">
    <span class="cv-label">Context</span>
    <span id="cv-ctx"></span>
  </div>
  <div class="cv-row"><span class="cv-label">Samples seen</span><span id="cv-n"></span></div>
  <div class="cv-legend">
    <span><i class="cv-key true"></i>true distribution p*</span>
    <span><i class="cv-key model"></i>model q fitted to the samples</span>
  </div>
  <div id="cv-bars"></div>
  <svg id="cv-chart" viewBox="0 0 520 170" role="img" aria-label="Cross entropy of the fitted model against number of samples, approaching the entropy floor"></svg>
</div>

<style>
.cv-demo { --horizon:#f8ba7d; --dusk:#84b4d8; --slate:#2f3c4b; --cream:#fdf6ef;
  border:1px solid var(--slate); border-radius:8px; padding:1rem; margin:1.5rem 0; font-size:.9rem; }
.cv-demo .cv-row { display:flex; align-items:center; gap:.6rem; flex-wrap:wrap; margin-bottom:.6rem; }
.cv-demo .cv-label { color:var(--horizon); min-width:7.5rem; }
.cv-demo .cv-chip { background:var(--slate); color:var(--cream); border-radius:4px; padding:.15rem .6rem; margin:.1rem;
  transition:background .3s, color .3s; }
.cv-demo .cv-chip.on { background:var(--horizon); color:#121418; }
.cv-demo #cv-n { color:var(--cream); font-variant-numeric:tabular-nums; }
.cv-demo .cv-legend { display:flex; gap:1.2rem; flex-wrap:wrap; margin:.2rem 0 .5rem; color:var(--cream); font-size:.85rem; }
.cv-demo .cv-key { display:inline-block; width:.8rem; height:.8rem; border-radius:2px; margin-right:.35rem; vertical-align:-1px; }
.cv-demo .cv-key.true { background:var(--dusk); }
.cv-demo .cv-key.model { background:var(--horizon); }
.cv-demo .cv-word-row { display:flex; align-items:center; gap:.6rem; margin:.35rem 0; }
.cv-demo .cv-word { min-width:7.5rem; text-align:right; color:var(--cream); }
.cv-demo .cv-pair { flex:0 1 22rem; display:flex; flex-direction:column; gap:2px; }
.cv-demo .cv-fill { height:.6rem; border-radius:0 4px 4px 0; min-width:2px; transition:width .35s ease-out; }
.cv-demo .cv-fill.true { background:var(--dusk); }
.cv-demo .cv-fill.model { background:var(--horizon); }
.cv-demo .cv-pcts { width:7rem; color:var(--cream); font-variant-numeric:tabular-nums; font-size:.8rem; line-height:1.15; }
.cv-demo #cv-chart { width:100%; max-width:520px; display:block; margin-top:.8rem; }
.cv-demo #cv-chart text { fill:#afd2e4; font-size:11px; font-family:inherit; }
</style>

<script>
(() => {
  const vocab = ["I", "like", "cats", "dogs"];
  // Same bigram "true language" as the next-token demo above.
  const P = {
    I:    [.05, .85, .05, .05],
    like: [.05, .05, .55, .35],
    cats: [.70, .10, .10, .10],
    dogs: [.70, .10, .10, .10],
  };
  const MAX_N = 1000, CYCLE = 7000, GROW = 5500;  // ms per context; N grows 1 -> MAX_N over GROW, then holds
  const still = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
  let ci = 0, counts, hist, t0;

  const $ = id => document.getElementById(id);
  const ctx = () => vocab[ci];
  const sample = p => { let r = Math.random(); for (let i = 0; i < p.length; i++) if ((r -= p[i]) < 0) return i; return p.length - 1; };
  const total = () => counts.reduce((a, b) => a + b, 0);
  const H = p => -p.reduce((s, x) => s + x * Math.log(x), 0);
  // cross entropy H(p*, q): the fitted model's expected loss on a fresh next token
  const crossEntropy = (p, q) => p.reduce((s, x, i) => s - x * Math.log(q[i]), 0);
  // add-one smoothed frequencies: uniform at N = 0, never exactly 0, -> plain frequencies as N grows
  const model = () => { const N = total(); return counts.map(c => (c + 1) / (N + vocab.length)); };

  function addUpTo(n) {
    const p = P[ctx()];
    while (total() < n) {
      counts[sample(p)]++;
      hist.push([total(), crossEntropy(p, model())]);
    }
  }

  function chart(p) {
    const W = 520, Hh = 170, l = 44, r = 22, t = 10, b = 42;
    const floor = H(p), xMax = Math.log10(MAX_N);
    // fixed y range per context so the axis doesn't jitter while animating
    const yMax = floor + .8, yMin = Math.max(0, floor - .05);
    const x = N => l + (W - l - r) * Math.log10(N) / xMax;
    const y = L => t + (Hh - t - b) * (1 - (Math.min(L, yMax) - yMin) / (yMax - yMin));
    const ticks = [];
    for (let e = 0; e <= xMax; e++)
      ticks.push(`<line x1="${x(10 ** e)}" x2="${x(10 ** e)}" y1="${t}" y2="${Hh - b}" stroke="#2f3c4b"/>` +
                 `<text x="${x(10 ** e)}" y="${Hh - b + 26}" text-anchor="middle">${10 ** e}</text>`);
    const path = hist.map(([N, L], i) => `${i ? "L" : "M"}${x(N).toFixed(1)},${y(L).toFixed(1)}`).join("");
    const last = hist[hist.length - 1];
    $("cv-chart").innerHTML =
      ticks.join("") +
      `<line x1="${l}" x2="${W - r}" y1="${y(floor)}" y2="${y(floor)}" stroke="#84b4d8" stroke-dasharray="4 4"/>` +
      `<text x="${W - r}" y="${y(floor) + 14}" text-anchor="end">entropy floor H(p*) = ${floor.toFixed(2)}</text>` +
      `<path d="${path}" fill="none" stroke="#f8ba7d" stroke-width="2"/>` +
      (last ? `<circle cx="${x(last[0])}" cy="${y(last[1])}" r="4" fill="#f8ba7d"/>` : "") +
      `<text x="${l - 6}" y="${y(yMax) + 4}" text-anchor="end">${yMax.toFixed(1)}</text>` +
      `<text x="${l - 6}" y="${y(yMin) + 4}" text-anchor="end">${yMin.toFixed(1)}</text>` +
      `<text x="${(l + W - r) / 2}" y="${Hh - 2}" text-anchor="middle">samples seen (log scale)</text>` +
      `<text transform="translate(11 ${(t + Hh - b) / 2}) rotate(-90)" text-anchor="middle">cross entropy H(p*, q) (nats)</text>`;
  }

  // Build chips and bars once and only update widths/text per frame, so the CSS width transition can smooth them.
  $("cv-ctx").innerHTML = vocab.map(w => `<span class="cv-chip">after “${w}”</span>`).join("");
  $("cv-bars").innerHTML = vocab.map(w =>
    `<div class="cv-word-row"><span class="cv-word">${w}</span><span class="cv-pair">` +
    `<div class="cv-fill true"></div><div class="cv-fill model"></div></span><span class="cv-pcts"></span></div>`).join("");
  const chips = [...$("cv-ctx").children], rows = [...$("cv-bars").children];

  function draw() {
    const p = P[ctx()], N = total(), q = model();
    chips.forEach((c, i) => c.classList.toggle("on", i === ci));
    $("cv-n").textContent = `N = ${N}  ·  cross entropy = ${crossEntropy(p, q).toFixed(3)} nats`;
    rows.forEach((row, j) => {
      const [t, m] = row.querySelectorAll(".cv-fill");
      t.style.width = `${100 * p[j]}%`;
      m.style.width = `${100 * q[j]}%`;
      row.querySelector(".cv-pcts").innerHTML = `${(100 * p[j]).toFixed(0)}%<br>${(100 * q[j]).toFixed(1)}%`;
    });
    chart(p);
  }

  function start(now) { counts = [0, 0, 0, 0]; hist = []; t0 = now; }

  function tick(now) {
    if (now - t0 >= CYCLE) { ci = (ci + 1) % vocab.length; start(now); }
    // exponential growth: N = MAX_N^(t/GROW), so each decade of samples gets equal screen time
    addUpTo(still ? MAX_N : Math.round(MAX_N ** Math.min(1, (now - t0) / GROW)));
    draw();
    requestAnimationFrame(tick);
  }

  start(performance.now());
  requestAnimationFrame(tick);
})();
</script>
```
