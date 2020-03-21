---
layout: post
title: Analysis of English - Rungus language for NMT
---

The analysis is based on [en - drg language corpus](https://github.com/devennn/rungus-language-corpus).

Basic overview as of 23/1/2020:
- Total Rungus word = 1764
- Total english translation word = 1646
- Frequency of words:
  - [Rungus frequency](https://github.com/devennn/rungus-language-corpus/blob/master/rgs_overview.csv)
  - [English translation frequency](https://github.com/devennn/rungus-language-corpus/blob/master/rgs_eng_translated_overview.csv)
  
## Analysis 1: Some rungus words translate into English phrases

| Rungus     | English                |
|------------|------------------------|
| hambin     | baby slink             |
| riwayat    | background story       |
| binurangus | bamboo water container |

There are no exact words to translate some of the Rungus words. Rather than translating directly, some dictionary explain the meaning using phrase. This method will increase word counts of English words and the sentences overall as shown below. 

"""
Rungus: Ihibai poka do vaig iti sangit, inumon ku.
English: Please pour some water in this bamboo container, I want to drink.
"""

It could be a problem if the ratio of input and target sequence(sentence) differ too much as it will affect the ability of a model to process long words. 

## Analysis 2: Different words order after sentence translation

| Rungus     | English                |
|------------|------------------------|
| Ogumu ma dahava ong ikau o barasan to | You argue so much when people advised you |
| Dumarun dati iti tu akapal ino mituvong | The cloud is dark, it will be raining |

- Observation
  - Example 1: Translation is not one to one.
  - Example 2: Backward translation
  
Just by rough judgement to the sequences above, I can decide that Encoder-Decoder model might not train well with it. This will affect the generelization ability of a model as it will tends to remember the sequence as a whole rather than "understand" how every words maps to the target. As a result, model will perform well(translate well) if the input sequence exists in the training set. Other input will be consider noise as it will output empty translate a.k.a "Is this even a word?"

## Analysis 3: Rungus prefix and lingos

| Before  | After       |
|---------|-------------|
| Tuvong  | Mituvong    |
| gaji    | mangagaji   |
| patazan | kinapatazan |
| matai   | minatai     |
| Labus   | nakalabus   |

And there is no direct translation of the prefix when used in a sentence such as:
```The buffalo's baby escaped == Nakalabus it uzog do karabau.```
The example above will cause the algorithm to treat it as different words.

As such, having proper language preprocessing toolkit will help. The toolkit should work like NLTK and handle prefixex and common words. Not only that, language lingo will be a huge problem to the translation. They shouldn't be removed or it will bring different meaning to rungus phrase. Some of know language lingos(up to the time of writing) are:

"""
- 'do'
- 'ong'
- 'no'
- 'ko'
- 'po'
- 'dino'
- 'ino'
- 'o'
"""
