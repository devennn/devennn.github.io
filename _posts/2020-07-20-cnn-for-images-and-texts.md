---
layout: post
title: Applying CNN for texts classification
description: "Diffferences and practical example of CNN for texts"
keywords: "CNN, NLP, Images"
published: false
---

Convolutional Neural Network or CNN is a well known for its ability to detect local features. This algorithm is used in every SOTA model as of the time of writing.
However, it is not frequently used in the NLP world. In this post, I present the difference of CNN for image vs text.

### How CNN works for Image

If I would summarize the functionality of CNN, it would be "algorithm that search for local features in an image." You can say it is observing sign of shapes. It is very useful in a classification task. The usual process of an image related task is usually as below:

```
1. Convolution Layer
2. Max Pooling
3. Matrix Flattening
4. Fully Connected layer
```

#### Convolutional Layer
Humans classify an image by observing the shape. Almost the same idea is applied here. The only difference is CNN observe more detail representation of shape. 

It uses feature detector called filter. At the begining of training, all filters are randomly initialized. Then, the model will tune the filter according to what features best describe the label.

The process undergo element-wise multiplication of input and filter matrix. This process produce a feature map according to the filter. As the filter become more descriptive, the feature map will show a better sumamry of the image.

#### Max Pooling
From before, we know that the feature map holds the summary of the input image with respect to the filter. The bigger the image, the more result CNN layer produce, which means the model will have more parameters and will take longer time to process.

This problem can be solved by pooling the information. The bigest number from the matrix from CNN layer will be taken to describe if the features. The bigger the number, the more prominent the features. In this way, the size of image is reduced without losing too much information.

#### Matrix Flatening
At some point, the output must be a vector or 1D matrix. This is where falttening does its job. From previous stage, we understand that the output is still a 2D matrix. At this point, the matrix will be flatten by rearranging it according to its position in the space.

The output will be a vector.

#### Fully Connected Layer

This is the standard neural network layer which is tunable and connected to the output layer. 

### The main difference between Image and Text data

### How to CNN works for Text

