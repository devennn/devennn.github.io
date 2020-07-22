---
layout: post
title: Understanding Self and Multi-Head Attention
description: "Explaining the workings of self-attention and multi-head attention"
keywords: "attention, bert"
published: false
---

## Purpose of attention

## Self-Attention

When Transformer is introduced, the concept of self-attention becomes important. The main function is to produce a vector that is related to the word being focused.

![output of self-attention]()

Zooming in, these are the process taken to produce the output.

![process of self-attention]()

#### Process 1 - Word embedding to Query, Key and Value

There are three key parameters introduced in this concept. Query, Key and Value. These values are calculated from the word embedding. Another way to understand this process is the input embedding is translated to three different versions of itself. From embedding space to query, key and value space.

This is done by projecting the input to three different set of weights, i.e. Query weight, Key weight and Value weight. All weights act as medium conversion which are trainable.  

![query, key and value process]()

One thing to note is all three values actually mean nothing. They are just mathematical representations of the input word in different vector space which can be used for calculations. That's it.  

#### Process 2 - Calculating Attention Score

#### Process 3 - Calculating Output Vector

## Multi-head Attention