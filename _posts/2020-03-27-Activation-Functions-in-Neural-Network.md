---
layout: post
title: Activation Functions in Neural Network
published: false
---
Activation function is a way to represent the output base on a given set of input. In neural network, they are used to summarize all input values of a neuron into a range of lower and upper limit. The lower and upper limit are based on types of functions used. 

1. Linear
2. Relu
Rectifier liner unit. It transform the value to either 0 or no the value itself. So, the lower limit is 0 and the upper limit is the maximum value in the input sequence.

When the input is less than or equal to 0, the function will transform the value to be 0, whereas if the input is more than 0, the function will output the value itself 

3. Softmax
4. Sigmoid
image
Sigmoid has a lower limit of 0 and upper limit of 1. It has binary simmilarity which makes it used in binary related output classifications. The main takeaway of this is when features value are mostly negative, they are likely to be 0 when sigmoid activation function is used. So, the closer to 0 the activation output, the less activated the neuron is.


5. Tanh

The formula for Tanh function is... It works almost the same as sigmoid function. Rather than scaling from 0 to 1, tanh scale its output from -1 to 1. 

Usually, we want to have ranges to denote the hidden layer which is centered at 0. 
