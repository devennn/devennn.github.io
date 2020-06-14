---
layout: post
title: Find similar words in Word2Vec embeddings
published: false
---

Word2Vec is no doubt a powerful algorithm to compute word embeddings. The one layer network make it consume less compute power while producing excellent result compared to other embeddings algorithm. One of the downside of Word2Vec embeddings model is the inability to identify new words. In this post, I tested a simple method to overcome this problem. 

The test dataset used is the COVID-19 Malaysian tweets. I also pretrained a Word2Vec model on Malaysian Tweets. One interesting observation on Twitter dataset is the variation of words caused by typo, prefix or someone just think it is better to add extra letter :satisfied:.
```
psttt -> ['pssttt', 'pstt', 'psstttt', 'psssttt']

entrances -> ['entrance', 'entranced', 'enterance', 'entrace']
```

The word ```psttt``` is not a formal word. However, we definitely understand what it means. So, we don't really care how the word is spelled. The second word, ```entrances``` exists in english dictionary. We may or may not care how it is spelled as long as we understand it. For human, most of the times, we're able to self-correct a word. For a computer, even one letter difference is enough to convice the words are not the same. 

# Predict unknown words

Since word embeddings are trained based on context words, we can use this idea to predict similar words as well. We understand that frequent words in the tranining corpus will have better embedding representation. In a social media corpus, this idea can be tricky since we don't know what is the most used words. In the example above, we don't know which of the word ```psttt``` exists frequently. But, that doesn't matter since we only want to find the similar words. So, any variation is okay as long as the word make sense for computer and human especially. 

With this understanding, the plan overview is:
1. Generate simillar words from vocabulary - I am using difflib get_close_matches here. It will generate 7 close match string from the model vocabulary list. 
```python
 results = difflib.get_close_matches(word, model_vocab, n=7)
```

2. Compare every similar strings to the context words from the sentence
```python
similarity_score = [model.wv.similarity(r, token) for r in results]
```

3. Find the highest similarity score for each similar words.
```python
results[similarity_score.index(max(similarity_score))]
```

That's it! Below is the full implementation. The unknown word and its sentence is passed to the arguments.
```python
def count_words(words):
  d = {}
  for w in words:
    if w in d: d[w] += 1
    else: d[w] = 1
  return {k: v for k, v in sorted(d.items(), key=lambda item: item[1], reverse=True)}
  
def find_closest_string_with_embeddings(word, sentence):
  tokens = sentence.split()
  results = difflib.get_close_matches(word, model_vocab, n=7)
  simmilarity_score, similar_word = [], []
  for token in tokens:
    try:
      # get similarity score for all tokens vs difflib result
      similarity_score = [model.wv.similarity(r, token) for r in results]
      # find highest scoring word from each results
      similar_word.append(results[similarity_score.index(max(similarity_score))])
    except KeyError:
      # Remove words to be predicted
      pass

  print("Unknown Word -> {}".format(word))
  print("Full sentence -> {}".format(sentence))
  print("Similar words -> {}".format(results))
  print("Similar by embedding -> {}".format(similar_word))
  print("Most similar word -> {}".format(count_words(similar_word)))
```
I am using try except method to skip unknown word. The model.wv.similarity will raise KeyError if the word is not in vocabulary. count_words is a helper function used to sort predicted word according to value.

# Correct Results
```python
word_tests = ['moleq', 'lariss', 'sesssion', 'dipermudohkn', 'dinobat']
for word in word_tests:
  text = get_sentence(word)
  find_closest_string_with_embeddings(word, text)
  print()
```
These words are which that yeilds good results as expected.

```
Unknown Word -> moleq
Full sentence -> gym pom dh sepi last day before officially lock down yg mane diberi cuti tu sila duk diam kt rumah yg mane kerja tu selamat bekerja jaga diri moleq blako jangan kerana corona hilang iman pedoman
Similar words -> ['mole', 'moole', 'moles', 'molep', 'molek', 'moleh', 'mohle']
Similar by embedding -> ['mole', 'molek', 'molek', 'moleh', 'mole', 'moles', 'mole', 'mohle', 'mole', 'mole', 'molek', 'molek', 'moleh', 'molek', 'molek', 'molek', 'molek', 'molek', 'molek', 'molek', 'molek', 'molek', 'molek', 'molek', 'moole', 'moleh', 'molek', 'molek', 'molep', 'molek', 'moleh', 'mole', 'molek', 'molek', 'mole']
Most similar word -> {'molek': 20, 'mole': 7, 'moleh': 4, 'moles': 1, 'mohle': 1, 'moole': 1, 'molep': 1}

Unknown Word -> lariss
Full sentence -> harap ketupat paling lariss y
Similar words -> ['larissa', 'larss', 'laris', 'laiss', 'clarissa', 'mariss', 'laisse']
Similar by embedding -> ['laris', 'laris', 'laris', 'laiss']
Most similar word -> {'laris': 3, 'laiss': 1}

Unknown Word -> sesssion
Full sentence -> covid pendamic chow kit wet market disinfection sesssion a potrait of city hall worker during the disinfection process at the local wet market in kuala lumpur malaysia
Similar words -> ['session', 'sessions', 'posession', 'obsession', 'cession', 'succession', 'sessionist']
Similar by embedding -> ['succession', 'posession', 'sessionist', 'obsession', 'obsession', 'sessions', 'sessions', 'sessions', 'session', 'sessions', 'sessions', 'session', 'obsession', 'sessions', 'sessions', 'sessions', 'succession', 'sessions', 'sessions', 'obsession', 'obsession', 'sessions', 'sessions', 'sessions', 'sessions', 'succession']
Most similar word -> {'sessions': 14, 'obsession': 5, 'succession': 3, 'session': 2, 'posession': 1, 'sessionist': 1}

Unknown Word -> dipermudohkn
Full sentence -> semoga dipermudohkn urusan pembahagian pada penduduk tdtd dapat meringankan sedikit beban bagi yang kurang berkemampuan
Similar words -> ['dipermudhkn', 'dipermudhkan', 'dipermudahkn', 'dpermudhkn', 'diprmudhkn', 'dipermdhkn', 'dipermudahkan']
Similar by embedding -> ['dipermudahkan', 'dipermudahkan', 'dipermdhkn', 'dipermudahkan', 'dipermudahkan', 'dipermudahkan', 'dipermudhkn', 'dipermudhkn', 'dipermudahkan', 'dipermudahkan', 'dipermudahkan', 'dipermudahkan', 'dipermudahkan']
Most similar word -> {'dipermudahkan': 10, 'dipermudhkn': 2, 'dipermdhkn': 1}

Unknown Word -> dinobat
Full sentence -> terpegun dengar jawapan datuk dr ketua pengarah kesihatan ketika jawab soalan sukar media boleh dapat pelbagai ilmu baru patutlah dinobat antara terbaik kendali kes di dunia
Similar words -> ['diobati', 'dingbat', 'nobat', 'inoba', 'dinot', 'dinobatkan', 'dikoba']
Similar by embedding -> ['inoba', 'nobat', 'nobat', 'dinobatkan', 'nobat', 'dinobatkan', 'dinobatkan', 'nobat', 'dinobatkan', 'nobat', 'dinobatkan', 'dinobatkan', 'nobat', 'nobat', 'dinobatkan', 'dinobatkan', 'dinobatkan', 'nobat', 'nobat', 'dinobatkan', 'dinobatkan', 'dinot', 'dinobatkan', 'dinobatkan', 'dinobatkan']
Most similar word -> {'dinobatkan': 14, 'nobat': 9, 'inoba': 1, 'dinot': 1}
```
1) For ```moleq```, ```lariss``` and ```session```, the most similar word produced by difflib is wrong, but the correct word is still in the list. By comparing word embeddings, the correct word is recognized.

2) For ```sesssion```, ```dipermudohkan``` and ```dinobat```, difflib works well to find all simmilar words as almost all the words, including the most similar are correct. When compared with embedding, higher context word is chosen. This is caused by frequency of the word in the training corpus.

# Wrong Results
```python
word_tests = ['reassemble', 'compasses', 'boleeeeeh', 'meningatkan', 'bomathi']
for word in word_tests:
  text = get_sentence(word)
  find_closest_string_with_embeddings(word, text)
  print()
```

The wrong results

```
Unknown Word -> reassemble
Full sentence -> day avengers reassemble psst admin kka sorg tu blh start charging suit dah mrs potts stark kan from malaysiaigers level pkp fasa polis fasa tentera fasa
Similar words -> ['ressemble', 'ressemblez', 'ressembles', 'ressembler', 'resemble', 'assemble', 'ressemblent']
Similar by embedding -> ['resemble', 'ressemblent', 'ressemblez', 'ressemblent', 'ressemble', 'ressembles', 'ressemblent', 'ressembles', 'resemble', 'assemble', 'ressembles', 'ressemblent', 'ressembles', 'ressembler', 'ressembles', 'assemble', 'assemble', 'assemble', 'assemble', 'ressembler', 'assemble', 'ressembler', 'assemble']
Most similar word -> {'assemble': 7, 'ressembles': 5, 'ressemblent': 4, 'ressembler': 3, 'resemble': 2, 'ressemblez': 1, 'ressemble': 1}

Unknown Word -> compasses
Full sentence -> thank you and all workers for being there for us in all over the world it s time to reset our compasses ramp up compassion kindness to everyone we can beat this
Similar words -> ['encompasses', 'compass', 'compares', 'passes', 'masses', 'encompassed', 'compas']
Similar by embedding -> ['compares', 'masses', 'masses', 'masses', 'masses', 'compass', 'masses', 'masses', 'compass', 'masses', 'compass', 'masses', 'masses', 'masses', 'masses', 'masses', 'passes', 'masses', 'masses', 'compass', 'masses', 'passes', 'compass', 'masses', 'masses', 'masses', 'masses', 'masses', 'passes', 'compares', 'masses']
Most similar word -> {'masses': 21, 'compass': 5, 'passes': 3, 'compares': 2}

Unknown Word -> boleeeeeh
Full sentence -> cooking a sikit for our volunteer lunch tadi diet apa diet kita diet esok pun boleeeeeh
Similar words -> ['boleeeeh', 'boleeeee', 'boleeeeee', 'boleeeh', 'boleeee', 'leeeee', 'eeeeeh']
Similar by embedding -> ['boleeeeh', 'eeeeeh', 'boleeeh', 'eeeeeh', 'boleeeh', 'boleeee', 'eeeeeh', 'boleeeh', 'eeeeeh', 'boleeee', 'eeeeeh', 'leeeee', 'eeeeeh', 'eeeeeh', 'boleeeee']
Most similar word -> {'eeeeeh': 7, 'boleeeh': 3, 'boleeee': 2, 'boleeeeh': 1, 'leeeee': 1, 'boleeeee': 1}

Unknown Word -> meningatkan
Full sentence -> saudara saudari rakyat dll mari kita saling bantu meningatkan satu sama lain perjuangan untuk menghentikan penularan boleh di menang sayangi pekerja dll
Similar words -> ['meningkatkan', 'mengingatkan', 'mngingatkan', 'meningalkan', 'mengingatkn', 'mengingatkanku', 'meninggalkan']
Similar by embedding -> ['mengingatkan', 'mengingatkan', 'meningkatkan', 'meningkatkan', 'mengingatkn', 'mengingatkan', 'mengingatkan', 'meningkatkan', 'meningkatkan', 'meninggalkan', 'mengingatkan', 'mengingatkan', 'meningkatkan', 'meningkatkan', 'meningkatkan', 'meningkatkan', 'meninggalkan', 'mengingatkan', 'mengingatkan', 'meningkatkan', 'meningkatkan']
Most similar word -> {'meningkatkan': 10, 'mengingatkan': 8, 'meninggalkan': 2, 'mengingatkn': 1}

Unknown Word -> bomathi
Full sentence -> hadive udhares nufenevi dhane ey bandilaa nagaa bomathi koh nimijjey varugadha kolhigandeh ge vai rajje ah misraabu vejje anna oiy thoofaanun salamaiy vumah zuvaabu nukoh kudhi bodu enmen masahkaiy kuran vejje ge balimadukamun alhamen minju kuravaafaandeyve aameen
Similar words -> ['mathi', 'bathi', 'basmathi', 'momati', 'mathri', 'mathis', 'bumati']
Similar by embedding -> ['mathri', 'bumati', 'bumati', 'bathi', 'bumati', 'mathri', 'mathri', 'bumati', 'mathri', 'bathi', 'bumati', 'mathri']
Most similar word -> {'mathri': 5, 'bumati': 5, 'bathi': 2}
```
1) For ```reassemble```, ```compasses```, ```boleeeeeh``` and ```meningatkan```, difflib does a good job to find the most similar word. Most of the embedding score also produce the correct words. However, since this experiment uses every word in the sentence as the contexts, more noise are included in the similar by embedding list which outnumber the correct word. This can be observed from Most similar word list, where the second word for ```compasses```, ```boleeeeeh```, ```meningatkan``` is the correct word.

2) ```bomathi``` is not a malay or english word (or it is?). When plotting every word in the sentence, the result shows that most of the words are not in the vocab. While it still gives output, I have no idea if that is correct.

# Conclusion
One of the important parameter that should be focused is context word size. The pretrained model only used 5 word to the left and right as the context. That means, every every words after that range are considered noise as prove in the worng results above. 

You can view the full code [here](https://github.com/devennn/word_embeddings/blob/master/MYTweet_wordNotInVocab.ipynb).
