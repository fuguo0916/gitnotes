# Transformer

2023.04.11

[YouTube Link](https://www.youtube.com/watch?v=n9TlOhRjYoc&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=12)

## Sequence-to-sequence (Seq2seq)
+ The output length is determined by model
+ Examples:
  + Speech Recognition
  + Machine Translation
  + Speech Translation (e.g. Hokkien speech to Mandarin text, for languages without text)
  + Seq2seq for chat-bot
  + Most NLP applications can be solve as Question Answering (QA) problems.
  + Seq2seq for Syntactic Parsing: sentence to tree (represented by sequence)
  + Seq2seq for Multi-label Classification
  + [Seq2seq for Object Detection](https://arxiv.org/abs/2005.12872)

## Encoder
+ You may use RNN or CNN
+ Use self-attention in transformer
+ Use layer normalization instead of batch normalization [[think-link](https://arxiv.org/abs/2003.07845)]

<img src="LEC05/lec05_01.png" alt="lec05_01" style="zoom:50%;" />

<img src="LEC05/lec05_02.png" alt="lec05_02" style="zoom:50%;" />

## Decoder
### Autoregressive (AT)

(speech recognition as the example)

<img src="LEC05/lec05_04.png" style="zoom:50%;" />

+ Add BEGIN Token and END Token
+ Use masked self-attention
+ We do not know the correct output length

### Non-autoregressive (NAT)

+ The input is a sequence of BEGIN
+ One way: use another predictor for output length
+ Another way: output a very long sequence, ignoring tokens after END
+ Advantages: parallel, controllable output length

### Cross Attention

<img src="LEC05/lec05_05.png" alt="lec05_05" style="zoom: 33%;" />

<img src="LEC05/lec05_06.png" alt="lec05_06" style="zoom:50%;" />

### Guided Attention
(Monotonic Attention, Location-aware attention)

In some tasks, input and output are monotonically aligned. For example, speech recognition, TTS, etc.

### Beam Search

The Greedy Strategy is not always the best.
