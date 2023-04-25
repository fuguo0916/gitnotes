# Self-supervised Learning

2023.04.12

Source: [[YouTube](https://www.youtube.com/watch?v=e422eloJ0W4&list=PLJV_el3uVTsMhtt7_Y6sgTHGHp1Vb2P2J&index=18)]

[TOC]

## Names

+ BERT: **B**idirectional **E**ncoder **R**epresentations from **T**ransformers
+ ELMo: **E**mbeddings from **L**anguage **Mo**dels
+ ERNIE: **E**nhanced **R**epresentation through K**n**owledge **I**nt**e**gration
+ Big Bird: Transformers for Longer Sequences

## Bert

+ Proposed in 2018, with 340M parameters

    | ELMO | BERT | GPT-2 | Megatron | T5   | GPT-3 |
    | ---- | ---- | ----- | -------- | ---- | ----- |
    | 94M  | 340M | 1542M | 8B       | 11B  | 175B  |

+ Transformer Encoder

+ Masking Input: Randomly masking some tokens (replacing with a special token or random tokens) [[arXiv](https://arxiv.org/abs/1810.04805)]

+ Next Sentence Prediction

    + Input: `[CLS] + Sentence 1 + [SEP] + Sentence 2`
    + Output: Yes/No (This approach is not helpful. [[arXiv](https://arxiv.org/abs/1907.11692)])
    + SOP (Sentence Order Prediction): used in ALBERT [[arXiv](htpps://arxiv.org/abs/1909.11942)]

+ Trained to fill blanks, used for various downstream tasks.

+ Semi-supervised: The upstream task (pretrain) is unsupervised, while the downstream tasks are usually supervised.

+ GLUE: General Language Understanding Evaluation

    + Corpus of Linguistic Acceptability (CoLA)
    + Stanford Sentiment Treebank (SST-2)
    + ... (9 tasks in total) (using 9 fine tuned models based on Bert)

### How to use BERT

#### Case 1

+ Input: sequence
+ Output: class
+ Example: sentiment analysis
+ The BERT part is init with pretrained weights and the Linear part is randomly initialized.

#### Case 2

+ Input: sequence
+ Output: same size as input
+ Example: POS tagging

#### Case 3

+ Input: two sequences
+ Output: a class
+ Example: Natural Language Inference (NLI)

#### Case 4

+ Extraction-based Question Answering (QA)
+ Document: $D=\{d_1,d_2,\cdots,d_N\}$
+ Query: $Q=\{q_1,q_2,\cdots,q_M\}$
+ The input is $D$ and $Q$, and the output is two integers $s$ and $e$, and the final answer is $A=\{d_s,\cdots,d_e\}$.

### Pretraining a seq2seq model

+ Bert is only the transformer encoder
+ The input of encoder is corrupted data, and the output of decoder is supposed to be the reconstructed data.
+ How to corrupt sequence? MASS (replacing with a special token ) / BART (ensemble method)
+ T5 - Comparison
    + Transfer Text-to-Text Transformer (T5)
    + Colossal Clean Crawled Corpus (C4)

### Why does BERT work?

+ *You shall know a word by the company it keeps.* Word embedding (CBOW model).
+ Bert is contextualized word embedding.
+ Applying BERT to protein, DNA, music classification

### Multi-lingual BERT

+ QA: Pretrained in 104 languages. Trained only in English, but working well in Chinese.
+ Mean Reciprocal Rank (MRR): Higher MRR, better alignment.
+ The category of language is also reflected in word vectors. [[arXiv](https://arxiv.org/abs/2010.10041)]

## GPT

+ Predict Next Token
+ Works like transformer decoder.

### How to use GPT?

+ "In-context" Learning: task description & examples & prompt (You don't need to train for the specific task.)

    Few-shot Learning => One-shot Learning => Zero-shot Learning (no examples)

### Beyond Text

+ Image: SimCLR. [[arXiv](https://arxiv.org/abs/2002.05709)] [[GitHub](https://github.com/google-research/simclr)]
+ Image: BYOL (Bootstrap your own latent) [[arXiv](https://arxiv.org/abs/2006.07733)]
+ Speech