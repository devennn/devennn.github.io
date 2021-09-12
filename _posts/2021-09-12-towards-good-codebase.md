---
layout: post
title: Three data loaders for almost all Keras applications
description: "Keras Data Loader for Image Dataset"
keywords: "Keras, Data Loader, Images"
published: false
---

I always try to make my code easy to read. Making them short and sweet. Abstracting classes to hide the unrelated process. And probably make a config file to cater for different use case without touching the codebase.

Today I want to share another way to make your code readable by focusing on the data flow. Heads up, this may or may not increase the lines of code. But certainly it helps to increase the readibility.

What do I mean by data flow? Take an example of a server, it has api endpoint, middleware and database. When processing happens in the middleware, some data might lose its shape. The more complicated the process, the easier it is for the developer to make mistake. For some case, bad data handling hides error. What can be detected during compile time now become a runtime error. Let's look at how to handle this.

## Use specific data type for argument and return 
## Reduce steps to the innermost process
