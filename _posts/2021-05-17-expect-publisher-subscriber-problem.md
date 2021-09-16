---
layout: post
title: Expect these three problems when using publisher-subscriber pattern in ML projects
description: "3 Problems to expect when using publisher-subscriber design pattern"
keywords: "design pattern"
published: false
---

I am lucky to be involved in lots of building process for machine learning system. And too often publisher-subscriber design pattern serves the need. It is a well known fact that this design pattern increase the possibility of scalling the services. The concept of loose coupling is the central idea whereby the input, in the case of server, the api is not integrated directly to the services. It has to pass through some central communication bus that acts as a transport to the services. 

Today, I want to share few problems that keep on popping up for this pattern.

## Event bus becomes too huge
## Subscribers services waste resources
## Communication bus might introduce latency
