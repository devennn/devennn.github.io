---
layout: post
title: Understanding Self and Multi-Head Attention
description: "Explaining the workings of self-attention and multi-head attention"
keywords: "attention, bert"
published: false
---

This post aims to explain the workings of self and multi-headed attention in BERT family model. You can read more about attention algorithms in general from these references. 

Simply said, the function of attention algorithm is to help focus on meaningful words.

## Self-Attention

In the original BERT design, self-attention is a small part in the encoder and decoder block. The purpose is to focus on important words and are used with other parts such as feedforward, etc. to produce the output for the bigger block(encoder, decoder).

![output of self-attention](/assets/images/self-attention-input-output.png)

Zooming into the self-attention section, these are the processes to produce the output.

![process of self-attention](/assets/images/self-attention-process.png)

#### Process 1 - Word embedding to Query, Key and Value

There are three key parameters introduced in this concept—query, key and value. To avoid confusion, I will use Q, K and V to reference them.

These vectors are calculated from the word embedding. Another way to understand this process is the input embedding is translated into three different versions of itself, from embedding space to Q, K and V space.

This is done by projecting the input to three different sets of weights, i.e. Query weight, Key weight and Value weight. All weights act as a projection medium which is trainable. 

Each word in the sequence will have their query, key and value. 

![query, key and value process](/assets/images/transformer_self_attention_vec.png)

[*Image Source: jalammar.github.io*](https://jalammar.github.io/illustrated-transformer/)

One thing to note is all three values mean nothing. They are just mathematical representations of the input word in different vector space which can be used for calculations. That's it.  

#### Process 2 - Calculating Attention Score

Up to here, the input embedding has been projected to Q, K and V spaces. In this process, Q and K are used for calculating the attention score. 

Let's say we want to encode ```eat``` from the sentence ```They eat fried noodles```, after process 1, all input words will have Q, K and V as below.

Starting from here, all examples are referring to calculating for one word only. In a real application, every word will have to go through these steps.

![Q, K and V for every word](/assets/images/qkv_result.png)

In process 2, the scores are calculated by performing dot products of Q of ```eat``` with transposed K of every word in the sequence, including itself. The result for a single word is a vector of size (1, 4) which holds the value of every dot product result.

The size (1, 4) is used as an example in this post for simplicity. BERT has its dimensions of word vectors. 

![score calculation](/assets/images/score_calculation.png)

Then, every score is divided by 8. According to this source, *"This leads to having more stable gradients"* [source](https://jalammar.github.io/illustrated-transformer/). 

```
score = [32, 88, 56, 72]

after divide by 8
score = [4, 11, 7, 9]
```
The attention score is calculated by applying the softmax function to all values in the vector.

![softmax function](/assets/images/softmax_function.png)

This will adjust the scores so that the total will add up to 1.

```
Softmax result

softmax_score = [0.0008, 0.87, 0.0016, 0.012]

```
The attention scores indicate the importance of the word in the context of word being encoded, which is ```eat```. Higher value, higher importance.

#### Process 3 - Calculating Output Vector

The last process is to produce the output vector. Up to here, we have the attention score and V. 

Here, the softmax scores are multiplied to their respective V. Then, the values are added together to produce the output vector. 

![Output vector calculation](/assets/images/attention_score.png)

For example, to produce value for ```eat``` with respect to ```they``` the calculation is

```
softmax score: 0.0008
V for *they* = [V11 V12 V13 V14]

The Multiplication values, X are:

X = [X11 X12 X13 X14]
X = [0.0008(V11) 0.0008(V12) 0.0008(V13) 0.0008(V14)]
```

The green box is the output vector of self-attention section. This is the value that is passed to the next part of the block.

## Understanding how the matrix evolve

Before we go to the multi-head, lets understand the dimensions of matrixes for every stage. Let's use the dimensions from transformer model.

The model requires input dimension to be (1, 512) for a single word vector. In our example sentence, The matrix size is (4, 512). The embedding size or number of features for every parts of encoder in the whole model should be 512. That means, the input and output matrix that pass through self-attention has matrix of size (4, 512).

#### Evaluating self-attention

![self-attention matirx evolution](assets/images/self-attention-evolution.png)

If we look at the output of self-attention, the size of output matrix is not equal to the input. This is intentional because self-attention is to be used as one of the many heads in this layer.

## Multi-head Attention

As said before, the self-attention is used as one of the heads of the multi-headed. Each head performs their own self-attention process, which means, they have separate Q, K and V and also have different output vector of size (4, 64) in our example.

To produce the required output vector with correct dimension (4, 512), all heads will combine their output by concatenating with each other.

![concatenation]()

To produce the output, the combined output is multiplied with output projection matrix.

![output projection matrix](assets/images/multi-head-evolution.png)

This is the output for the multi-headed attention layer whcih is used for the next stage.
