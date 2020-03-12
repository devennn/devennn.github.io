---
layout: post
title: Rungus language corpus - Need more data
published: false
---

I have been playing with the dataset to train a machine translation model. The results are not good which makes me take a step behind to see what can be improved. 

# Why I don't get good results

So, I have 2 theories:
- Not enough data for training and validation
- Bad models

From training, most of the times I end up with these following situations.
- Value accuracy increase and start to decrease
- Both accuracy and loss decreases

Overfitting! The most sensible explanation that I can think is not having enough data. Hence, I need to collect enough data as much as possible.

# Data collection strategy

There are not many public sources such as blogs or public dictionary available that can be scrapped. So, I am looking into other possible media sources.

## eBooks and other pdf

There is a dictionary of english - rungus translation available for Free download. It has lots of new translation which can increase the vocabulary of the data. Other pdf such as translated book will also be useful. This data will provide structured formal sentences with correct grammar and word usage.

## Media

Media, such as radio channel and videos are good source of continuous data. This data will provide informal language that is used in everyday conversation.

To collect data from these sources, automated transcribing data will be used.
