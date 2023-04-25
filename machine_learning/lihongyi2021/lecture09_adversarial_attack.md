# Adversarial Attack

2023.04.14

Source: [[YouTube](https://www.youtube.com/watch?v=xGQKhbjrFRk&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=24)]

## How to Attack?

+ Example of Attack
    + Add some imperceptible noise to benign images and get attacked images.
    + The outcome changes after attacking.
    + Non-targeted / Targeted


$$
y^0=f(x^0)
$$

$$
y=f(x)
$$

To make $y$ and $\hat{y}$ as far as possible, define loss function.

For non-targeted attack:

$$
L(x)=e(y,\hat{y})
$$

For targeted attack:

$$
L(x)=-e(y,\hat{y})+e(y,y^{target})
$$

Also, $x$ and $x^0$ should be as close as possible. So,
$$
x^{\ast}=\arg\min_{d(x^{0},x)\le\epsilon}L(x)
$$

where $d$ could be L2-norm or L-infinity.

L2-norm:
$$
d(x^0,x)=\|\Delta x\|_2=(\Delta x_1)^2+(\Delta x_2)^2+(\Delta x_3)^2+\dots
$$


L-infinity:
$$
d(x^0,x)=\|\Delta x\|_{\infty}=\max\{|\Delta x_1|,|\Delta x_2|,|\Delta x_3|,\dots\}
$$

Get $x^{\ast}$ by **Fast Gradient Sign Method (FGSM)** [[arXiv](https://arxiv.org/abs/1412.6572)]

## White Box v.s. Black Box

+ In the previous attack, we know the network parameters $\theta$, which is called **White Box Attack**.
+ Are we safe if we do not release model? No, because **Block Box Attack** is possible.

### Black Box Attack

+ If the training data is shared, you may use a proxy network.
+ Black box attack is also easy to work for non-targeted attack. [[arXiv](https://arxiv.org/pdf/1611.02770.pdf)]
+ One pixel attack
+ Universal Adversarial Attack [[arXiv](https://arxiv.org/abs/1610.08401)]
+ Beyond Images
    + Speech Processing: *Detect Synthesized Speech* could also be attacked
    + Natural Language Processing
    + Attack in the Physical World: Add noise in physical world. [[src](https://www.cs.cmu.edu/~sbhagava/papers/face-rec-ccs16.pdf)]
    + Adversarial Reprogramming.
    + *Backdoor* in Model [[arXiv](https://arxiv.org/abs/1804.00792)]

## Defense

### Passive Defense

+ Add filters, such as smoothing, compression, generator, etc.
+ Passive defense is brittle if someone knows it. You may add some randomization.

### Proactive Defense

+ Adversarial Training: Training a model that is robust to adversarial attack. Another view of data augmentation.

