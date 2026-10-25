# Training

The weights initialisation in older models, like deep conv nets relied on Xavier/Glorot initialisation for tanh and sigmoid functions and He, the ReLU equivalent, where you initialize the weights such that the variance of the activations are the same across every layer. This is where ml becomes more like alchemy, usually in production weights are initialised with $\mu = 0$ $\sigma = 0.02$. To see why this could be the case if we take $d = 4096$ then Xavier is $\sqrt{2/8192} \approx 0.016$ and He is $\sqrt{2/4096} \approx 0.022$. The more important element is the parameters are truncated to $\pm 2-3 \sigma$ which is done to improve stability in training since very large activations can propagate deep into the model especially from the embedding weights (Alex Wa, *Frontier Training Methodologies*). This doesn't track so well for really small models. 

## References

1. Glorot, X., & Bengio, Y. (2010). *Understanding the Difficulty of Training Deep Feedforward Neural Networks.* AISTATS. https://proceedings.mlr.press/v9/glorot10a.html

2. He, K., Zhang, X., Ren, S., & Sun, J. (2015). *Delving Deep into Rectifiers: Surpassing Human-Level Performance on ImageNet Classification.* arXiv:1502.01852. https://arxiv.org/abs/1502.01852

3. Alex Wa. *Frontier Training Methodologies.* [TODO: full author name and link]
