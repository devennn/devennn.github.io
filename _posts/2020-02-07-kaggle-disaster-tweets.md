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

Since these stuff (except punctuations) are not useful to humans, they must be removed.

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

test_df is the new data to be predicted. Because test_df does not have target column, it will be processed when KeyError triggerd. Pretty much what I have done is removing all things that can't be understood by human.

I couldn't understand why the texts are having binary symbol eventhough they are string type right from the sile. So, I remove the binary symbol (b' ') manually. Because removing them create whitespaces, I applied .strip() to remove them.

At first I tried to remove all hashtags, leaving only the tweet. But, I found out that the hashtags affect the sentiment of a tweet. Thus, I decided to remove the '#' and leave the word.

## Tokenization

It is time to break them up. Tokenization is a must do step. This is a process whereby all sentences are split into something meaningful that can be used for later process. The result of tokenization are based on the tokenizing algorithm used.

I am using CountVectorizer. Because the return value is a ```scipy.sparse.csr.csr_matrix``` , it needs to be converted to array.

```
def vectorize_tweets(count_vect, data):
    vect_tweets = count_vect.fit_transform(data)
    vect_tweets = vect_tweets.toarray()
    return vect_tweets, count_vect
    
count_vect = CountVectorizer(analyzer='word', lowercase=False, stop_words='english')
combined_tr_tst = pd.concat([processed_tr_tweets[0], processed_tst_tweets[0]], axis=0)
combined_vect, count_vect = vectorize_tweets(count_vect, combined_tr_tst)
print('length of Vocabulary: %d' %len(count_vect.vocabulary_))

vect_tweets = combined_vect[:len_tr]
vect_tst_tweets = combined_vect[len_tr:]
```

The output of this function is a collection of arrays that represents every sentence. Every array shows how many times every words in a sentence exists compared to the whole vocabulary of the corpus. The return value  ```combined_vect``` is then split back to training (vect_tweets) and testing (vect_tst_tweets _a.k.a_ new data to predict)

Output:

```
length of Vocabulary: 19847
```

The corpus consists of lots of words. This is one of the reason to perform cleaning. Any 'noise' will add the vocabulary and eventually affect the training result. I also remove english stopwords as they usually exist in every sentences without really giving information to the sentence as a whole.

Final tokenization results. It is very hard to view the values as every array is 19847 long. Imagine tokenizing 'hey you' against the corpuse vocab.

```
[[0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 ...
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]]
```

## Training, Evaluation and Prediction

Up to now, I only have a combined data. I used the built in test_train_split function froms sklearn to divide the data
```
# Split training and testing
X_train, X_test, y_train, y_test  = train_test_split(
        vect_tweets, 
        processed_tr_tweets['target'],
        train_size=0.80, 
        random_state=True,
        shuffle=True
)
```
I prefer my data to be 80% train and 20% evaluation. I also like the data to be shuffled even though they looked shuffled. Just in case.

For the classifier model. I'm using logistic regression. 
```
# Train
model_rg = LogisticRegression(solver='liblinear')
model_rg = model_rg.fit(X=X_train, y=y_train)

# Evaluate model
y_pred = model_rg.predict(X_test)
print(accuracy_score(y_test, y_pred))
```

Then, the output is 
```
0.8030203545633617
```

With the trained model, it is time to perform prediciton of new data
```
# Predict
new_prediction = model.predict(vect_tst_tweets)
new_prediction = pd.DataFrame(new_prediction)
new_prediction = pd.concat([test_df['id'], new_prediction], axis=1)
print(new_prediction)
```

Output:
```
id  target
0         0       1
1         2       1
2         3       1
3         9       1
4        11       1
...     ...     ...
3258  10861       1
3259  10865       1
3260  10868       1
3261  10874       1
3262  10875       0

[3263 rows x 2 columns]
```
