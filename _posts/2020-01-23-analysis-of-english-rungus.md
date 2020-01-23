The analysis is made based on [eng - rgs language corpus](https://github.com/devennn/rungus-language-corpus). The language may not be the official translation.

Basic overview as of 23/1/2020:
- Total Rungus word = 1764
- Total english translation word = 1646
- Frequency of words:
  - [Rungus frequency](https://github.com/devennn/rungus-language-corpus/blob/master/rgs_overview.csv)
  - [English translation frequency](https://github.com/devennn/rungus-language-corpus/blob/master/rgs_eng_translated_overview.csv)
  
## Analysis 1: Phrase translations rather than word by word

Some of the rungus words translate into English phrases. For example:

| Rungus     | English                |
|------------|------------------------|
| hambin     | baby slink             |
| riwayat    | background story       |
| binurangus | bamboo water container |

When used in a sentence, it can increase the number of translated words. Since the algorithm might map it one to one, it can cause translation error.

## Analysis 2: Different words order after translation

Sentences like these will also be a problem.

| Rungus     | English                |
|------------|------------------------|
| Ogumu ma dahava ong ikau o barasan to | You argue so much when people advised you |
| Dumarun dati iti tu akapal ino mituvong | The cloud is dark, it will be raining |
| Ihibai poka do vaig iti sangit, inumon ku. | Please pour some water in this bamboo container, I want to drink. |

- Observation
  - Example 1: Translation is not one to one.
  - Example 2: Backward translation
  - Example 3: Sentences have different length. 8 rungus words translated to 12 English words.

## Analysis 3: Prefix

Rungus has their own prefix such as:
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

## What's the plan?

I find it useful to translate based on typical phrases rather than word by word. For example:
```
English: My calf bitten by a dog.
Rungus: Nokokot dot asu iti votis ku.
```
_Nokokot_ is not _My_. Same as _votis ku_ is not _a dog_.

My proposed solution will be that phrase _votis ku_ can be translated as _my calf_, and _Nokokot dot asu_ is translated as _bitten by a dog_. Having two phrases may give better results rather than one to one translation.
