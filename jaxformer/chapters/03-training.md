# Training

## Training a decoder: next-token prediction

causal masking here?

{{< include _next-token-demo.md >}}

Training a transformer in the pretraining phase is entirely "next-token" prediction. This is where people get the idea of LLMs as simple stochastic parrots from (Bender et al., 2021). Without the Supervised Fine Tuning (SFT) and Reinforcement Learning (RL) they would simply be very efficient token compression algorithms.  

To train these models we take a sequence of tokens and have the model predict the distribution of the next token. This is done by taking the model output from the final layer of the model, called logits, and taking the softmax over them to get a conditional probability distribution for the next token.

$$
\begin{aligned}
\text{softmax}(x)_i = \frac{e^{x_i}}{\sum_{j=1}^C e^{x_j}}
\end{aligned}
$$

The training objective for a transformer is the cross entropy loss between the predicted distribution and the real next token, averaged over many, many examples the model will converge on the actual distribution. A fancy way to say this is in expectation the model which minimises the loss is the model which learns the true probability distributions 

{{< include _convergence-demo.md >}}

So for a single example the cross entropy loss for models predicted probability distribution $p$ and target next token $y$ when we have a vocabulary of $C$ tokens.
$$
\begin{aligned}
\text{CE} = - \sum_{j=1}^C y_{j} \log p_{j}
\end{aligned}
$$
where $y_j = 1$ when the next token is $j$ and otherwise $y_j = 0$. Taken over some dataset of $N$ examples we get.
$$
\begin{aligned}
\text{CE} = - \frac{1}{N} \sum_{i=1}^N \sum_{j=1}^C y_{i,j} \log p_{i,j}
\end{aligned}
$$

## Weight initialisation

The weights initialisation in older models, like deep conv nets relied on Xavier/Glorot initialisation (Glorot & Bengio, 2010) for tanh and sigmoid functions and He (He et al., 2015), the ReLU equivalent, where you initialise the weights such that the variance of the activations is the same across every layer. This is where ML becomes more like alchemy, usually in production weights are initialised with $\mu = 0$, $\sigma = 0.02$. To see why this could be the case if we take $d = 4096$ then Xavier is $\sqrt{2/8192} \approx 0.016$ and He is $\sqrt{2/4096} \approx 0.022$. The more important element is the parameters are truncated to $\pm 2$–$3\sigma$ which is done to improve stability in training since very large activations can propagate deep into the model especially from the embedding weights (Wa, 2026). This doesn't track so well for really small models. 

## References

1. Bender, E. M., Gebru, T., McMillan-Major, A., & Shmitchell, S. (2021). *On the Dangers of Stochastic Parrots: Can Language Models Be Too Big?* FAccT '21. <https://doi.org/10.1145/3442188.3445922>

2. Glorot, X., & Bengio, Y. (2010). *Understanding the Difficulty of Training Deep Feedforward Neural Networks.* AISTATS. <https://proceedings.mlr.press/v9/glorot10a.html>

3. He, K., Zhang, X., Ren, S., & Sun, J. (2015). *Delving Deep into Rectifiers: Surpassing Human-Level Performance on ImageNet Classification.* arXiv:1502.01852. <https://arxiv.org/abs/1502.01852>

4. Wa, A. (2026). *Frontier Model Training Methodologies.* <https://djdumpling.github.io/2026/01/31/frontier_training.html>
