```{=html}
<figure class="svg-fig">
<svg role="img" aria-label="Top: seq2seq, three encoder LSTM cells read the cat sat into a single context vector c, which three decoder LSTM cells turn into die Katze saß. Bottom: Bahdanau attention, every encoder cell feeds an attention block weighted by the previous decoder state, and its context output goes into the next decoder cell." xmlns="http://www.w3.org/2000/svg" viewBox="0 0 610 440" font-family="Lato, Helvetica, Arial, sans-serif" font-size="13">
<defs><marker id="s2s-a" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path style="fill:var(--ink)" d="M0 0L10 5L0 10z"/></marker></defs>
<text style="fill:var(--ink)" x="20" y="28" text-anchor="start" font-weight="bold">Seq2seq</text>
<rect x="30" y="80" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="58.0" y="100.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="110" y="80" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="138.0" y="100.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="190" y="80" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="218.0" y="100.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="360" y="80" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="388.0" y="100.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="440" y="80" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="468.0" y="100.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="520" y="80" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="548.0" y="100.5" text-anchor="middle" fill="#002b36">LSTM</text>
<path style="stroke:var(--ink)" d="M86 96H108" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M166 96H188" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M416 96H438" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M496 96H518" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<rect x="280" y="80" width="40" height="32" rx="6" fill="#cb4b16"/>
<text x="300.0" y="100.5" text-anchor="middle" fill="#002b36">c</text>
<path style="stroke:var(--ink)" d="M246 96H278" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M320 96H358" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="58.0" y="150" text-anchor="middle">the</text>
<path style="stroke:var(--ink)" d="M58.0 136V114" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="138.0" y="150" text-anchor="middle">cat</text>
<path style="stroke:var(--ink)" d="M138.0 136V114" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="218.0" y="150" text-anchor="middle">sat</text>
<path style="stroke:var(--ink)" d="M218.0 136V114" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="388.0" y="50" text-anchor="middle">die</text>
<path style="stroke:var(--ink)" d="M388.0 80V58" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="468.0" y="50" text-anchor="middle">Katze</text>
<path style="stroke:var(--ink)" d="M468.0 80V58" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="548.0" y="50" text-anchor="middle">saß</text>
<path style="stroke:var(--ink)" d="M548.0 80V58" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="300" y="130" text-anchor="middle">context vector</text>
<text style="fill:var(--ink)" x="138" y="172" text-anchor="middle">Encoder</text>
<text style="fill:var(--ink)" x="468" y="172" text-anchor="middle">Decoder</text>
<path style="stroke:var(--surface)" d="M20 196H590" stroke-width="2"/>
<text style="fill:var(--ink)" x="20" y="228" text-anchor="start" font-weight="bold">Bahdanau attention</text>
<rect x="30" y="340" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="58.0" y="360.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="110" y="340" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="138.0" y="360.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="190" y="340" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="218.0" y="360.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="360" y="340" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="388.0" y="360.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="440" y="340" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="468.0" y="360.5" text-anchor="middle" fill="#002b36">LSTM</text>
<rect x="520" y="340" width="56" height="32" rx="6" fill="#268bd2"/>
<text x="548.0" y="360.5" text-anchor="middle" fill="#002b36">LSTM</text>
<path style="stroke:var(--ink)" d="M86 356H108" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M166 356H188" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M416 356H438" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M496 356H518" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M246 356H358" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="58.0" y="410" text-anchor="middle">the</text>
<path style="stroke:var(--ink)" d="M58.0 396V374" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="138.0" y="410" text-anchor="middle">cat</text>
<path style="stroke:var(--ink)" d="M138.0 396V374" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="218.0" y="410" text-anchor="middle">sat</text>
<path style="stroke:var(--ink)" d="M218.0 396V374" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<rect x="250" y="250" width="100" height="36" rx="6" fill="#b58900"/>
<text x="300.0" y="272.5" text-anchor="middle" fill="#002b36">Attention</text>
<path style="stroke:var(--ink)" d="M58.0 338L270 288" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M138.0 338L290 288" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<path style="stroke:var(--ink)" d="M218.0 338L310 288" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="150" y="300" text-anchor="end">weights</text>
<path style="stroke:var(--ink)" d="M388.0 338L340 288" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="394" y="314" text-anchor="start">query</text>
<path style="stroke:var(--ink)" d="M350 268L460 338" fill="none" stroke-width="1.5" marker-end="url(#s2s-a)"/>
<text style="fill:var(--ink)" x="430" y="276" text-anchor="start">context</text>
<text style="fill:var(--ink)" x="138" y="432" text-anchor="middle">Encoder</text>
<text style="fill:var(--ink)" x="468" y="432" text-anchor="middle">Decoder</text>
</svg>
<figcaption>Seq2seq vs Bahdanau attention, after Sutskever et al. (2014) and Bahdanau et al. (2015).</figcaption>
</figure>
```

```{=html}
<style>
.svg-fig { margin:1.5rem auto; max-width:610px; }
.svg-fig svg { width:100%; height:auto; display:block; }
.svg-fig figcaption { color:var(--muted); font-size:.9rem; margin-top:.4rem; }
</style>
```
