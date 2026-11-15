```{=html}
<figure class="svg-fig">
<svg role="img" aria-label="Transformer architecture: an encoder stack of multi-head attention and feed-forward layers with residual add-and-norm, feeding a decoder stack of masked attention, cross-attention and feed-forward layers, then linear and softmax." xmlns="http://www.w3.org/2000/svg" viewBox="10 0 570 620" font-family="Lato, Helvetica, Arial, sans-serif" font-size="13">
<defs><marker id="tf-a" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path style="fill:var(--ink)" d="M0 0L10 5L0 10z"/></marker></defs>
<rect style="fill:var(--surface)" x="65" y="262" width="210" height="218" rx="10"/>
<rect style="fill:var(--surface)" x="305" y="136" width="200" height="344" rx="10"/>
<text style="fill:var(--ink)" x="55" y="375" text-anchor="end">N×</text>
<text style="fill:var(--ink)" x="515" y="310" text-anchor="start">N×</text>
<rect x="95" y="540" width="150" height="36" rx="6" fill="#859900"/>
<text x="170" y="555.0" text-anchor="middle" fill="#002b36">Input</text>
<text x="170" y="569.0" text-anchor="middle" fill="#002b36">Embedding</text>
<rect x="95" y="420" width="150" height="44" rx="6" fill="#b58900"/>
<text x="170" y="439.0" text-anchor="middle" fill="#002b36">Multi-Head</text>
<text x="170" y="453.0" text-anchor="middle" fill="#002b36">Attention</text>
<rect style="fill:var(--ink)" x="95" y="374" width="150" height="24" rx="6"/>
<text style="fill:var(--bs-body-bg)" x="170" y="390.0" text-anchor="middle">Add &amp; Norm</text>
<rect x="95" y="318" width="150" height="40" rx="6" fill="#268bd2"/>
<text x="170" y="335.0" text-anchor="middle" fill="#002b36">Feed</text>
<text x="170" y="349.0" text-anchor="middle" fill="#002b36">Forward</text>
<rect style="fill:var(--ink)" x="95" y="276" width="150" height="24" rx="6"/>
<text style="fill:var(--bs-body-bg)" x="170" y="292.0" text-anchor="middle">Add &amp; Norm</text>
<text style="fill:var(--ink)" x="170" y="610" text-anchor="middle">Inputs</text>
<path style="stroke:var(--ink)" d="M170 596V578" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 540V521" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<circle style="stroke:var(--ink)" cx="170" cy="510" r="9" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M165 510H175M170 505V515" fill="none" stroke-width="1.5"/>
<circle style="stroke:var(--ink)" cx="100" cy="510" r="11" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M92 510C95 500 99 500 100 510S105 520 108 510" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M111 510H159" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<text style="fill:var(--ink)" x="82" y="506" text-anchor="end">Positional</text>
<text style="fill:var(--ink)" x="82" y="520" text-anchor="end">Encoding</text>
<path style="stroke:var(--ink)" d="M170 501V488M130 488H210" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M130 488V466" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 488V466" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M210 488V466" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 488H80V386H93" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 420V400" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 374V360" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 366H80V288H93" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 318V302" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<rect x="325" y="540" width="150" height="36" rx="6" fill="#859900"/>
<text x="400" y="555.0" text-anchor="middle" fill="#002b36">Output</text>
<text x="400" y="569.0" text-anchor="middle" fill="#002b36">Embedding</text>
<rect x="325" y="420" width="150" height="44" rx="6" fill="#b58900"/>
<text x="400" y="432.0" text-anchor="middle" fill="#002b36">Masked</text>
<text x="400" y="446.0" text-anchor="middle" fill="#002b36">Multi-Head</text>
<text x="400" y="460.0" text-anchor="middle" fill="#002b36">Attention</text>
<rect style="fill:var(--ink)" x="325" y="374" width="150" height="24" rx="6"/>
<text style="fill:var(--bs-body-bg)" x="400" y="390.0" text-anchor="middle">Add &amp; Norm</text>
<rect x="325" y="308" width="150" height="44" rx="6" fill="#b58900"/>
<text x="400" y="327.0" text-anchor="middle" fill="#002b36">Multi-Head</text>
<text x="400" y="341.0" text-anchor="middle" fill="#002b36">Attention</text>
<rect style="fill:var(--ink)" x="325" y="262" width="150" height="24" rx="6"/>
<text style="fill:var(--bs-body-bg)" x="400" y="278.0" text-anchor="middle">Add &amp; Norm</text>
<rect x="325" y="196" width="150" height="40" rx="6" fill="#268bd2"/>
<text x="400" y="213.0" text-anchor="middle" fill="#002b36">Feed</text>
<text x="400" y="227.0" text-anchor="middle" fill="#002b36">Forward</text>
<rect style="fill:var(--ink)" x="325" y="150" width="150" height="24" rx="6"/>
<text style="fill:var(--bs-body-bg)" x="400" y="166.0" text-anchor="middle">Add &amp; Norm</text>
<rect x="325" y="88" width="150" height="28" rx="6" fill="#2aa198"/>
<text x="400" y="106.0" text-anchor="middle" fill="#002b36">Linear</text>
<rect x="325" y="40" width="150" height="28" rx="6" fill="#2aa198"/>
<text x="400" y="58.0" text-anchor="middle" fill="#002b36">Softmax</text>
<text style="fill:var(--ink)" x="400" y="610" text-anchor="middle">Outputs (shifted right)</text>
<path style="stroke:var(--ink)" d="M400 596V578" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 540V521" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<circle style="stroke:var(--ink)" cx="400" cy="510" r="9" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M395 510H405M400 505V515" fill="none" stroke-width="1.5"/>
<circle style="stroke:var(--ink)" cx="470" cy="510" r="11" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M462 510C465 500 469 500 470 510S475 520 478 510" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M459 510H411" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<text style="fill:var(--ink)" x="488" y="506" text-anchor="start">Positional</text>
<text style="fill:var(--ink)" x="488" y="520" text-anchor="start">Encoding</text>
<path style="stroke:var(--ink)" d="M400 501V488M360 488H440" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M360 488V466" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 488V466" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M440 488V466" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 488H490V386H477" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 420V400" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M440 374V354" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M440 364H490V274H477" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M170 276V250H290V364H400" fill="none" stroke-width="1.5"/>
<path style="stroke:var(--ink)" d="M360 364V354" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 364V354" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 308V288" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 262V238" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 250H490V162H477" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 196V176" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 150V118" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 88V70" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<path style="stroke:var(--ink)" d="M400 40V24" fill="none" stroke-width="1.5" marker-end="url(#tf-a)"/>
<text style="fill:var(--ink)" x="400" y="14" text-anchor="middle">Output Probabilities</text>
</svg>
<figcaption>The Transformer, redrawn from Figure 1 of Vaswani et al. (2017). Encoder on the left, decoder on the right.</figcaption>
</figure>
```

```{=html}
<style>
.svg-fig { margin:1.5rem auto; max-width:610px; }
.svg-fig svg { width:100%; height:auto; display:block; }
.svg-fig figcaption { color:var(--muted); font-size:.9rem; margin-top:.4rem; }
</style>
```
