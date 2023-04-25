# Introduction

## Steps

+ Function with Unknown Parameters
+ Define Loss from Training Data
  + MAE: Mean Absolute Error
  + MSE: Mean Square Error
  + Cross Entrophy
+ Optimization
  + Gradient Descent: $\Delta W = - \eta \times Gradient$, where $\eta$ is the learning rate.

Linear models have severe limitation. Such kind of limitation is called **model bias**.

Any curve could be approximated by a piecewise linear curve. Any piecewise linear curve can be decomposed into $\textit{constant} + \textit{sum of hard sigmoid like functions}$. Hard sigmoid like functions could be approximated by sigmoid like functions as $c \times sigmoid(Wx+b)$. In conclusion,
$$ y = b + \Sigma_i c_i\ sigmoid(b_i + \Sigma_j W_{ij} x_j) $$
$$ y = b + c^{T} \sigma(\mathbb{b} + Wx) $$
$$ \theta^1 \leftarrow \theta^0 - \eta \nabla L(\theta^0) $$

Another way to approximate hard sigmoid is by two rectified linear units (ReLU).