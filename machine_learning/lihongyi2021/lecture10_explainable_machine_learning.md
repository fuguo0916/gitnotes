# Explainable Machine Learning

2023.04.14

Soure: [[YouTube](https://www.youtube.com/watch?v=WQY85vaQfTI&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=26)]

## Why do we need Explainable ML?

+ Correct answers $\neq$ Intelligent
+ Loan issues / medical diagnosis / court / self-driving
+ We can improve ML model based on explaination.

## Interpretable v.s. Powerful

+ Linear models are more interpretable but less powerful.
+ Deep network is less interpretable but more powerful.
+ Let's make deep networks explainable!
+ Decision tree is interpretable and powerful. / Random forest, which is also hard to explain.

## The Goal of Explainable ML

+ Completely know how an ML model works?
+ But we trust the decision of humans!
+ To make people (your customers, your boss, yourself) comfortable.

## Catageries

Local Explaination / Global Explaination

## Which component is critical?

+ Randomly mask or remove some components.

+ Saliency Map:

    + Given a set of inputs $\{x_1,\dots,x_n\}$ and evaluation $e$, then we get
        $$
        |\frac{\Delta e}{\Delta x_k}|\to|\frac{\partial e}{\partial x_k}|
        $$

    + Limitation: **Noisy Gradient**.

        You may solve this problem by **SmoothGrad**, which randomly adds noises to the input image, gets saliency maps of the noisy images, and averages them.

    + Limitation: **Gradient Saturation**. Gradient cannot always reflect importance.

        Example: in saturated region, maybe
        $$
        \frac{\partial\ elephant}{\partial\ length}\approx 0
        $$
        Solve this problem by **Integrated Gradient (IG)** [[arXiv](https://arxiv.org/abs/1611.02639)]

## How a network processes the input data?

+ Visualization
+ Attention
+ Probing

## Global Explaination

+ For example, we generate the picture $X$ to make the output of filter 1 as big as possible.
    $$
    X^{\ast}=\arg\max_X\sum_i\sum_j a_{ij}
    $$
    The $X^{\ast}$ contains the patterns filter 1 can detect.

+ What does a digit look like for CNN? (e.g. digit classifier)

    If we want the $X^{\ast}$ such that:
    $$
    X^{\ast}=\arg\max_X y_i
    $$
    Can we see digits? No, we can only see noise.

    So, we use:
    $$
    X^{\ast}=\arg\max_X (y_i+R(X))
    $$
    where the $R(X)$ indicates how likely $X$ is a digit, which may be definited as follows, where intuitive motive is to make white color as less as possible.
    $$
    R(X)=-\sum_{i,j}|X_{ij}|
    $$

+ Another way is to use generator.

    ```mermaid
    graph LR
    z[z]
    g("Image<br/>Generator")
    x["Image<br/>X"]
    c("Image<br/>Classifier")
    y[y]
    
    z --> g --> x --> c --> y
    ```

    where $z$ is a low-dim vector.

    And the task is:
    $$
    X^{\ast}=\arg\max_X y_i\Longrightarrow z^{\ast}=\arg\max_z y_i
    $$
    Show image:
    $$
    X^{\ast}=G(z^{\ast})
    $$