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

LSTM cell code interpretation
```python
# ft = forget gate output
# it = input gate output
# Ct = candidate cell state
# ot = output gate output
# ht = hidden state
def LSTM_cell(prev_cell_state, prev_hiddden_state, input):
  combine = perv_hidden_state + input #vector concatenation
  ft = forget_layer(combine)
  candidate = candidate_layer(combine)
  it = input_layer(combine)
  Ct = (prev_cell_state * ft) + (candidate * it)
  ot = output_layer(combine)
  ht = ot * tanh(Ct)
  
  return ht, Ct
  
  # Control flow
  # Using generated ct and ht for next iteration prediction
  ct = [0, 0, 0]
  ht = [0, 0, 0]
  for input in inputs:
    Ct, ht = LSTM_cell9cCt, ht, input)
 ```
 
 GRU update gate act similar to forget and input gate. decide what information to throw away and to add
 GRU reset gate used to decide how much pass information to forget
 GRU has less tensor operation
