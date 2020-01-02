---
layout: post
title: Regression vs Classification basic intuition
---

When to use them? How do I know if this is the right algorithm to use? Asking these questions is crucial when developing a predictive model. I've encountered few experiences where classification gives me higher accuracy during training. When predicting, regression is more accurate. Sometimes, the other way around.

I would like to think that accuracy metrics does not matter if "wrong" algorithm is used. Instead, "lower" accuracy using the right one for  the prediction goals should be focused. Imagine this; Would anyone use motorbike to pick up few friends? Going to the place is faster than using car. But, it is still not the right tool. In other words, use the right tool for the right goals.

What I have learned so far, the first step to build prediction model is to understand the goal. Take an example of weather data. I need to ask myself:
- Am I predicting the possibilities of raining today? or
- Am I trying to predict if today will be raining or not?

Then I can decide which algorithm to use.

Simply said. Regression predicts continuous output. Classification predicts discrete output.

![regression-vs-classification-plot](https://res.cloudinary.com/practicaldev/image/fetch/s--c4Lfzdwy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mjshszqx4fj22hs12vfn.png)

Regression take input data x to find the response, y classification take input data x and find which gorup, class does it belong to, y.

Going back to the example of weather to predict possibilities. The possibilities(%) lies between 0 - 100%. This is a continuous output which should use regression. But, can I use classification for this data? yes! Then, the question is, how do I label every data? If i am crazy enough, I can label 0%, 1%, 2% ... 100%. That will give me 101 labels and surely classifying them will be hard. Plus, i have to consider if they fall in between such as 55%. So, surely, classification is usable, but not practical.

Again, looking at the next example. Predict if today will rain or not? or "raining? yes or no?". The goal is to predict 2 classes. and this classes can be labelled as:
- 0 - 50% (no today will not rain)
- 51 - 100% (yes today will rain) 
With this, all numbers in between have been considered. The output discrete as I just want to know the 2 outcome. 

Observing the example above, I can conclude that, whenever I am building a model that may require too much (infinite) number of class, maybe regression is a better algorithm to use. And whenever, i need to build a model that is able to group the data, classification is the way to go.
