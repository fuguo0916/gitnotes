# Self Attention

2023.04.11

### Sophisticated Input

+ Input might be a set of vectors (indefinite length)
+ e.g. Input is a sentence.
+ e.g. Input is a set of frames of sounds. (1 frame for 25ms)
+ e.g. Input is a graph. (e.g. social network)

### Sophisticated Output

+ Sequence to sequence (fixed length)
+ Sequence to one
+ Sequence to sequence (indefinite length, e.g. translation)

### Sequence Labeling

+ Focus on the first case: Sequence to sequence (fixed length)
+ Fully Connected: One to one.
+ Fully Connected: You may use fixed sized window. Window to one.
+ Self Attention: takes whole sequence as the input, and outputs a sequence, in which each vector carries information of its context.

### Self Attention

+ *Attention is all you need*: proposes transformer by Google.
+ For each vector: Find the relevant vectors in a sequence by $\alpha$ index, which is calculated out via dot-product (almost always used) or additive (seldom used)

$$
\boldsymbol{q^1}=W^q\boldsymbol{a^1}
$$

where $W^q$ is the query matrix and $\boldsymbol{q^1}$ is the query of $\boldsymbol{a^1}$.

$$
\boldsymbol{k^2}=W^k\boldsymbol{a^2}
$$

where $W^k$ is the key matrix and $\boldsymbol{k^2}$ is the key of $\boldsymbol{a^2}$.

For $\boldsymbol{a^1}$, the relevance of $\boldsymbol{a^2}$ is denoted by $\alpha_{1,2}$, and

$$
\alpha_{1,2}=\boldsymbol{q^1}\cdot\boldsymbol{k^2}
$$

Then, do softmax:

$$
\alpha^{\prime}_{1,i}=\frac{\exp(\alpha_{1,i})}{\sum_j\exp(\alpha_{1,j})}
$$

Calculate $\boldsymbol{v^1}$ which is the value vector of $\boldsymbol{a^1}$:

$$
\boldsymbol{v^1}=W^v\boldsymbol{a^1}
$$

Get the final output $\boldsymbol{b^1}$:

$$
\boldsymbol{b^1} = \sum_i \alpha^{\prime}_{1,i}\boldsymbol{v^i}
$$

Overall,

$$
\begin{cases}
Q=W^q I\\
K=W^k I\\
V=W^v I\\
A=K^T Q\\
A^{\prime}=ColumnwiseSoftmax(A)\\
B=VA^{\prime}
\end{cases}
$$

All we need to train is $W^q,W^k,W^v$.

### Multi-head Self Attention

Take head number 2: Double $Q,K,V$ matrix.

### Positional Encoding
+ No position information in self-attention.
+ Each position has a unique positional vector $\boldsymbol{e^i}$.
+ hand-crafted

### Many Applications
+ Transformer
+ Bert
+ Self Attention for Speech
  - to reduce complexity: Truncated Self Attention
+ Self Attention for Image
  - Every pixel is a 3-dim vector, and an image is a vector set
  - Self Attention GAN
  - Detection Transformer (DETR)

### Self Attention v.s. CNN

+ CNN: self-attention that can only attends in a receptive field.
  - CNN is simplified self-attention
+ Self-attention: CNN with learnable receptive field
  - Self-attention is the complex version of CNN
+ [On the Relationship between Self-Attention and Convolutional Layers](https://arxiv.org/abs/1911.03584)

### Self Attention v.s. RNN (Recurrent Neural Network)

+ It is hard to consider remote inputs as for RNN
+ RNN is nonparallel
+ [Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention](https://arxiv.org/abs/2006.16236)

### Self Attention for Graph
+ Consider edge: pay attention only to connected nodes
+ Self-attention is one type of Graph Neural Network (GNN)

