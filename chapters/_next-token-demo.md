```{=html}
<div class="nt-demo" id="nt-demo">
  <div class="nt-row">
    <span class="nt-label">Sequence</span>
    <span id="nt-seq"></span>
    <button id="nt-new" class="nt-ghost">new sentence</button>
  </div>
  <div class="nt-row"><span class="nt-label">Model sees</span><span id="nt-ctx"></span></div>
  <div id="nt-bars"></div>
  <div class="nt-row nt-loss"><span class="nt-label">Loss here</span><span id="nt-loss"></span></div>
  <div class="nt-row">
    <span class="nt-label">Loss per token</span>
    <span id="nt-strip"></span>
    <span id="nt-mean"></span>
  </div>
</div>

<style>
.nt-demo { --horizon:#f8ba7d; --dusk:#84b4d8; --slate:#2f3c4b; --cream:#fdf6ef;
  border:1px solid var(--slate); border-radius:8px; padding:1rem; margin:1.5rem 0; font-size:.9rem; }
.nt-demo .nt-row { display:flex; align-items:center; gap:.6rem; flex-wrap:wrap; margin-bottom:.6rem; }
.nt-demo .nt-label { color:var(--horizon); min-width:7.5rem; }
.nt-demo button { background:var(--slate); color:var(--cream); border:1px solid var(--slate);
  border-radius:4px; padding:.15rem .6rem; margin:.1rem; cursor:pointer; font-family:inherit; }
.nt-demo button.on { background:var(--horizon); color:#121418; border-color:var(--horizon); }
.nt-demo button.next { border-color:var(--dusk); }
.nt-demo button.nt-ghost { background:none; color:var(--dusk); border-color:var(--dusk); margin-left:auto; }
.nt-demo #nt-ctx { color:var(--cream); }
.nt-demo #nt-ctx .dim { color:#6b7a8c; }
.nt-demo .nt-bar-row { display:flex; align-items:center; gap:.6rem; margin:.25rem 0; }
.nt-demo .nt-word { min-width:7.5rem; text-align:right; color:var(--cream); }
.nt-demo .nt-track { flex:0 1 22rem; height:1rem; position:relative; }
.nt-demo .nt-fill { height:100%; background:var(--horizon); border-radius:0 4px 4px 0; min-width:2px; }
.nt-demo .nt-bar-row.target .nt-fill { background:var(--dusk); }
.nt-demo .nt-pct { width:3rem; text-align:right; font-variant-numeric:tabular-nums; color:var(--cream); }
.nt-demo .nt-tag { color:var(--dusk); font-size:.8rem; white-space:nowrap; }
.nt-demo .nt-loss #nt-loss { font-variant-numeric:tabular-nums; color:var(--cream); }
.nt-demo #nt-strip { display:inline-flex; gap:3px; flex-wrap:wrap; }
.nt-demo #nt-strip span { display:inline-block; width:2.6rem; text-align:center; border-radius:3px; cursor:pointer;
  font-variant-numeric:tabular-nums; color:var(--cream); outline:1px solid var(--slate); }
.nt-demo #nt-strip span.on { outline:2px solid var(--horizon); }
.nt-demo #nt-mean { color:var(--horizon); font-variant-numeric:tabular-nums; }
</style>

<script>
(() => {
  const vocab = ["I", "like", "cats", "dogs"];
  // P[prev][next]: the toy "trained model" -- a bigram table over the 4-word vocabulary.
  const P = {
    I:    [.05, .85, .05, .05],
    like: [.05, .05, .55, .35],
    cats: [.70, .10, .10, .10],
    dogs: [.70, .10, .10, .10],
  };
  let seq = ["I", "like", "cats", "I", "like", "dogs", "I", "like", "cats"];
  let pos = 1;

  const $ = id => document.getElementById(id);
  const sample = p => { let r = Math.random(); for (let i = 0; i < p.length; i++) if ((r -= p[i]) < 0) return i; return p.length - 1; };
  const loss = t => -Math.log(P[seq[t]][vocab.indexOf(seq[t + 1])]);
  // darker cell = more surprised; capped at 3 nats so one bad token doesn't wash the rest out
  const heat = x => `rgba(248,186,125,${Math.min(1, x / 3) * .85})`;

  function draw() {
    const p = P[seq[pos]], next = seq[pos + 1];
    $("nt-seq").innerHTML = seq.map((w, i) =>
      `<button data-i="${i}" class="${i === pos ? "on" : i === pos + 1 ? "next" : ""}">${w}</button>`).join("");
    $("nt-ctx").innerHTML = seq.slice(0, pos + 1).map((w, i) => i === pos ? w : `<span class="dim">${w}</span>`).join(" ") + " → ?";
    $("nt-bars").innerHTML = vocab.map((w, j) =>
      `<div class="nt-bar-row${w === next ? " target" : ""}" title="p(${w} | ${seq[pos]}) = ${p[j].toFixed(2)}">` +
      `<span class="nt-word">${w}</span><span class="nt-track"><div class="nt-fill" style="width:${100 * p[j]}%"></div></span>` +
      `<span class="nt-pct">${(100 * p[j]).toFixed(0)}%</span>` +
      `<span class="nt-tag">${w === next ? "← actually next" : ""}</span></div>`).join("");
    $("nt-loss").textContent = next === undefined
      ? "last token: nothing to compare against, so no loss here"
      : `−log p(${next}) = −log ${p[vocab.indexOf(next)].toFixed(2)} = ${loss(pos).toFixed(2)} nats`;
    const losses = seq.slice(0, -1).map((_, t) => loss(t));
    $("nt-strip").innerHTML = losses.map((l, t) =>
      `<span data-i="${t}" class="${t === pos ? "on" : ""}" style="background:${heat(l)}" title="${seq[t]} → ${seq[t + 1]}">${l.toFixed(2)}</span>`).join("");
    $("nt-mean").textContent = `mean = ${(losses.reduce((a, b) => a + b, 0) / losses.length).toFixed(2)}`;
  }

  const pick = e => { if (e.target.dataset.i) { pos = +e.target.dataset.i; draw(); } };
  $("nt-seq").addEventListener("click", pick);
  $("nt-strip").addEventListener("click", pick);
  $("nt-new").addEventListener("click", () => {
    seq = ["I"];
    while (seq.length < 9) seq.push(vocab[sample(P[seq[seq.length - 1]])]);
    pos = Math.min(pos, seq.length - 1);
    draw();
  });
  draw();
})();
</script>
```
