# A lightning fast history lesson
I think it is good to have an understand of where the models we use today come from as many of the constraints and solutions seem obvious in retrospect but at the time required an intimate understanding of the problem. Plus I enjoy writting about this stuff.

Also I will say token meaning character/word/subword/sample so use whatever mental model is most comfortable for you.

The simplest model to start with is a Recurrent Neural Network (RNN). Whereas a simple nueral network is simply 

$$\begin{aligned}
y = \sigma(W \cdot x)
\end{aligned}$$


where $\sigma$ is an activation function like tanh, $W$ is the weight matrix $x$ is our input and $y$ could be something like a class preiction if we log normalised. A deeper model would have more layers composed together maybe with residual connections. The problem with a model like this is it has a fixed input and output, this can be great when we want to map from an image of pixels say, with fixed hieight and width, to a set of specific classifications. This doesn't work with sequential data since we really need to feed in a sequence of information where the input length is unknown at compute time (I guess it could be possible if we train different N-gram models, but that would be dumb). One way of doing this is allowing the model to recursively propagate what is called a hidden space (which I never really like the name of what makes it more hidden than the rest of the model?) the hidden state is passed on from each token to another so the function is now 

$$\begin{aligned}
h_{n+1} = \sigma(W_x \cdot x_n + U_h \cdot h_n)
\end{aligned}$$


and the prediction 
$$\begin{aligned}

y_{n+1} = W_{hy} \cdot h_{n+1}
\end{aligned}$$

We can see that the information from previous tokens is stored in the hidden state. Andrej karpathy has a great post on how unresonably good RNNs are https://karpathy.github.io/2015/05/21/rnn-effectiveness/ and it is basically this property. 

The issue with RNNs is the model really struggles to keep information from more than a few previous tokens, this is due to the recursive nature of the model where repeated multiplication  shrinks the contribution of previous token to zero. This has an additional effect in training where the gradients from previous tokens tend to zero as they get futher away (exercsie for the reader to convince themselves). (Another persisent problem with these recursive style of models is they required "rolling-out" the models recursively in an expensive and not easily parallisable way (which we will come back to))

This encouraged the invention of LSTM models which solves this problem by adding a cell state with an additive update gated by input/forget gates which allows the model to have stable a pathway over longer sequences. 
$$
\begin{aligned}
f_n &= \sigma(W_f \cdot x_n + U_f \cdot h_n + b_f) \\
i_n &= \sigma(W_i \cdot x_n + U_i \cdot h_n + b_i) \\
o_n &= \sigma(W_o \cdot x_n + U_o \cdot h_n + b_o) \\
\tilde{c}_{n+1} &= \tanh(W_c \cdot x_n + U_c \cdot h_n + b_c) \\
c_{n+1} &= f_n \odot c_n + i_n \odot \tilde{c}_{n+1} \\
h_{n+1} &= o_n \odot \tanh(c_{n+1})\\
y_{n+1} &= W_{hy} \cdot h_{n+1}
\end{aligned}
$$
what a nightmare to read, the important part is $c_{n+1}$ being an additive update


To understand what motivated the transformer we need to investigate the seq2seq model which was trying to improve on translation tasks. To do this they build a model composed of two LSTM models, one as an encoder and another as a decoder. The first LSTM model would encode a single embedding representation of the sequence and the second LSTM model would use the context vector as they called it to try and generate a translation for example. The difficulty was the context vector could only store so much information which could be transferred to the decoding model and so it would struggle with longer sequences or more complex grammar structures.

[archetecture diagram]

The proposed solution to this was in Bahdanau Attention where an LSTM would generate vector representation of each token then the decoder would use attention to take a weighted sum over those feature embeddings of the seqeunce to bias the most relevant embeddings (at this point we are very close to the form we are familiar to today). This model was a huge step forward since it could handle difficult grammar challenges like english to german or english to japanese (since the the grammar needs reordering). Then we get to the big one Attention Is All You Need where they strip out the hard to parallelise LSTM models and go all in on the transformer model archetecture which is basically just attention + a feed forward network (however you'll notice that with the loss of the recurrence relationship you have lost all positional information!).

To see why parallisation is not easy for recurrance tpye model; consider this excerpt from the forward pass of an RNN.
```python
...
for t, x in enumerate(self.xs):
    h_t = np.tanh(self.Wx @ x + self.Wh @ h_t + self.b_h)
    self.hs[t+1] = h_t
    logits = self.Why @ h_t + self.b_y
    self.probs[t] = softmax(logits)
                                                                            
self.h_t = h_t                                     
return self.probs
```
Here we can clearly see how the recurrance relation requires serial computation of our hidden states. This means while we can still parallelise over batches but not over time, extra painful in training when we do it again in reverse for Back Propagation Through Time. Add on top our processing time is dependant on the length of the series and you can see why this archetecture isn't ideal. We will see how transformers differ in these respects (parallel training but still sequential generation).

It is worth noting as a final remark of this section; ML is dominated not neccassarily by the smartest or most domain specific archetectures, but more often whoevers strategy allows for massively scaling the compute and volume of quality data for the model, and it is this with lens the transformer should be viewed.  

## References

1. Karpathy, A. (2015). *The Unreasonable Effectiveness of Recurrent Neural Networks.* https://karpathy.github.io/2015/05/21/rnn-effectiveness/

2. Hochreiter, S., & Schmidhuber, J. (1997). *Long Short-Term Memory.* Neural Computation, 9(8), 1735–1780. https://www.bioinf.jku.at/publications/older/2604.pdf

3. Sutskever, I., Vinyals, O., & Le, Q. V. (2014). *Sequence to Sequence Learning with Neural Networks.* arXiv:1409.3215. https://arxiv.org/abs/1409.3215

4. Bahdanau, D., Cho, K., & Bengio, Y. (2015). *Neural Machine Translation by Jointly Learning to Align and Translate.* arXiv:1409.0473. https://arxiv.org/abs/1409.0473

5. Vaswani, A., et al. (2017). *Attention Is All You Need.* arXiv:1706.03762. https://arxiv.org/abs/1706.03762

6. Sutton, R. (2019). *The Bitter Lesson.* http://www.incompleteideas.net/IncIdeas/BitterLesson.html

7. Austin, J., et al. *How to Scale Your Model* (the "Scaling Book"). https://jax-ml.github.io/scaling-book/

# Attention and the transformer
Transformers are built entirely around the attention mechansim. Attention uses three inputs: Queries, Keys, and Values. A useful analogy is to how in programming we have dictionaries where you have a query (the thing you're matching against), which you compare against keys (what you ahve), to get values (what you want). Rather than direct equality between query and key a similairty score is computed, and rather than returning one value you return a weighted sum over all of them. Basically using a dictionary is like a hard lookup whereas attention is doing a soft lookup. From an input of $X$ we project a $Q$,$K$,and $V$ matrix.

$$
\begin{aligned}
scores &= Q \cdot K^T / \sqrt{d_k}   \\
weights &= softmax(scores) \\
attention &= weights \cdot V           
\end{aligned}
$$

This has the nice property where every token can attend any other token. I thhink of Query being what the token is looking for, the Key being what other tokens can recognise, and the Value is the actual information handed over. Whereas before our recurrant models were bottlenecked by their iterative multiplication attention allows the model to represent relationships arbitarily far apart.

There is a distinction to be made between cross-attention (like in Bahdanau) where the queries came from the decoder and the keys/values from the encoder and self-attention. In self-attention the attention attends all on the input $X$.

[equations or maybe diagram]

Continuing through the "Attention is all you need" paper, we now need to connect out attenion mechanism into a machine learning model this done with a Transformer, Attention models and transformers are used interchangably at this point but I think it is a usefull distinction to make. A transformer starts with attenion over the inputs followed by a feed foward network typically with a residual conncetion

[definitely a diagram] 