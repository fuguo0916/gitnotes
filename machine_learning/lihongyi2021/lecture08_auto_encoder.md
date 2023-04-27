# Auto-encoder

2023.04.14

Source: [[YouTube](https://www.youtube.com/watch?v=3oHlf8-J3Nc&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=22)]

Auto-encoder, which is an old technology, is also a kind of self-supervised learning.

## Basic Idea of Auto-encoder

+ NN Encoder: from image to vector
+ NN Decoder: from vector to image

+ To make input of encoder and output of decoder as close as possible. (also called **reconstruction**) (We have seen the same idea in **Cycle GAN**)
+ The intermediate vector is called **enbedding**, or **representation**, or **code**. It can be used for downstream tasks.
+ The intermediate is called bottleneck because of it is low dim. (Dimension Reduction)

## Why Auto-encoder?

+ Auto-encoder is not a new idea. Hinton, 2006, *Reducing the dimensionality of data with neural networks*.
+ Variant: Denoising Auto-encoder. (2008) Adding noise precedes the encoder and decoder.
    + BERT can also be considered as donoising auto-encoder.

## Feature Disentanglement

+ Representation includes information of different aspects
+ Feature Disentangle: To know the specific meaning of each dimension of representation
+ Application: Voice Conversion

## Discrete Latent Representation

+ Representation could be binaries, even one-hot, instead of real numbers.
+ Vector Quantized Variantional Auto-encoder (VQVAE) [[arXiv](https://arxiv.org/abs/1711.00937)]
    + We have a codebook comprising a set of vectors (representations)
    + Compute similarity between the outcome representation and vectors in codebook.
    + The most similar one is the input of decoder
    + For speech, the codebook represents phonetic information

## More Applications

+ Text as Representation: The enbedding could be a word sequence, which usually turns out to be the abstract of the input article.
+ NN Decoder used as Generator
+ NN Encoder as Compression, and NN Decoder as Decompression
+ Anomaly Detection
    + First, use normal data to train a pair of encoder and decoder
    + If the output is close to the input, it's judged normal. Otherwise, it's abnormal.