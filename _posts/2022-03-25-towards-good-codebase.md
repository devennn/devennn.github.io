---
layout: post
title: Good coding practice - Remove Json object
description: "Increase readibility of codebase"
keywords: "Coding practice"
---

I always try to produce a readable code. Making them short and sweet. Abstracting classes to hide the unrelated process. And probably make a config file to cater for different use case without touching the codebase.

Today I want to share another way to make your code readable by focusing on the data flow. Heads up, this may or may not increase the lines of code. But certainly it helps to increase the readibility.

## Reason to carefully format data flow

What do I mean by data flow? Take an example of a server, it has api endpoint, middleware and database. When processing happens in the middleware, some data might lose its shape. The more complicated the process, the easier it is for the developer to make mistake. 

For some case, bad data flow hides error. What can be detected during compile time now become a runtime error. Let's look at how to handle this.

## Remove Json object for argument and return 

Somehow when extracting api body, data comes(most of the time) by default as json. And the developer have to check if the data is valid. Performing validation is quite a hassle. Of course it is easy to do if you are the one that build and maintain the features.

However, it will be confusing for someone that is entirely new to it.

One way to go about this problem is to use the exact data type. Do not take shortcut by putting everything in a json object to pass/return. If a definite structure of data is returned, use data transfer object (DTO). 

This method acts as a template for the data structure. It enables automatic checking of data structure and also type of each data in the structure. Change API json body to DTO right at the point of entry. Another benefit of doing so is error hanldling of data incompatibility can be done as quick as possible. Save time!

Here are some good tutorial on how to build DTO
- [For Java](https://www.javaguides.net/2018/08/data-transfer-object-design-pattern-in-java.html)
- [For Python](https://pynative.com/python-convert-json-data-into-custom-python-object/)

