# Attention and the transformer
## The attention mechanism

Transformers are built entirely around the attention mechanism. Attention uses three inputs: Queries, Keys, and Values. A useful analogy is to how in programming we have dictionaries where you have a query (the thing you're matching against), which you compare against keys (what you have), to get values (what you want). Rather than direct equality between query and key a similarity score is computed, and rather than returning one value you return a weighted sum over all of them. Basically using a dictionary is like a hard lookup whereas attention is doing a soft lookup. From an input of $X$ we project a $Q$, $K$, and $V$ matrix.

$$
\begin{aligned}
\text{scores} &= Q K^T / \sqrt{d_k}   \\
\text{weights} &= \text{softmax}(\text{scores}) \\
\text{attention} &= \text{weights} \cdot V           
\end{aligned}
$$

This has the nice property where every token can attend to any other token. I think of Query being the relevant information the token is looking for, the Key being what a token represents itself as so other tokens can find it, and the Value is the actual information handed over. Whereas before our recurrent models were bottlenecked by their iterative multiplication attention allows the model to represent relationships arbitrarily far apart (While this is true in theory we will see in practice there are constraints on how far this can go). RNNs are also bottlenecked by the amount of information they can retain in their hidden dimension, there is only so much information that can be stored in 4096 values right, as well as the backpropagation through time relying on sequential computation. 

{{< include _attention-demo.md >}}

It is worth highlighting the $\sqrt{d_k}$ normalisation in the attention calculation since to me it was not immediately obvious why we scale by the dimension axis rather than unit normalise. Basically as the number of dimensions grows the expected value of a random dot product stays 0 but the variance scales with $d_k$.

To see why consider this non-intuitive idea: In 2D two random directions often point in similar directions. In 10,000D, there are so many directions that two random vectors are almost always at ~90°. The Johnson–Lindenstrauss theorem states you can put $n$ points into $k = O(\log n / \varepsilon^2)$ dimensions and keep all distances within a factor $1 \pm \varepsilon$, which you can invert to get $n = e^{O(\varepsilon^2 k)}$ nearly orthogonal directions in $k$ dimensions.

{{< include _angle-demo.md >}}

The sum of many small independent effects is Gaussian. The dot product of two random unit vectors is a sum of $d$ small terms, so $u \cdot v \sim \mathcal{N}(0, 1/d)$ and the angle concentrates at 90°. A Gaussian in high dimensions is like a soap bubble: all its mass is on the surface. Linking this back: when attention is not scaled the variance scales and you get problems where the softmax equation gets saturated and the gradients are either minuscule or exploding.

{{< include _bubble-demo.md >}}

I love [this article](https://jamiesimon.io/blog/how-to-win-a-beer-with-high-dimensional-statistics/) (Simon, 2026) which demonstrates the funky properties of really high dimensional spaces. It's well known at this point transformer models will keep a cycle of related features in embedding space such that the model can cycle through them by rotating, this has been shown for counting (Gurnee et al., 2025), and days/months (Engels et al., 2024). However, because the number of dimensions allows for so many combinations, you're practically guaranteed to find any relationship you might want to go looking for in the embeddings. Simon shows this for words but this would be true for pretty much everything else.

::: {.callout-note}
## Aside
This really nicely links with another post I've written [How clean are neural nets, how long is a piece of string?](https://louisaberdeen.dev/are-nets-already-clean/) In it we deal with the tension between models having many possible interpretations of their internal state, which we try and understand for mechanistic interpretability, but we have to be careful to see what the model actually uses for computation.
:::

There is a distinction to be made between cross-attention (like in Bahdanau) where the queries came from the decoder and the keys/values from the encoder and self-attention. In self-attention the attention attends all on the input $X$.

I will add a quick note about multi-headed attention, the concept is pretty simple to understand. Imagine our model is like a person, and every time we feed it text we send it to just one person. Any one person will only have so much expertise in any topic, and making one person knowledgeable in everything is very difficult. What if instead we could have a group of people, with different jobs and perspectives (grammar vs numeracy in this analogy) who collaborate on an input sequence. This is essentially what we do. Instead of having one single attention calculation performed for our input, we could split between many "heads" which build their own patterns which get incorporated together. Not to be confused with "Mixture of Experts" which I will talk about later on. Splitting the attention into heads also has performance benefits too. When the attention is split into separate heads there are opportunities to parallelise or reduce redundancy (which I will talk more on later).

{{< include _multihead-diagram.md >}}


## The transformer block

Continuing through the "Attention is all you need" paper (Vaswani et al., 2017), we now need to connect our attention mechanism into a machine learning model this is done with a Transformer, Attention models and transformers are used interchangeably but I think it is a useful distinction to make. A transformer starts with attention over the inputs followed by a feed forward network typically with a residual connection and some layer normalisation. 

{{< include _transformer-diagram.md >}}

In the original paper the layer norm is applied to the residual but modern transformers move the residual outside the layer norm. The motivation is you want the residual stream to represent the identity function which allows gradients to flow evenly during training. The intuition is without residual stream early into training the early layers get good updates but later layers get basically random updates. In practice this modification results in more stable training with less warm up (Xiong et al., 2020).


```python
# Original paper
X = layer_norm(X + feed_forward(X, W_1, W_2), gamma2, beta2)

# Modern 
X = X + feed_forward(layer_norm(X, gamma2, beta2), W_1, W_2)
```

## Position

There is one important piece we haven't spoken about yet: How does the model know the token position relative to each other? since attention has the same score no matter what order, called permutation equivariance (work through it yourself to see if you reorder the attention calculation the score is the same). The sentence "The dog bit the man" is very different from "The man bit the dog".

The way this is done is by building in a position embedding, either a learned position embedding or a constructed one. The simplest way of doing this would be to have an absolute position embedding. 

```python
# Simplest
x = token_embeddings + np.arange(seq_len)[:, None] / seq_len

#Used in GPT-2
pe = np.random.normal(size=(max_len, d_model))  # trained like any other weight
x = token_embeddings + pe[:seq_len]
```

So for each token up to our maximum sequence length we add a vector to the token embeddings which describes their absolute position in the sequence. This approach is brittle to a few things; first the absolute nature means the model doesn't generalise well because the model needs to see an example at every position in the sequence to learn a position embedding. A concrete example is the phrase "The dog jumped" has the same relational meaning at position 1-3 as 35000-35002, but the model needs to learn a new position embedding for every scenario. The second issue is the model often doesn't get as many training examples at longer contexts so it won't perform as well further into its context window.

What is done in most modern models instead is called Rotary Position Embeddings (RoPE). The idea is simple; what if we could simply encode the relative position between tokens without any absolute position information. In terms of properties we would like translation invariance when we are calculating the score between queries and keys:
$$
\begin{aligned}
   \langle f(q, m), f(k, n) \rangle = g(q, k, n - m) 
\end{aligned}
$$

Where $f$ is our unknown position function, and $g$ is the attention score calculated without any knowledge of $n$ or $m$ only the relative distance $n-m$. We will assume that $f$ will be a linear function (they're just much nicer to work with, easier to calculate etc). Let us write $f$ as $f(x, m) = R_m x$ and work through the implications:

- $\langle R_m q, R_n k \rangle = q^\top R_m^\top R_n k$ this follows from substituting into the dot product.
- For our translation invariance we need $R_m^\top R_n = R_{n-m}$ for any $m$ and $n$. 
- Without loss of generality we can assume $R_m$ must be the identity $I$ when $m=0$ 
- When $m=n$,  $R_m^\top R_m = I$, implying $R_m$ is orthogonal, meaning its transpose is its inverse. 
- In the same vein we set $n=0$ and $R_m^\top = R_{-m}$
- We combine the above concepts and we can show $R_{m+n} = R_{-m}^\top R_n = R_m R_n$ so $f$ composes.
- Because $R_m$ composes it immediately follows $R_m = R^{m}$ (easiest to show via induction)

Put this together and we have an $R$ a 2D orthogonal linear operator in the form $R_m=R^m$. The operator we are describing is exactly a rotation by some $\theta$, so $R^m$ is a rotation by $m\theta$ (Puranik, 2026). The way this is applied in practice for $Q$ and $K$ with dimension $d$ is to apply $d/2$ rotations along the diagonal. Typically different frequencies are used along the diagonal. The main reason is because a single theta would wrap around and cause ambiguity, but you can think of a low frequency encoding a long term relationship and the high frequency encoding a short term relationship.


```python
def rope(x, base=10000.0):
    seq_len, d = x.shape[-2:]
    theta = base ** (-jnp.arange(0, d, 2) / d)      
    angles = jnp.arange(seq_len)[:, None] * theta   
    cos, sin = jnp.cos(angles), jnp.sin(angles)

    x1, x2 = x[..., 0::2], x[..., 1::2]              
    rotated = jnp.stack([x1 * cos - x2 * sin,
                         x1 * sin + x2 * cos], axis=-1)
    return rotated.reshape(x.shape)
```

{{< include _rope-diagram.md >}}



## References

1. Bahdanau, D., Cho, K., & Bengio, Y. (2015). *Neural Machine Translation by Jointly Learning to Align and Translate.* arXiv:1409.0473. <https://arxiv.org/abs/1409.0473>

2. Vaswani, A., et al. (2017). *Attention Is All You Need.* arXiv:1706.03762. <https://arxiv.org/abs/1706.03762>

3. Xiong, R., et al. (2020). *On Layer Normalization in the Transformer Architecture.* arXiv:2002.04745. <https://arxiv.org/abs/2002.04745>

4. Simon, J. (2026). *How to Win a Beer with High-Dimensional Statistics.* <https://jamiesimon.io/blog/how-to-win-a-beer-with-high-dimensional-statistics/>

5. Puranik, A. (2026). *Using Group Theory to Explore the Space of Positional Encodings for Attention.* Jane Street Blog. <https://blog.janestreet.com/using-group-theory-to-explore-positional-encodings-attention/>

6. Engels, J., Michaud, E. J., Liao, I., Gurnee, W., & Tegmark, M. (2024). *Not All Language Model Features Are One-Dimensionally Linear.* arXiv:2405.14860. <https://arxiv.org/abs/2405.14860>

7. Gurnee, W., et al. (2025). *When Models Manipulate Manifolds: The Geometry of a Counting Task.* Transformer Circuits Thread. <https://transformer-circuits.pub/2025/linebreaks/index.html>
