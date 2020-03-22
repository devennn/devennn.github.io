---
layout: post
title: Understanding Encoder-Decoder with Attention for NMT using PyTorch
published: false
---

One primary application that get me interested in AI is Natural Language Processing, primarily machine translation. My parents are both native Sabahan (Dusun and Rungus). Native group in Sabah Malaysia usually have their own language which make them unique. But, the problem is, I could not speak even one, neither understand even a little bit of the words. This is what gives me idea of applying NLP to solve my slow learning problem. Sigh...

For this article, I will be using Malay language, Malaysia's primary language. The corpus is available [here](https://github.com/huseinzol05/Malaya-Dataset/tree/master/english-malay). I did try with my own Rungus [corpus](https://github.com/devennn/rungus-language-corpus). The result tends to overfit because it is performing 100% evaluation and not be able to "translate" new sequence of sentence. My guess is because of less data.

The code used is taken directly from [Pytorch tutroial on NMT](https://pytorch.org/tutorials/intermediate/seq2seq_translation_tutorial.html#sphx-glr-intermediate-seq2seq-translation-tutorial-py). The aim for this article is to add on to the tutorial by going in depth about Encoder-Decoder with Attention algorithm. I will be discussing on the basic idea, how the code works and the Math of it. 

# Preprocessing

# Introduction on Encoder, Decoder and Attention

When human make conversion from one state to another, most of the times, we are performing encoding and ecoding. For example, if someone ask a question like "Can you pass me the spice for curry?", we try to get insights of the question by performing simple judgement in our head such as:
- What is spice?
- What is curry?
- Which one is spice for curry?
- How do I pass the spice?

These simple questions, will dictate out reaction. Human are used to encode and decode. In the example above, we are encoding the questions to our own understanding and decoding them in terms of action. One thing to note is that our understanding is something that is unique, it can be the same or totaly different than others. In other words, our understanding is the higher dimension of the input questions. Putting it all together, we can see that in the process of Encoding and Decoding, there is a higher dimension involve. We can say that it is the middle part of the process where it is very useful for the output.

Encoder and Decoder algorithm works almost the same as the illustration given. It is also known as sequence to sequence or seq2seq, and as the name suggest it works by Encoding sequence of data to higher dimension and encode them to target output sequence. 

[IMAGE]

In the image above, the final states is the higher dimension of the Encoder. This is used in the decoder part for prediction. In theory, this model will work. However, as longer sentence is fed to the model, more information are required to be captured. The higher dimension of the encoder which is a vector that holds information will need to "summarize" the information. So, longer sentences will result in poor vector representation as only the last hidden state of the encdoer model is used in the decoder section. Will explore more into this concept in the later part. There are different ways to tackle this problem. One of them, that I'm using, is the Attention Mechanism.

The mechanism solves the problem stated just now. Rather than using only the last part of the model, it retains and uses all hidden states from the encoder when decoding the input sequence in the decoder section. This technique amplify the usage of mini sequence from whole input sequence. For example, ```I love my family``` will be utilizes fully by processing each mini sequence which looks like
```
> I
> I love
> I love my
> I love my family
```
As said before, this approach is useful for longer sequences(which is usualy the case) and has been the starting point of many state of the art model.

# Understanding Encoder, Decoder and Attention

# Train, Evaluate and observing results and performance

# Conclusion
