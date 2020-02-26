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

When used in sentences, it can increase the number of translated words and cause translation error.

## Analysis 2: Different words order after sentence translation

| Rungus     | English                |
|------------|------------------------|
| Ogumu ma dahava ong ikau o barasan to | You argue so much when people advised you |
| Dumarun dati iti tu akapal ino mituvong | The cloud is dark, it will be raining |
| Ihibai poka do vaig iti sangit, inumon ku. | Please pour some water in this bamboo container, I want to drink. |

- Observation
  - Example 1: Translation is not one to one.
  - Example 2: Backward translation
  - Example 3: Sentences have different length. 8 rungus words translated to 12 English words.

## Analysis 3: Rungus prefix

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

## My proposed solution?

I find it useful to train algorithm based on phrases rather than word by word. For example:
```
English: My calf bitten by a dog.
Rungus: Nokokot dot asu iti votis ku.
```
_Nokokot_ is not _My_ and _votis ku_ is not _a dog_.

Phrase _votis ku_ can be translated as _my calf_, and _Nokokot dot asu_ is translated as _bitten by a dog_. Having two phrases may reduce translation error.
