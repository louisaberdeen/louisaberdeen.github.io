```{=html}
<div class="attn-demo" id="attn-demo">
  <div class="attn-row attn-controls">
    <span class="attn-label">Query token</span>
    <span id="attn-tokens"></span>
  </div>
  <div class="attn-row attn-controls">
    <label class="attn-label" for="attn-temp">Temperature τ</label>
    <input type="range" id="attn-temp" min="0" max="100" value="50">
    <output id="attn-temp-out"></output>
  </div>
  <div class="attn-scroll">
    <table class="attn-table">
      <thead><tr>
        <th>key</th><th>score</th><th>weight</th>
        <th colspan="4">value v</th><th></th><th colspan="4">weight × v</th>
      </tr></thead>
      <tbody id="attn-body"></tbody>
      <tfoot><tr>
        <td colspan="3" class="attn-sum-label">output = Σ weight × v</td>
        <td colspan="4"></td><td class="attn-eq">=</td>
        <td id="attn-out" colspan="4"></td>
      </tr></tfoot>
    </table>
  </div>
  <p class="attn-note">Each output column is the sum of the column above it. Turn τ down and almost all the weight lands
  on one key, which is a dictionary lookup. Turn it up and the output becomes a plain average of every value.</p>
</div>

<style>
.attn-demo { border:1px solid var(--surface); border-radius:8px; padding:1rem; margin:1.5rem 0; font-size:.9rem; }
.attn-demo .attn-row { display:flex; align-items:center; gap:.6rem; flex-wrap:wrap; margin-bottom:.6rem; }
.attn-demo .attn-label { color:var(--accent); min-width:7.5rem; }
.attn-demo button { background:var(--surface); color:var(--ink); border:1px solid var(--surface);
  border-radius:4px; padding:.15rem .6rem; margin:.1rem; cursor:pointer; font-family:inherit; }
.attn-demo button.on { background:var(--accent); color:var(--on-accent); border-color:var(--accent); }
.attn-demo input[type=range] { flex:1; min-width:8rem; max-width:18rem; accent-color:var(--accent); }
.attn-demo .attn-scroll { overflow-x:auto; }
.attn-demo table { border-collapse:separate; border-spacing:3px; margin:0; }
.attn-demo th { font-weight:normal; color:var(--accent2); text-align:center; padding:0 .3rem; border:0; }
.attn-demo td { padding:0 .3rem; border:0; text-align:right; font-variant-numeric:tabular-nums; white-space:nowrap; }
.attn-demo td.attn-tok { text-align:left; color:var(--ink); }
.attn-demo td.attn-cell { width:2.8rem; min-width:2.8rem; text-align:center; border-radius:3px; color:var(--ink); }
.attn-demo .attn-track { display:inline-block; width:60px; vertical-align:middle; }
.attn-demo td.attn-score { width:3rem; min-width:3rem; }
.attn-demo #attn-temp-out { display:inline-block; width:3rem; }
.attn-demo .attn-bar { display:block; height:.8rem; background:var(--accent); border-radius:2px; vertical-align:middle; }
.attn-demo .attn-w { display:inline-block; width:2.6rem; margin-right:.3rem; text-align:right; }
.attn-demo .attn-eq, .attn-demo .attn-sum-label { color:var(--accent); text-align:center; }
.attn-demo tfoot td { padding-top:.5rem; border-top:1px solid var(--surface); }
.attn-demo #attn-out { padding:0; }
.attn-demo #attn-out span { display:inline-block; width:2.8rem; margin-right:3px; text-align:center; border-radius:3px;
  outline:1px solid var(--accent); color:var(--ink); }
.attn-demo .attn-note { margin:.6rem 0 0; color:var(--accent2); }
</style>

<script>
(() => {
  const tokens = ["the", "cat", "sat", "on", "the", "mat"];
  // Hand-made toy vectors (d_k = 4) so the attention pattern is readable:
  // "cat" looks for a verb, "sat" for its subject, "mat" for its preposition, and so on.
  const K = [[1,0,0,0],[0,1,0,.3],[0,.4,1,0],[.3,0,0,1],[1,0,0,0],[0,.8,0,.6]];
  const Q = [[0,1,0,.5],[0,.3,1.2,0],[0,1.5,.3,0],[0,.8,0,1],[0,.6,0,.9],[.4,0,0,1.5]].map(r => r.map(x => 3*x));
  const V = [[.1,-.2,.3,0],[.9,.1,-.6,.4],[-.3,.8,.2,-.7],[.2,-.5,.9,.6],[.1,-.2,.3,0],[-.8,.4,.1,.9]];
  const dk = 4;
  let qi = 1;

  const $ = id => document.getElementById(id);
  const temp = () => Math.pow(10, -1.3 + 2.6 * $("attn-temp").value / 100); // 0.05 … 20
  // positive values in accent, negative in accent2
  const shade = x => x >= 0 ? `color-mix(in srgb, var(--accent) ${100 * Math.min(1, Math.abs(x)) * .85}%, transparent)`
                             : `color-mix(in srgb, var(--accent2) ${100 * Math.min(1, Math.abs(x)) * .85}%, transparent)`;
  const ink = x => Math.abs(x) > .5 ? "var(--on-accent)" : "var(--ink)"; // dark text once the cell is bright
  const fmt = x => (Math.abs(x) < .005 ? 0 : x).toFixed(2); // no "-0.00"
  const cell = x => `<td class="attn-cell" style="background:${shade(x)};color:${ink(x)}">${fmt(x)}</td>`;

  $("attn-tokens").innerHTML = tokens.map((t, i) => `<button data-i="${i}">${t}</button>`).join("");
  $("attn-tokens").addEventListener("click", e => { if (e.target.dataset.i) { qi = +e.target.dataset.i; draw(); } });
  $("attn-temp").addEventListener("input", draw);

  function draw() {
    const t = temp();
    $("attn-temp-out").textContent = t.toFixed(2);
    document.querySelectorAll("#attn-tokens button").forEach((b, i) => b.classList.toggle("on", i === qi));
    const scores = K.map(k => k.reduce((s, kj, j) => s + kj * Q[qi][j], 0) / Math.sqrt(dk));
    const m = Math.max(...scores);
    const e = scores.map(s => Math.exp((s - m) / t));
    const z = e.reduce((a, b) => a + b, 0);
    const w = e.map(x => x / z);
    const out = [0, 1, 2, 3].map(j => w.reduce((s, wi, i) => s + wi * V[i][j], 0));
    $("attn-body").innerHTML = tokens.map((tok, i) =>
      `<tr><td class="attn-tok">${tok}</td><td class="attn-score">${fmt(scores[i])}</td>` +
      `<td><span class="attn-w">${(100 * w[i]).toFixed(0)}%</span><span class="attn-track"><span class="attn-bar" style="width:${(60 * w[i]).toFixed(1)}px"></span></span></td>` +
      V[i].map(cell).join("") + `<td class="attn-eq">→</td>` + V[i].map(v => cell(w[i] * v)).join("") + `</tr>`
    ).join("");
    $("attn-out").innerHTML = out.map(x => `<span style="background:${shade(x)};color:${ink(x)}">${fmt(x)}</span>`).join("");
  }
  draw();
})();
</script>
```
