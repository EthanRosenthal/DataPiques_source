date: '2014-11-25'
layout: post
slug: festival-chatter-part4
title: Festival Chatter (Part 4) - Some Simple Sentiment Analysis

I think this post will probably conclude my Festival Chatter series on analyzing Bonnaroo tweets in Python ([part 1]({filename}/2014-08-31-festival-chatter-part1.md), [part 2]({filename}/2014-09-09-festival-chatter-part2.md), [part 3]({filename}/2014-10-6-festival-chatter-part3.md)). I've had a lot of fun messing around with this dataset, but I think it's time to move on to playing with something else. For this last post, though, I will show some simple [sentiment analysis](http://en.wikipedia.org/wiki/Sentiment_analysis) of the collected tweets. There are a whole bunch of issues with this method of sentiment analysis. I'll mention some of these after presenting the findings.

## Words to Numbers

The sentiment analysis used here consists of associating a number with an English word where the value of the number corresponds to how "positive" or "negative" the word seems. Presumably, I could go through the dictionary and subjectively assign numbers to words, but thankfully somebody else has less of a life than me (maybe). So, if you click [here](http://www2.imm.dtu.dk/pubdb/views/publication_details.php?id=6010), you can download a tab-separated text file called ```AFINN-111.txt``` that contains a dictionary of 2477 words and associated integer sentiment score. Larger positive (negative) values correspond to more positive (negative) sentiment. On a total side note, I often see versions of my previous sentence in scientific papers where one attempts to describe two often opposing concepts and puts the antonyms in parentheses in order to save space. I wonder if there is a name for this "literary device"? Anyway, good words like "love" get a value of +3 on the sentiment score, while hate is worth -3.

I wrote a small script ```calculate_sentiment.py``` that reads in ```AFINN-111.txt``` and calculates the sentiment score of every tweet. This score is just the sum of the sentiment scores for all words in the tweet that are also in the dictionary. I originally wrote this script for a homework assignment from the Coursera [Introduction to Data Science](https://www.coursera.org/course/datasci) course. Things like separating the scores into "positive" and "negative" buckets are unnecessary now and just left over from the assignment.

```python
"""
Calculate tweet sentiment from tweet DataFrame
"""

import pandas as pd
from pandas import DataFrame, Series


def get_sentiment_dict():
    """ Load in sentiment file AFINN-111 as dictionary """

    sent_file = open('AFINN-111.txt')
    sentiment_dict = {}
    for line in sent_file:
      term, score  = line.split("\t")
      sentiment_dict[term] = int(score)

    return sentiment_dict

def get_tweet_sentiment(tweet_df):
    """
    Calculate sentiment score for every tweet in DataFrame tweet_df
    """
    sentiment_dict = get_sentiment_dict()

    apply_fun  =  lambda x: sentiment_count(x, sentiment_dict)
    tweet_sents = tweet_df['tokens'].apply(apply_fun, sentiment_dict)

    return pd.Series(tweet_sents, name='text_sentiment')


def sentiment_count(tokens, sentiment_dict):
    """
    Calculate sentiment score for list of "tokens".
    """
    # Initialize
    sent_score = 0.
    word_count = 0.
    sent_buck = {}
    sent_buck['positive'] = 0.
    sent_buck['negative'] = 0.

    for word in tokens:
        if sentiment_dict.has_key(word):
            if sentiment_dict[word]>0:
                sent_buck['positive'] += float(sentiment_dict[word])
            elif sentiment_dict[word]<0:
                sent_buck['negative'] += float(sentiment_dict[word])
        word_count += 1.

    if word_count == 0:
        sent_score = 0
    else:
        sent_score = (sent_buck['positive']+sent_buck['negative'])

    return sent_score
```

## "But how did it make you feel?"

With this machinery, we can now take a look at the Bonnaroo dataset.

```python
import calculate_sentiment

sents = calculate_sentiment.get_tweet_sentiment(bandPop)

print '------------------------'
print '| Tweet sentiment head |'
print '------------------------'
print sents.head()
```
```
------------------------
| Tweet sentiment head |
------------------------
created_at
2014-06-11 09:24:57-05:00    3
2014-06-11 09:24:57-05:00    2
2014-06-11 09:25:01-05:00    0
2014-06-11 09:25:05-05:00    0
2014-06-11 09:25:06-05:00    0
Name: text_sentiment, dtype: float64
```
We now have a single ```text_sentiment``` number associated with each tweet.

```python
print '\n-------------------------------'
print '| Tweet sentiment description |'
print '-------------------------------'
print sents.describe()
```
```
-------------------------------
| Tweet sentiment description |
-------------------------------
count    66424.000000
mean         0.422844
std          2.344547
min        -20.000000
25%          0.000000
50%          0.000000
75%          1.000000
max         24.000000
Name: text_sentiment, dtype: float64
```
We can see that there is a positive average sentiment, but the range is quite large (-20, +24). Let's take a look at those minimum and maximum tweets:

```python
bandPop_sents = pd.concat([bandPop, sents], axis=1)
print '\n-----------------------'
print '| Most negative tweet |'
print '-----------------------'
print bandPop_sents[bandPop_sents['text_sentiment']==bandPop_sents['text_sentiment'].min()]['text'].values[0]
print '\n-----------------------'
print '| Most positive tweet |'
print '-----------------------'
print bandPop_sents[bandPop_sents['text_sentiment']==bandPop_sents['text_sentiment'].max()]['text'].values[0]
```
```
-----------------------
| Most negative tweet |
-----------------------
Mother fuckers quit sending me your bonnaroo shit. I couldn't give a fuck about some shit ass music festival

-----------------------
| Most positive tweet |
-----------------------
I love strangers. I love dance parties. I  love music. I love my friends. I love pizza. I love camping. I love high fives. I love bonnaroo.
```
As you can see, the most negative tweet is no surprise. Although our sentiment scoring algorithm captured this tweet's sentiment fairly well, you will see in a bit how the algorithm often fails because "inflamatory language" is *always* scored so negatively. Meanwhile, the frequent use of the word "love" in the positive tweet makes it clear why this tweet ranked so high on the sentiment score.

## Temporal Vibes

Now that we have our sentiment score for each tweet, I thought it would be interesting to look at how average sentiment changed with time. For this, I employ a similar approach to the last post by resampling the data. I also resample at different rates which reveals the tradeoff between temporal resolution and noise. Lastly, I've switched from using prettyplotlib to [seaborn](http://stanford.edu/~mwaskom/software/seaborn/). Prettyplotlib was abandoned by the creator, and seaborn seems to be pretty popular and easy to use.

```python
import seaborn as sns
sns.set()

# Time series tweet sentiment

pal = sns.dark_palette("skyblue", 3, reverse=True)

# Different resampling rates
x5 = bandPop_sents['text_sentiment'].resample('5t',how='mean')
x20 = bandPop_sents['text_sentiment'].resample('20t',how='mean')
x60 = bandPop_sents['text_sentiment'].resample('60t',how='mean')

x5.plot(color=pal[0],lw=3,alpha=.5)
x20.plot(color=pal[1],lw=3,alpha=.75)
x60.plot(color=pal[2],lw=1.5)

fig = plt.gcf()
ax = plt.gca()

# Labels
plt.xlabel('Date',fontsize=20)
plt.ylabel('Text Sentiment Score', fontsize=20)
plt.title('Average Tweet Sentiment', fontsize=20)
# Legend
leg = plt.legend(['5 min', '20 min', '60 min'], fontsize=12, title='Resampling Rate')
plt.setp(leg.get_title(),fontsize='15')
# Axes
plt.setp(ax.get_xticklabels(), fontsize=14, family='sans-serif')
plt.setp(ax.get_yticklabels(), fontsize=18, family='sans-serif')
plt.tight_layout()
```
![Average Sentiment]({filename}/assets/img/avg_tweet_sent.png)

For the high frequency resampling rate (5 min), you can see that the signal gets quite noisey near the morning of June 13th. It turns out that there were very few tweets during this time, so any single tweet with a sizeable sentiment score would dominate the 5 minute bin over which I was averaging.

By decreasing the sampling rate to 60 minute bins, you can see a clear upturn in the average sentiment starting around midnight on June 15th which was the last night of the festival. I decided to look at some of the very positive tweets during this time.

```python
festival_end = bandPop_sents['2014-6-16':]
for tweet in festival_end[festival_end['text_sentiment']>10]['text'][:6]:
    print tweet + '\n'
```
```
If you're a stoner an you love good food and good music you need to go to bonnaroo period lol

I am so happy I came to Bonnaroo.  Had so much fun and saw so many amazing bands.

High Fives and Free Hugs, 90,000 awesome people, over 125+ amazing performances, 5 stages/tents... #Bonnaroo2014 #Bonnaroo

@KKvspr @Bonnaroo lol it was awesome, so trippy! Lol xo hope ur well! ;-) n that band awesome as well.

Soooooo many people. Wow I love my job. I had an amazing time working Bonnaroo.  Music is so powerful,… http://t.co/QTxJinI8xs

made it home, had the best shower ever. what amazing bonnaroo (as usual)! met lovely people, got lots of free alcohol, and (duh) the music!
```
This is pretty cool - it seems like many of these tweets are people looking fondly back on their time at Bonnaroo and talking about how much they enjoyed it. It would be interesting to look at how long this "after-glow" lasts.

## Festival Chatter Scatter

Lastly, to get back to some insight into the artists, I decided to create a scatterplot of the average sentiment for each of the top 40 most popular artists on the x-axis, and the sentiment standard deviation on the y-axis. The idea was that maybe the standard deviation would indicate "controversial" artists.

```python
top_40 = band_hist.index.tolist()[:40]
sent_stats = {}
for band in top_40:
    mn =  bandPop_sents[bandPop_sents[band]==True]['text_sentiment'].mean()
    sd = bandPop_sents[bandPop_sents[band]==True]['text_sentiment'].std()
    sent_stats[band] = [mn, sd]

fig, ax = plt.subplots()

for k, v in sent_stats.iteritems():
    plt.scatter(v[0],v[1],s=200,alpha=0.5)
    plt.annotate(k,(v[0],v[1]),xytext=(5,5),textcoords='offset points')

# Axes
plt.axis([-1.2, 1.7, 1.5, 3.5])
plt.setp(ax.get_xticklabels(),fontsize=18,family='sans-serif')
plt.setp(ax.get_yticklabels(),fontsize=18,family='sans-serif')
# Labels
plt.ylabel('Sentiment Standard Deviation', fontsize=25)
plt.xlabel('Sentiment Mean', fontsize=25)
plt.tight_layout()
````
![Scatter Sentiment]({filename}/assets/img/sent_scatter.png)

I wasn't quite sure what to make of this chart. You can see that Broken Bells is far positive in terms of mean sentiment and low on the controversy axis, which is probably not surprising. I was interested in the far negative outlier, A$AP Ferg. I've never listened to him, so I had no intuition. Looking at some of the more negative A$AP Ferg tweets, we again run into the limitations of sentiment analysis:

```python
for tweet in bandPop_sents[(bandPop_sents['A$AP Ferg']==True) & (bandPop_sents['text_sentiment']<-5)]['text']:
    print tweet
```
```
Holy shit the surprise guests dont stop.  A$AP Ferg just hit the stage with a cover of The Notorious B.I.G.'s "Juicy". @Bonnaroo

Zedd's on drums and A$AP Ferg is climbing shit and the @Skrillex #superjam is officially the craziest thing I've seen in awhile #bonnaroo

A$AP FERG KILLED IT! Shit was crazy! #bonnaroo

A$AP Ferg just pulled the most ridiculous shit of all time at Bonnaroo right now.

I think I might have lost 5lbs during the A$AP Ferg show today at Bonnaroo. Just constantly moshing around. Fucking crazy.
```
People clearly loved this show, but their language is not well suited to be captured by ```AFINN-111```. And while I'm sure that there are many ways to attempt to alleviate this issue, I'll worry abou that another day. For now, all the scripts used in this series are on my [GitHub](https://github.com/EthanRosenthal/festival-chatter).
