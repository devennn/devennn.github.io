---
layout: post
title: Applying CNN for texts classification
description: "Diffferences and practical example of CNN for texts"
keywords: "CNN, NLP, Images"
published: false
---

In this post, I would like to write about the differences of the application of CNN for image versus text.

Convolutional Neural Network or CNN is a well known for its ability to detect local features. As the time of writing, this algorithm is used in every SOTA image classification model. However, it is not a well known method in the NLP classification domain. 

### How CNN works for Image

If someone asks me to describe CNN in simple terms, I would say *"algorithm that search for local features in an image."* Basically, it is observing signs of shapes. This approach is very useful in a classification task.

The usual classification process of an image is usually as below:

```
1. Convolution Layer
2. Max Pooling
3. Matrix Flattening
4. Fully Connected layer
```

![image](/assets/images/cnn_standard_process.png)
[Source](https://www.researchgate.net/profile/Tao_Jiang10/publication/320441691/figure/fig3/AS:631629156462608@1527603534394/A-CNN-architecture-that-adds-convolution-layers-and-pooling-layers-before-dense-layers.png)

#### Convolutional Layer
Humans classify an image by observing shapes to form an object. Almost the same idea is applied here. The only difference is CNN observe much more detail representation of shape. 

It uses feature detector called filter. At the begining of training, all filters are randomly initialized. Then, the model will tune the filter according to what features best describe the label.

The process undergo element-wise multiplication of input and filter matrix. This process produce a feature map according to the filter. As the filter become more descriptive, the feature map will show a better sumamry of the image.

#### Max Pooling
The feature map holds the summary of the input image with respect to the filter. The bigger the image, the more result CNN layer produce, which means the model will have more parameters and will take longer time to process.

This problem can be solved by pooling the information. The bigest number from the feaure map will be taken to describe the feature. The bigger the number, the more prominent the features. In this way, the size of image is reduced without losing too much information.

#### Matrix Flatening
At some point, the output must be a vector or 1D matrix. This is where falttening does its job. From previous stage, we understand that the output is still a 2D matrix. At this point, the matrix will be flatten by rearranging it according to its position in the space.

The output will be a vector.

#### Fully Connected Layer

This is the standard neural network which consist of tunable parameters and connected to the output layer. 

### The main difference between Image and Text data

Before going to CNN for texts, we need to understand the difference of both data type (image vs text). What we know so far is CNN is used to detect features of matrix. So, both image and text data have to be in the form of 2D matrix. 

Lets assume both input matrix has the size of 128 by 128. What can we understand from that?

#### Image  

For image, this can be understood as image of having width of 128 pixels and length of 128 pixels. A square image.

Further observation tells us that every pixels surrounding a pixel (top, bottom, left, right, top-right, bottom-right, etc) contribute to the meaning of the whole image. This is why the filter slides sideways and downwards because it needs to take into account the neighbouring pixels.

#### Text

Text data has a different representation. Since, words are used in sequences, also known as sentences, every input data have to be in sequential manners. That means, every sentence should form an input matrix according to the input size of the model.

The number of words in a sentence + padding make up the length of input matrix. The padding is added to match the input neurons. For model with length of 7 inputs, the data matrix size should be as below:

```
Input Sentence: This is a ball

Input matrix. Size (7, 4).

This    [0.1, 0.2, 0.01, 0.7]
is      [0.3, 0.21, 0.34, 0.23] 
a       [0.42, 0.17, 0.74, 0.57]
ball    [0.11, 0.38, 0.14, 0.24]
pad     [0, 0, 0, 0]
pad     [0, 0, 0, 0]
pad     [0, 0, 0, 0]
```

In NLP, one most preferred method to describe words is using word embeddings. There are some good tutorials on this topic. The main idea of word embedding is to represent a word in the vector space.

Each word is represent as a 1 by N vector. For example above, the N value is 4. There is no rule of thumb for the number. The length of BERT embeddings is 768. This value is experimental.

So, going back to the initial example, if the size of text input data is 128 by 128, it means the maximum length of sentence is 128 words and the length of every word embeddings is 128. 

### How CNN works for Text

If you get the idea of word embedding, you will understand two things:
- Every row represents a word
- Matrix columns mean nothing. They are just points to represent the word in the vector space. 

That means, using a filter that slide from left to right is useless. The features of a word in a sentence is its neighbouring words also called as context words.

#### Context words

If you are familiar with Word2Vec, there is a parameter called window size, which determine how many words to the left and right are considered as contexts. For example:

```
Sentence: The brown fox jump over the red fence
window size = 2
```

If we are checking for the word ```jump```, the context words are:
- 2 words to its left: ```brown, fox```
- 2 words to its right: ```over, the```

#### CNN filter for text

Because words are arrange row-wise, the filter should slide according to row. The variation of feature detector is determined by the value of context words.

#### Pooling

This layer serve the same purpose as in the image, to reduce size. Only the maximum value will be taken to represent the feature. As you can see, the output is already in vector format. So, there is no need to apply any more flatten process. 

#### Fully Connected Layer

And the last layer should be the standard neural network, which is connected to the output neurons.

### Conclusion

In this post, I hope:
- You gain good insights regarding the intuition of CNN for both application. 
- You see the difference in process
- You understand the input format of both process.

Thanks for reading!
