---
layout: post
published: false
title: Understanding LSTM for sentiment analysis
---

Humans interpret based on context. We do not try to understand everything without connecting the "dots" of information. In other words, when we make decisions, it is always based on what happened before. We pass data from one stage to another, which builds up the context of problems or situations, including text interpretation.

The key to understand is to listen. When we listen, we try to understand what the word means. Then, we pass the information to our future self, which will then interpret. It is human's nature to understand word sequences

Sequence dependence learning is associated with RNN or Recurrent Neural Network. LSTM or Long Short Term Memory is a type of RNN with a powerful application. The main reason that makes it useful in sequence learning is the ability avoids long-term dependency problems. They can remember information for a long time.

## Understanding components of LSTM

## Using LSTM for sentiment analysis

Let's see if the model is able to perform sequence learning to decide Good and Bad reviews.

In this example, i will be using:
- IMDB Keras dataset
- Keras with Tensorflow backend for modelling

### Explore Data

Start by loading IMDB dataset
```python
from tensorflow.keras.datasets import imdb

(X_train, y_train), (X_test, y_test) = imdb.load_data(num_words=10000)
```
I set the maximum words to be 10000. This parameter will cause the function to return only the top 10000 frequent words from the corpus.

Let's explore the loaded data
```python
print('=== Data ====\n{}\n\n=== Sentiment ====\n{}\n\n{}'.format(
    X_train[5], y_train[5], type(X_train)))
    
# Default value in load_data
INDEX_FROM = 3

# Download word index and prepare word id
word2id = imdb.get_word_index()
word2id = {word:(word_id + INDEX_FROM) for (word, word_id) in word2id.items()}
# Labelling predefined value to prevent error
word2id["<PAD>"] = 0
word2id["<START>"] = 1
word2id["<UNK>"] = 2
word2id["<UNUSED>"] = 3

# Prepare id to word
id2word = {value:key for key, value in word2id.items()}

print('=== Tokenized sentences words ===')
print(' '.join(id2word[word_id] for word_id in X_train[5]))
```

Output:
```
=== Data ====
[1, 778, 128, 74, 12, 630, 163, 15, 4, 1766, 7982, 1051, 2, 32, 85, 156, 45, 40, 148, 139, 121, 664, 665, 10, 10, 1361, 173, 4, 749, 2, 16, 3804, 8, 4, 226, 65, 12, 43, 127, 24, 2, 10, 10]

=== Sentiment ====
0

<class 'numpy.ndarray'>

=== Tokenized sentences words ===
<START> begins better than it ends funny that the russian submarine crew <UNK> all other actors it's like those scenes where documentary shots br br spoiler part the message <UNK> was contrary to the whole story it just does not <UNK> br br
```

Looks like the corpus has been preprocessed to numpy array. Every review is represented as word index sequence. The sentiment is in binary. So, there is no need to perform manual categorizing.

Another things to observe from the reconstructed sentence are <start> and <UNK>.
  - <start> : This is to set the boundary such as informing the starting point of sentence
  - <UNK> : Label new words that is not present in English dictionary.
  
### Define Model

### Perform Training and Evaluation

Obtain full code [here](https://www.kaggle.com/deventommy96/lstm-sentiment)
