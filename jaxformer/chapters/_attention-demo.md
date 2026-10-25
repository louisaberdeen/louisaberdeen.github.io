```{=html}
<div class="attn-demo" id="attn-demo">
  <div class="ad-row ad-controls">
    <span class="ad-label">Query token</span>
    <span id="ad-tokens"></span>
  </div>
  <div class="ad-row ad-controls">
    <label class="ad-label" for="ad-temp">Temperature τ</label>
    <input type="range" id="ad-temp" min="0" max="100" value="50">
    <output id="ad-temp-out"></output>
  </div>
  <div class="ad-scroll">
    <table class="ad-table">
      <thead><tr>
        <th>key</th><th>score</th><th>weight</th>
        <th colspan="4">value v</th><th></th><th colspan="4">weight × v</th>
      </tr></thead>
      <tbody id="ad-body"></tbody>
      <tfoot><tr>
        <td colspan="3" class="ad-sum-label">output = Σ weight × v</td>
        <td colspan="4"></td><td class="ad-eq">=</td>
        <td id="ad-out" colspan="4"></td>
      </tr></tfoot>
    </table>
  </div>
  <p class="ad-note">Each output column is the sum of the column above it. Turn τ down and almost all the weight lands
  on one key, which is a dictionary lookup. Turn it up and the output becomes a plain average of every value.</p>
</div>

<style>
.attn-demo { --peach:#edc19e; --horizon:#f8ba7d; --dusk:#84b4d8; --slate:#2f3c4b; --cream:#fdf6ef;
  border:1px solid var(--slate); border-radius:8px; padding:1rem; margin:1.5rem 0; font-size:.9rem; }
.attn-demo .ad-row { display:flex; align-items:center; gap:.6rem; flex-wrap:wrap; margin-bottom:.6rem; }
.attn-demo .ad-label { color:var(--horizon); min-width:7.5rem; }
.attn-demo button { background:var(--slate); color:var(--cream); border:1px solid var(--slate);
  border-radius:4px; padding:.15rem .6rem; margin:.1rem; cursor:pointer; font-family:inherit; }
.attn-demo button.on { background:var(--horizon); color:#121418; border-color:var(--horizon); }
.attn-demo input[type=range] { flex:1; min-width:8rem; max-width:18rem; accent-color:var(--horizon); }
.attn-demo .ad-scroll { overflow-x:auto; }
.attn-demo table { border-collapse:separate; border-spacing:3px; margin:0; }
.attn-demo th { font-weight:normal; color:var(--dusk); text-align:center; padding:0 .3rem; border:0; }
.attn-demo td { padding:0 .3rem; border:0; text-align:right; font-variant-numeric:tabular-nums; white-space:nowrap; }
.attn-demo td.ad-tok { text-align:left; color:var(--cream); }
.attn-demo td.ad-cell { width:2.8rem; min-width:2.8rem; text-align:center; border-radius:3px; color:var(--cream); }
.attn-demo .ad-track { display:inline-block; width:60px; vertical-align:middle; }
.attn-demo td.ad-score { width:3rem; min-width:3rem; }
.attn-demo #ad-temp-out { display:inline-block; width:3rem; }
.attn-demo .ad-bar { display:block; height:.8rem; background:var(--horizon); border-radius:2px; vertical-align:middle; }
.attn-demo .ad-w { display:inline-block; width:2.6rem; margin-right:.3rem; text-align:right; }
.attn-demo .ad-eq, .attn-demo .ad-sum-label { color:var(--horizon); text-align:center; }
.attn-demo tfoot td { padding-top:.5rem; border-top:1px solid var(--slate); }
.attn-demo #ad-out { padding:0; }
.attn-demo #ad-out span { display:inline-block; width:2.8rem; margin-right:3px; text-align:center; border-radius:3px;
  outline:1px solid var(--horizon); color:var(--cream); }
.attn-demo .ad-note { margin:.6rem 0 0; color:#afd2e4; }
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
  const temp = () => Math.pow(10, -1.3 + 2.6 * $("ad-temp").value / 100); // 0.05 … 20
  // positive values in sunset orange, negative in sky blue
  const shade = x => x >= 0 ? `rgba(248,186,125,${Math.min(1, Math.abs(x)) * .85})`
                             : `rgba(132,180,216,${Math.min(1, Math.abs(x)) * .85})`;
  const ink = x => Math.abs(x) > .5 ? "#121418" : "#fdf6ef"; // dark text once the cell is bright
  const fmt = x => (Math.abs(x) < .005 ? 0 : x).toFixed(2); // no "-0.00"
  const cell = x => `<td class="ad-cell" style="background:${shade(x)};color:${ink(x)}">${fmt(x)}</td>`;

  $("ad-tokens").innerHTML = tokens.map((t, i) => `<button data-i="${i}">${t}</button>`).join("");
  $("ad-tokens").addEventListener("click", e => { if (e.target.dataset.i) { qi = +e.target.dataset.i; draw(); } });
  $("ad-temp").addEventListener("input", draw);

  function draw() {
    const t = temp();
    $("ad-temp-out").textContent = t.toFixed(2);
    document.querySelectorAll("#ad-tokens button").forEach((b, i) => b.classList.toggle("on", i === qi));
    const scores = K.map(k => k.reduce((s, kj, j) => s + kj * Q[qi][j], 0) / Math.sqrt(dk));
    const m = Math.max(...scores);
    const e = scores.map(s => Math.exp((s - m) / t));
    const z = e.reduce((a, b) => a + b, 0);
    const w = e.map(x => x / z);
    const out = [0, 1, 2, 3].map(j => w.reduce((s, wi, i) => s + wi * V[i][j], 0));
    $("ad-body").innerHTML = tokens.map((tok, i) =>
      `<tr><td class="ad-tok">${tok}</td><td class="ad-score">${fmt(scores[i])}</td>` +
      `<td><span class="ad-w">${(100 * w[i]).toFixed(0)}%</span><span class="ad-track"><span class="ad-bar" style="width:${(60 * w[i]).toFixed(1)}px"></span></span></td>` +
      V[i].map(cell).join("") + `<td class="ad-eq">→</td>` + V[i].map(v => cell(w[i] * v)).join("") + `</tr>`
    ).join("");
    $("ad-out").innerHTML = out.map(x => `<span style="background:${shade(x)};color:${ink(x)}">${fmt(x)}</span>`).join("");
  }
  draw();
})();
</script>
```
