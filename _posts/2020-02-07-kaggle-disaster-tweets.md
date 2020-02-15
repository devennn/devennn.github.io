---
title: Predicting real and fake Disaster Tweets
layout: post
---

This is a Kaggle competition to predict real and fake tweets from twitter tweets. You can find the full code [here](https://github.com/devennn/Programming-Challenge/blob/master/kaggle-submission/disaster-tweets/main_v2.ipynb)

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
  - Hashtags (the sign, #)
  - username
  - Unicode
  - Links
Since the variations listed (except punctuations) does not contribute to the understanding of the tweet for humans, I decide to remove them.

## Preprocessing - Cleaning the texts.
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

test_df is the new data to be predicted. Because test_df does not have a target column, it will be processed when KeyError triggered. Pretty much what I have done is removing all things that can’t be understood by humans.
The texts are having binary symbol even though they are string type right from the file. So, I remove the binary characters (b’ ‘) manually.

At first, I am thinking of removing all hashtags(# sign and the word), leaving only the tweet. But, I found out that the hashtags affect the sentiment as user use hashtag to set the topic of the tweet. If every tweet has hashtags, maybe it is a good idea only to use them for sentiment analysis data.

## Tokenization

It is time to break them up. Tokenization is a process to break sentences into something meaningful. The result of tokenization depends on the tokenizing algorithm used.

I am using CountVectorizer. The return value is a ```scipy.sparse.csr.csr_matrix``` so it needs to be converted to numpy array.

```python
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

The output of this function is a collection of arrays that represents every sentence. Every array shows how many times every word in a sentence exists compared to the whole vocabulary of the corpus. The return value combined_vect is then split back to training (vect_tweets) and testing (vect_tst_tweets a.k.a new data to predict)

Output:

```
length of Vocabulary: 19847
```

By setting stop_word to English, the algorithm will remove English stopwords. English stopwords are noises as they usually exist in every sentence without really giving information.

Final tokenization results. It is very hard to view the values as every array is 19847 long. Imagine tokenizing ‘hey you’ against the corpuse vocab.

```
[[0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 ...
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]]
```

## Training model and predict new data

Up to now, I only have a combined data.
```python
# Split training and testing
X_train, X_test, y_train, y_test  = train_test_split(
        vect_tweets, 
        processed_tr_tweets['target'],
        train_size=0.80, 
        random_state=True,
        shuffle=True
)
```

I used the built in test_train_split function froms sklearn to divide the data. I prefer the data to be 80% train and 20% evaluation and shuffled.

Now it is time to select a model. The output has two discrete values which represent two classes, True and False. Grouping texts according to the sentiment is a classification problem; thus, required a classification algorithm. I tried four algorithms and decide based on the accuracy from accuracy_score function.

```python
 def test_classifier(X_train, X_test, y_train, y_test):
        algorithm = [
            RandomForestClassifier(n_estimators=200, max_depth=3, random_state=0),
            LinearSVC(),
            LogisticRegression(solver='lbfgs'),
            KNeighborsClassifier(3)
        ]
        for i in range(len(algorithm)):
            print("=" * 40)
            print("Running : " + algorithm[i].__class__.__name__)
            model = algorithm[i]
            model.fit(X_train, y_train)
            predictions = model.predict(X_test)
            accuracy = accuracy_score(y_test, predictions)
            print("Accuracy: {:.4%}".format(accuracy))
            
test_classifier(X_train, X_test, y_train, y_test)
```

The output shows that LogisticRegression outperform the second highest by 3%. That means it predicts almost 183 more correct classes correctly compared to LinearSVC. The difference may caused by preproccessing or the algorithm itself. I would consider using LinearSVC if the difference is 1% or below. 

```
========================================
Running : RandomForestClassifier
Accuracy: 57.9120%
========================================
Running : LinearSVC
Accuracy: 77.8070%
========================================
Running : LogisticRegression
Accuracy: 80.4334%
========================================
Running : KNeighborsClassifier
Accuracy: 69.8621%
```

Rather than trying to explore both possibilities, I just went for the first place option. Using LogisticRegression.

```python
# Train
model_rg = LogisticRegression(solver='liblinear')
model_rg = model_rg.fit(X=X_train, y=y_train)

# Evaluate model
y_pred = model_rg.predict(X_test)
print(accuracy_score(y_test, y_pred))
```

The output varies a little bit from the first run, which will not effect the overall performance. Probably caused by internal computation which is not a problem .

```
0.8030203545633617
```

With the trained model, it is time to perform prediciton of new data

```python
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

## Future Work

1. I am still exploring few possibilities to fine-tune the model such as running grid search on the algorithm
2. Try different ways of tokenizing.
3. Cleaning texts
  - Short-form words
  - Numbers
  - Non-English words
