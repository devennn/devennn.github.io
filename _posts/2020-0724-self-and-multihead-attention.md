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

When Transformer is introduced, the concept of self-attention becomes important. The main function is to produce a vector that is related to the word being focused.

![output of self-attention](/assets/images/self-attention-input-output.png)

Zooming in, these are the process taken to produce the output.

![process of self-attention](/assets/images/self-attention-process.png)

#### Process 1 - Word embedding to Query, Key and Value

There are three key parameters introduced in this concept. Query, Key and Value. To avoid confusion, I will use Q, K and V to reference them.

These vectors are calculated from the word embedding. Another way to understand this process is the input embedding is translated to three different versions of itself. From embedding space to Q, K and V space.

This is done by projecting the input to three different set of weights, i.e. Query weight, Key weight and Value weight. All weights act as a projection medium which are trainable. 

Each word in the sequence will have their own query, key and value. 

![query, key and value process](/assets/images/transformer_self_attention_vec.png)

[*Image Source: jalammar.github.io*](https://jalammar.github.io/illustrated-transformer/)

One thing to note is all three values actually mean nothing. They are just mathematical representations of the input word in different vector space which can be used for calculations. That's it.  

#### Process 2 - Calculating Attention Score

Up to here, the input embedding has been projected to Q, K and V spaces. In this process, Q and K are used for calculating the attention score. 

Let's say we want to encode ```eat``` from the sentence ```He eat fried noodles```, after process 1, all input words will have Q, K and V as below.

![Q, K and V for every word](/assets/images/qkv_result.png)

In process 2, the scores are calculated by performing dot products of Q of ```eat``` with transposed K of every words in the sequence, including itself. The result for a single word is a vector of size (1, 4) which holds the value of every dot product result.

![score calculation](/assets/images/score_calculation.png)

Then, every score is divided by 8. According to this source, *"This leads to having more stable gradients"* [source](https://jalammar.github.io/illustrated-transformer/). 

```
score = [32, 88, 56, 72]

after divide by 8
score = [4, 11, 7, 9]
```
The attention score is calculated by applying softmax function to the all values in the vector.

![softmax function](/assets/images/softmax_function.png)

This will adjust the scores to add up to 1.

```
Softmax result

softmax_score = [0.0008, 0.87, 0.0016, 0.012]

```

The attention score indicate importance of word in the context of word being encoded, whcih is ```eat```. Higher value, higher importance.

#### Process 3 - Calculating Output Vector

The last process is to produce the output vector. Up to here, we have the attention score and V. 

Here, the softmax scores are multiplied to their respective V. Then the values are added together to produce the output vector. 

![Output vector calculation](/assets/images/attention_score.png)

This is the end of self-attention.

## Multi-head Attention
