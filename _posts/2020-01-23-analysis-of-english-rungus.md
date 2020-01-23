---
layout: post
title: Analysis of Rungus - English translation
---

This analysis are made based on [eng - rgs language corpus](https://github.com/devennn/rungus-language-corpus). The language may not be the official translation.

Basic overview as of 23/1/2020:
- Total Rungus word = 1764
- Total english translation word = 1646
- Frequency of words:
  - [Rungus frequency](https://github.com/devennn/rungus-language-corpus/blob/master/rgs_overview.csv)
  - [English translation frequency](https://github.com/devennn/rungus-language-corpus/blob/master/rgs_eng_translated_overview.csv)
  
## Analysis 1: Should consider phrase translations

Some of rungus language are translated ad english phrase. For example:

| Rungus     | English                |
|------------|------------------------|
| hambin     | baby slink             |
| riwayat    | background story       |
| binurangus | bamboo water container |

This will be a problem when used in a sentence since it is not one to one translation.

Sentences like these will also be a problem.

| Rungus     | English                | Observation |
|------------|------------------------|-------------|
| Ogumu ma dahava ong ikau o barasan to | You argue so much when people advised you | Not one to one translation |
| Dumarun dati iti tu akapal ino mituvong | The cloud is dark, it will be raining | Backward translation |
| Ihibai poka do vaig iti sangit, inumon ku. | Please pour some water in this bamboo container, I want to drink. | 'do' is not translated. 8 rungus words = 12 english words |


