```{=html}
<figure class="mh-fig">
<svg viewBox="0 0 600 270" role="img" aria-label="Top: one head, where Q, K and V are each n by d, giving a single n by n attention map and an n by d output. Bottom: four heads, where Q, K and V are each split into four n by d/4 slices, giving four n by n attention maps whose outputs are concatenated back to n by d and mixed by W_O.">
<defs><marker id="mh-a" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0 0L10 5L0 10z" style="fill:var(--ink)"/></marker></defs>
<text x="20" y="26" text-anchor="start">One head</text>
<rect x="40" y="40" width="72" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.45"/>
<text x="76" y="75" text-anchor="middle">Q</text>
<text x="76" y="118" text-anchor="middle">n × d</text>
<rect x="130" y="40" width="72" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.45"/>
<text x="166" y="75" text-anchor="middle">K</text>
<text x="166" y="118" text-anchor="middle">n × d</text>
<rect x="220" y="40" width="72" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.45"/>
<text x="256" y="75" text-anchor="middle">V</text>
<text x="256" y="118" text-anchor="middle">n × d</text>
<path d="M300 70H338" class="mh-line" marker-end="url(#mh-a)"/>
<rect x="345" y="40" width="60" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.45"/>
<text x="375" y="118" text-anchor="middle">n × n</text>
<path d="M410 70H443" class="mh-line" marker-end="url(#mh-a)"/>
<rect x="450" y="40" width="72" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.45"/>
<text x="486" y="75" text-anchor="middle">out</text>
<text x="486" y="118" text-anchor="middle">n × d</text>
<text x="20" y="156" text-anchor="start">Four heads</text>
<rect x="40" y="170" width="18" height="60" rx="3" style="fill:var(--accent)" fill-opacity="0.55"/>
<rect x="58" y="170" width="18" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.55"/>
<rect x="76" y="170" width="18" height="60" rx="3" style="fill:#2aa198" fill-opacity="0.55"/>
<rect x="94" y="170" width="18" height="60" rx="3" style="fill:#d33682" fill-opacity="0.55"/>
<text x="76" y="205" text-anchor="middle">Q</text>
<text x="76" y="248" text-anchor="middle">4 × (n × d/4)</text>
<rect x="130" y="170" width="18" height="60" rx="3" style="fill:var(--accent)" fill-opacity="0.55"/>
<rect x="148" y="170" width="18" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.55"/>
<rect x="166" y="170" width="18" height="60" rx="3" style="fill:#2aa198" fill-opacity="0.55"/>
<rect x="184" y="170" width="18" height="60" rx="3" style="fill:#d33682" fill-opacity="0.55"/>
<text x="166" y="205" text-anchor="middle">K</text>
<text x="166" y="248" text-anchor="middle">4 × (n × d/4)</text>
<rect x="220" y="170" width="18" height="60" rx="3" style="fill:var(--accent)" fill-opacity="0.55"/>
<rect x="238" y="170" width="18" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.55"/>
<rect x="256" y="170" width="18" height="60" rx="3" style="fill:#2aa198" fill-opacity="0.55"/>
<rect x="274" y="170" width="18" height="60" rx="3" style="fill:#d33682" fill-opacity="0.55"/>
<text x="256" y="205" text-anchor="middle">V</text>
<text x="256" y="248" text-anchor="middle">4 × (n × d/4)</text>
<path d="M300 200H338" class="mh-line" marker-end="url(#mh-a)"/>
<rect x="345" y="170" width="29" height="29" rx="3" style="fill:var(--accent)" fill-opacity="0.55"/>
<rect x="376" y="170" width="29" height="29" rx="3" style="fill:var(--accent2)" fill-opacity="0.55"/>
<rect x="345" y="201" width="29" height="29" rx="3" style="fill:#2aa198" fill-opacity="0.55"/>
<rect x="376" y="201" width="29" height="29" rx="3" style="fill:#d33682" fill-opacity="0.55"/>
<text x="375" y="248" text-anchor="middle">4 × (n × n)</text>
<path d="M410 200H443" class="mh-line" marker-end="url(#mh-a)"/>
<rect x="450" y="170" width="18" height="60" rx="3" style="fill:var(--accent)" fill-opacity="0.55"/>
<rect x="468" y="170" width="18" height="60" rx="3" style="fill:var(--accent2)" fill-opacity="0.55"/>
<rect x="486" y="170" width="18" height="60" rx="3" style="fill:#2aa198" fill-opacity="0.55"/>
<rect x="504" y="170" width="18" height="60" rx="3" style="fill:#d33682" fill-opacity="0.55"/>
<text x="486" y="205" text-anchor="middle">concat</text>
<path d="M525 200H545" class="mh-line" marker-end="url(#mh-a)"/>
<text x="568" y="205" text-anchor="middle">W<tspan baseline-shift="sub" font-size="9">O</tspan></text>
<text x="486" y="248" text-anchor="middle">n × d</text>
</svg>
<figcaption>One head vs four. Same total width d, split into four d/4 slices, each with its own attention pattern.</figcaption>
</figure>
<style>
.mh-fig { margin:1.5rem 0; }
.mh-fig svg { width:100%; height:auto; display:block; font-family:Lato, Helvetica, Arial, sans-serif; }
.mh-fig text { fill:var(--ink); font-size:12px; }
.mh-fig .mh-line { fill:none; stroke:var(--ink); stroke-width:1.5; }
.mh-fig figcaption { color:var(--muted); font-size:.9rem; margin-top:.4rem; }
</style>
```
