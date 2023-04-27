# Generative Adversarial Network

2023.04.10

[TOC]

### YouTube Links

+ [Part 1](https://www.youtube.com/watch?v=4OWp0wDu6Xw&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=14)
+ [Part 2](https://www.youtube.com/watch?v=jNY1WBb8l4U&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=15)
+ [Part 3](https://www.youtube.com/watch?v=MP0BnVH2yOo&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=16)
+ [Part 4](https://www.youtube.com/watch?v=wulqhgnDr7E&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=17)

A generator is a network which takes $x$ and $z$ as the input, where we get $z$ by sampling from a simple distribution, such as Gaussian Distribution. And the output $y$ is a complex distribution.

Take video prediction for example. In traditional way, the input $x$ is previous frames, and the output $y$ is the next frame. Because all common probabilities exist in $x$ while there is no specific choice. So we add a simple distribution $z$, then the output $y$ turns out a distribution.

### Why distribution?
+ Especially for tasks that need "creativity" (same input to different outputs)
+ Examples: Drawing (e. g. Characters with red eyes); Chat-bot

### Animated Face Generation

+ Unconditional generation: $Generator: z \to y$, where $z$ is a low-dim vector sampled from Normal Distribution, and $y$ is a high-dim vector which obeys a complex distribution, e. g. an animated face picture. A simple distribution like Normal Distribution for $z$ is OK.
+ We also need to train a discriminator, which takes a image as input and outputs a scalar reflecting how real/fake the image is.

### Basic Idea of GAN

<img src="GAN/GAN_01.png" alt="GAN_01" style="zoom:50%;" />

<img src="GAN/GAN_02.png" alt="GAN_02" style="zoom:50%;" />

### Algorithm

+ Initialize generator and discriminator.
+ In each training iteration:
  1. Fix generator G, and update discriminator D (Classification or regression)
  2. Generator learns to "fool" the discriminator:
    ```mermaid
    graph LR
    vector(vector)
    NNGenerator["NN Generator<br/>(update)"]
    output1(output)
    Discriminator["Discriminator<br/>(fix)"]
    score("score<br/>(e.g. 0.13)")
  
    vector --> NNGenerator --> output1 --> Discriminator --> score
    ```

### Theory behind GAN

$$
G^{\ast} = \arg \min_{G} Div(P_G, P_{data})
$$

where $Div(P_G, P_{data})$ means the divergence between distributions $P_G$ and $P_{data}$.

$$
D^{\ast}=\arg\max_D V(D,G)
$$

and objective function for D:

$$
V(G,D)=E_{y\sim P_{data}}[\log D(y)]+E_{y\sim P_G}[\log(1-D(y))]
$$

which is negative cross entropy.

Finally,

$$
G^{\ast} = \arg \min_{G} Div(P_G, P_{data})=\arg\min_{G}\max_{D}V(G,D)
$$

$V(D,G)$ is related to JS Divergence, and there are many substitutes for $V(D,G)$.

### Tips for GAN Training

#### JS divergence is not suitable sometimes
+ Because in most cases, $P_G$ and $P_{data}$ are not overlapped due to the nature of data and sampling.
+ JS divergence is always $\log2$ if two distributions don't overlap.

#### From JS Divergence to Wasserstein Distance

+ Considering one distribution P as a pile of earth, and another distribution Q as the target
+ The average distance the earth mover has to move the earth is the Wasserstein Distance
+ Sometimes there are many possible "moving plans". So use the "moving plan" with the smallest average distance to define Wasserstein Distance.

#### WGAN

Evaluate Wasserstein Distance between $P_{data}$ and $P_G$:

$$
\max_{D\in 1\text{-}Lipschitz}\{E_{y\sim P_{data}}[D(y)]-E_{y\sim P_G}[D(y)]\}
$$

$D\in 1\text{-}Lipschitz$ means that $D$ has to be smooth enough.

#### There are some other generative models

+ Variational Autoencoder (VAE)
+ FLOW-based Model

They are also hard to train and do not outperform GAN.

### Evaluation of Generator

Input **one** image into an Off-the-shelf Image Classifier, and more concentrated distribution means higher visual quality.

Diversity problems like mode collapse and mode dropping usually occur. So you may input the generated images into a classifier, and uniform means higher variety.

Inception Score (IS): Good quality, large diversity --> Large IS

Frechet Inception Distance (FID): using the vector just before softmax; Frechet distance between two Gaussians.

We do not want memory GAN.

### Conditional Generation

#### Text-to-image

Back to the first question, and set $x$ to some descriptions like "blue eyes black hair".

### Learning from Unpaired Data

#### Image Style Transfer

From Domain $\mathcal{X}$ to Domain $\mathcal{Y}$: $G_{\mathcal{X}\to\mathcal{Y}}$

<img src="GAN/GAN_03.png" alt="GAN_03" style="zoom: 50%;" />

<img src="GAN/GAN_04.png" alt="GAN_04" style="zoom:50%;" />