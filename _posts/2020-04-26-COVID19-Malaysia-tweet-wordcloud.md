---
layout: post
title: Simple wordcloud to understand Malaysian COVID19 tweet
published: false
---
This simple project is to explore an open source Twitter dataset on COVID19. I am interested only for Malaysian tweets.

Full dataset:
- [Coronavirus (covid19) Tweets](https://www.kaggle.com/smid80/coronavirus-covid19-tweets)

The original dataset combines tweets from other countries. I have filter the tweets based on county_code = 'MY'. This gives me 4785+ lines of tweets to play with. I only take few meaningful columns which are tweet, retweet_count, favourites_count, created_at, and lang.

## Exploring raw dataset
```
text 	retweet_count 	favourites_count 	created_at 	lang
0 	When will this be over #CoronavirusOutbreak 	0 	12523 	2020-03-05T03:19:35Z 	en
1 	I miss those days when I sneeze people would p... 	1 	5107 	2020-03-05T09:54:45Z 	en
2 	Bond movie postponed cuz nobody wants to die. ... 	0 	5107 	2020-03-05T11:47:07Z 	en
3 	All these days software was scanned for virus,... 	0 	5107 	2020-03-05T13:27:55Z 	en
4 	#GempakKongsi Maklumat terkini mengenai #Covid... 	8 	870 	2020-03-05T14:46:20Z 	in
... 	... 	... 	... 	... 	...
4780 	#CoronaUpdate #untilltomorrow #malamminggu\n#C... 	0 	183 	2020-03-28T21:26:30Z 	und
4781 	China sold 432million erous&gt;3548crores in m... 	0 	183 	2020-03-28T21:43:41Z 	en
4782 	#StayAtHomeAndStaySafe\n#Covid_19\n#Malaysialo... 	0 	4 	2020-03-28T22:49:40Z 	und
4783 	Please jangan macam tu😢\nKawan2 please #stayat... 	0 	471 	2020-03-28T23:22:16Z 	in
4784 	Why we should #StayHome #MCO #Covid_19 #MedTwe... 	487 	2361 	2020-03-28T23:41:36Z 	en

4785 rows × 5 columns
```
As you can see, there are lots of garbage characters and also emoji. I found out that most of the tweet datasets have html tag included. That is probably because of how they collect the data. If not carefully cleaned, they can be noise to the dataset if they exists frequently.

## Cleaning

```python
def unicodeToAscii(s):
    return ''.join(
        c for c in unicodedata.normalize('NFD', s)
        if unicodedata.category(c) != 'Mn'
    )

def remove_stopword(words):
    texts = [word for word in words if not word in stop_words]
    return ' '.join(texts)

table = str.maketrans("", "", string.punctuation)
def clean_string(tweet):
    tweet = re.sub(r"http\S+", "", tweet)
    tweet = re.sub('@[^\s]+','',str(tweet))
    tweet = tweet.translate(table).lower()
    tweet = unicodeToAscii(tweet.lower().strip())
    tweet = re.sub(r"([.!?])", r" \1", tweet)
    tweet = re.sub(r"[^a-zA-Z.!?]+", r" ", tweet)
    tweet = remove_stopword(tweet.split())
    return tweet.strip()
```

## Plotting the wordcloud

## What can I understand?
