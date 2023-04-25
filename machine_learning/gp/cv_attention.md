2023.04.21

[[知乎](https://zhuanlan.zhihu.com/p/510653740)]

视觉注意力机制分为三种: 通道域, 空间域, 混合域

## 通道域

SENet $\to$ SKNet (Selective Kernel) (适应大网络)

## 混合域

CBAM (Convolutional Block Attention Module) 在SE的基础上加入空间域注意力

$$
Convolutional\ Block\ Attention\ Module = Channel\ Attention\ Module + Spatial\ Attention\ Module 
$$

$$
CBAM=CAM+SAM
$$

## 空间域

### 第一类

CBAM的SAM部分

### 第二类

QKV自注意力. 缺点: 计算量大.

改进: ANN (Asymemetric Non-Local Neural Network) $\to$ GCNet (Global Context, Using random query point)

CCnet (Criss-Cross Attention)

## 实验中采用的CV-Attention

SE, SK, CBAM, GC
