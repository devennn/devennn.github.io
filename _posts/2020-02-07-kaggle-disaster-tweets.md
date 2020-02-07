---
title: Predicting real and fake Disaster Tweets
layout: post
---

This is a kaggle competitions to predict real and fake tweets from twitter tweets. Full code can be found [here](https://github.com/devennn/Programming-Challenge/blob/master/kaggle-submission/disaster-tweets/main_v2.ipynb)

I'm still exploring different approach to the problem. Hence, the code will not be 100% same as to what will be presented here.

## Exploring dataset
```
                                               text  target  
0   Our Deeds are the Reason of this #earthquake M...       1  
3   13,000 people receive #wildfires evacuation or...       1  
4   Just got sent this photo from Ruby #Alaska as ...       1  
5   #RockyFire Update => California Hwy. 20 closed...       1    
11  Haha South Tampa is getting flooded hah- WAIT ...       1  
12  #raining #flooding #Florida #TampaBay #Tampa 1...       1  
13            #Flood in Bago Myanmar #We arrived Bago       1   
19                       What a goooooooaaaaaal!!!!!!       0  
20                             this is ridiculous....       0  
21                                  London is cool ;)       0    
38  Barbados #Bridgetown JAMAICA ÛÒ Two cars set ...       1  
39                             Ablaze for you Lord :D       0  
40  Check these out: http://t.co/rOI2NSmEJJ http:/...       0  
41  on the outside you're ablaze and alive\r\nbut ...       0  
43       SOOOO PUMPED FOR ABLAZE ???? @southridgelife       0   
```

As seen, there are lots of variations that can be found.
- Punctuations
- Hashtags
- username
- Unicode
- Links

These stuff must be removed to get a clean text.

## Preprocessing - Cleaning the texts!
```python
def cleaning(tweet_text, df):
    temp = []
    table = str.maketrans("", "", string.punctuation)
    for tweet in tweet_text:
        # Remove links
        tweet = re.sub(r"http\S+", "", tweet)
        # Remove newline
        tweet = tweet.strip('\n')
        # Remove unicode
        tweet = normalize('NFKD', tweet).encode('ascii','ignore')
        # Remove username
        tweet = re.sub('@[^\s]+','',str(tweet))
        # Remove punctuation and change to lower case
        tweet = tweet.translate(table).lower()
        # Remove 'b' at the begining for binary
        tweet = tweet.replace('b', '', 1)
        # Remove whitespace at start of sentence
        tweet = tweet.strip()
        temp.append(tweet)
    try:
        # Concatenate training with target
        processed_tweets = pd.concat([pd.DataFrame(temp), df['target']], axis=1)
        processed_tweets = pd.DataFrame(processed_tweets)
    except KeyError:
        processed_tweets = pd.DataFrame(temp)
    print(processed_tweets)
    return processed_tweets

# Preprocess training and testing tweets
processed_tr_tweets = cleaning(train_df['text'], train_df)
processed_tst_tweets = cleaning(test_df['text'], test_df)
```

test_df is the new data to be predicted. Because test_df does not have target column, they will be processed when KeyError triggerd. 

Pretty much what I have done is removing all things that can't be understood by human.

I couldn't understand why the texts are having binary symbol eventhough they are string type right from the sile. So, I remove the binary symbol (b' ') manually. Because removing them create whitespaces, I applied .strip() to remove them.

## Tokenization

## Training

## Evaluation and Prediction

