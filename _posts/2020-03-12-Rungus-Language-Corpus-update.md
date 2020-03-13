---
layout: post
title: Rungus language corpus - Need more data
---

I have been playing with the dataset to train a machine translation model. The results are not good, which makes me take a step behind to see what can be improved. 

# Why I don't get good results

I have two theories:
- Not enough data for training and validation
- Bad models
- Bad project management

From training, most of the times I end up with these following situations.
- Value accuracy increase and start to decrease
- Both accuracy and loss decreases

Overfitting! The most sensible explanation that I can think is not having enough data which cause the model unable to generalize training and testing data.

I could blame bad models. However, in language modelling, especially building a language corpus for general use, data size is essential. Microsoft Researchers Banko and Brill in their research, [_Scaling to Very Very Large Corpora for Natural Language Disambiguation_](https://www.aclweb.org/anthology/P01-1005.pdf) conclude that additional training data does contribute to a performance increase of tested model. They also suggest an effort to increase the size of training collections and less focus on comparing learning technique on small training data.

Starting from here, I plan to do more data collection. But, I am not ready to ditch the machine translation model yet. So, I plan to use some open-source NMT library. It will reduce the time to research about machine translation while still making progress on it.

# Data collection strategy

There are not many public sources such as blogs or dictionary available on the internet, which makes data scrapping either manual or automated unuseful. So, I am looking into other possible media sources.

## eBooks and other pdf

There is a dictionary of English - Rungus translation available for free download. It has lots of new translation which can increase the vocabulary of the data. Other pdf such as translated book will also be useful. This data will provide structured formal sentences with correct grammar and word usage.

The challenge will be on how to parse pdf. The optimal solution is to automate them so I can feed .pdf, and it will produce wanted data. Up to now, I couldn't figure out one algorithm fits all technique since every pdf has different format even though they are just strings. What I mean is, some pdf have lots of Unicode and images which is more challenging to parse than pure words. I am currently working on parsing a dictionary pdf which is purely words with some Unicode. 

While doing this, I am observing the template to find a possible method of automating the task.

## Media

Media, such as radio channel and videos are good sources of continuous data. This data will provide informal language that is used in everyday conversation. Automated transcribing data can be used to collect data from these sources.

Some challenge that should be noticed is the accuracy of word spelling. Not having the right spelling will create new word instead of adding to the existing list. One method to solve this problem is to use a similar words algorithm. The algorithm can find "close words" that may or may not be the right words. It will not solve the problem entirely, but certainly, help to reduce them.

I hope to make better progress with these new focus. You can view full data repositiory [here](https://github.com/devennn/rungus-language-corpus)
