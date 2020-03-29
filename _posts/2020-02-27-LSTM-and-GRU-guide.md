---
layout: post
title: Intuition of LSTM and GRU
published: false
---

# Purpose of LSTM and GRU

they are the evolve version of vanilla recurrent neural network.
suffer from vanishing gradients
[formula]
during back propogation ... gradient value that is too small, it will not update the new weights too much. thus not contribute to learning. this is usually the earlier layers causing the entry layers to have short term memory when it fed with longer sequence, thus having short term memory

LSTM and GRU are creatred to solve short term memory. They have gates which regulate the flow of information. These gates can be used to decide which information is to keep or throw away.
