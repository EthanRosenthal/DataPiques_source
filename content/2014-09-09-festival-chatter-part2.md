date: '2014-09-09'
layout: post
title: Festival Chatter (Part 2) - Evaluating Band Popularity from Bonnaroo Tweets

In my previous [post]({filename}/2014-08-31-festival-chatter-part1.md), I wrote about how I collected tweets about the Bonnaroo Music and Arts Festival during the entirety of the festival. There are a wide range of questions that could be answered by this dataset, like

+ Do people spell worse as they become more intoxicated throughout the night?
+ Does text [sentiment](http://en.wikipedia.org/wiki/Sentiment_analysis) decline as people go more days without bathing?
+ Who in the world tweets from a laptop during a music festival?

I would honestly love to answer the above questions (and plan to), but I will focus on the most obvious question for this post:

**Which band was most [popular](http://youtu.be/RNc45FTenhg)?**

And while this question seems simple to answer, there are many reasons this blog post is so long. To start, we do not even have a decent definition of the question!

What does it mean for an artist to be the most popular as measured by tweets? For now, let's work off of the oldest rule of PR: "Any publicity is good publicity". In that case, we shall rank band popularity simply by the number of tweets that mention each artist. Let's try to do this and see what happens.

## Of Pythons and Pandas

From the previous post, I have my dataset of Bonnaroo tweets sitting in a MongoDB database. I would like to get these tweets out of the database and into [IPython](http://ipython.org/), a software package for interactive computing in Python. I exported the database as a giant JSON file and then loaded it into IPython with a list comprehension.

The next step is to get the JSON record into [pandas](http://pandas.pydata.org/), a Python library used mainly for manipulating tabular data. The main object for dealing with tabular data, the DataFrame, nicely reads in such a JSON record.

```python
import json
import pandas as pd
from pandas import DataFrame, Series

path = 'tweetCollection.json'
record = [json.loads(line) for line in open(path)]
df = DataFrame(record)
```
If I type ```df.count()``` I see that, sure enough, all 157,600 tweets are present and that 8,656 of them contain location data.

```
_id           157600
created_at    157600
geo             8656
source        157600
text          157600
dtype: int64
```
While watching the tweets stream in, I noticed that there are a lot of retweets. To me, these do not seem as "organic" as a bona-fide, original tweet. People and software can spam twitter all they want with retweets, but it is more difficult to spam with original tweets. So, I think a more robust measure of band popularity is *unique* tweets. Pandas allows us to easily investigate this:

```python
df['text'].describe()
```
```
count                                                157600
unique                                               107773
top       RT @502michael502: Islam The Religion Of Truth...
freq                                                   1938
Name: text, dtype: object
```
Of the 157,600 original tweets, only about two-thirds of them are unique tweets. And wait, what's that most popular tweet that is repeated 1,938 times?

```python
print df['text'].describe()['top']
```
```
RT @502michael502: Islam The Religion Of Truth

http://t.co/BO7Sjw6pSl

#FathersDay #AFLDonsDees  #Bonnaroo #Brasil2014 #WorldCup #Jewish #…
```
Wow. Sure enough, if you go to the twitter page for [@502michael502](https://twitter.com/502michael502), you will see that random pro-Islam messages are tweeted and retweeted thousands of times with an assorted collection of hashtags containing trending and religious words. I guess Bonnaroo was popular enough to make it onto @502michael502's trending hashtags! And here I thought he was just a big jam band fan.

Ok, now we can try to remove retweets. We start by grabbing only unique tweets.

```python
# Retain only unique text-unique tweets
uniques = df.drop_duplicates(inplace=False, cols='text')

# I don't know how to make .startswith() case insensitve,
# so check both cases:
organics = uniques[ uniques['text'].str.startswith('RT')==False ]
organics = organics[ organics['text'].str.startswith('rt')==False ]

# In case RT was placed further in the text than the beginning.
# Include spaces around ' RT ' to prevent grabbing words like start
organics = organics[ organics['text'].str.contains(' RT ', case=False)==False ]

print organics.count()
```
```
_id           93311
created_at    93311
geo            8537
source        93311
text          93311
dtype: int64
```
There we go: we have gone from 157,600 total tweets to 93,311 "organic" tweets. There is still more work that we could do to get more organic tweets. For example, I would argue that news media sources tweeting about artists at Bonnaroo are not a good measure of band popularity. Such tweets are more difficult to detect, though. One method could be to look at the source of the tweet - maybe tweets from cell phones are more likely to be individuals than media organizations? I will save this for another post because we still have a lot of work to do!

## Most BeautifulSoup in the [Room](http://youtu.be/5YIxpNPhAQE)

Now that I have grouped together all of the tweets that we care to investigate, we must search for mentions of each Bonnaroo artist. But I am lazy. There are *189* different artists performing at Bonnaroo, and by no means do I feel like typing them all out.

Enter [BeautifulSoup](http://www.crummy.com/software/BeautifulSoup/), a Python library for scraping websites. All I have to do is check out the band lineup on the Bonnaroo website, figure out which ```div``` elements correspond to the listed bands, and *BeautifulSoup* will grab the contents.

```python
import urllib2
from BeautifulSoup import BeautifulSoup

url='http://lineup.bonnaroo.com/'
ufile = urllib2.urlopen(url)
soup = BeautifulSoup(ufile)
bandList = soup.find('div',{'class':'ds-lineup ds-player'}).findAll('a')
fout = open('bonnarooBandList.txt','w')
for row in bandList:
    band=row.renderContents()
    fout.write(band + '\n')

fout.close()
```

## With a Little Help from my (API) Friends

When I wrote the above script, I thought I was done. Later on, I thought about the fact that people do not always call bands by their full name. For example, the Red Hot Chili Peppers are often abbreviated RHCP. I was amazed when I found that [MusicBrainz](https://musicbrainz.org/), an online music encyclopedia, not only keeps track of bands' aliases and mispellings, but MusicBrainz actually has an API for accessing this information. Even better, somebody created a [Python wrapper](http://python-musicbrainzngs.readthedocs.org/en/latest/) for the API.

I also had to perform some "scrubbing" of the aliases that are retrieved from the MusicBrainz API. I consider that a band is "mentioned" in a tweet if all words in any of the band's aliases are present in the tweet text. For example, a match for both "arctic" and "monkeys" in the text would be a mention of "Arctic Monkeys". However, I do not want to miss a mention of "The Flaming Lips" because "the" is not included.

I ameliorated this issue by using [nltk](http://www.nltk.org/), a Natural Language Processing library. The library contains a list of English stopwords (common words like "the") which I used as a filter. **Note**: This may be an issue for bands like "The Head and the Heart" where the filter would leave behind "head" and "heart". Both these words could easily be in a tweet and not relate to the band.

The code below shows how I used the public API's and *nltk* in order to get searchable aliases.

```python
import musicbrainzngs as mbrainz
import json
import string
import re
import nltk
from nltk.corpus import stopwords


mbrainz.auth(username,password) # Use a username and password
mbrainz.set_useragent(program_version,email_address) # Include name
                                # of your program and email address

# To be used for removing punctuation
regex = re.compile('[%s]' % re.escape(string.punctuation))

################################
def clean_aliases(alias, regex):
    """
    This function converts each alias to lowercase, removes
    punctuation, and removes stop words. The output is a
    list containing the remaining words.
    """
    alias = alias.lower().replace(' &amp;', '') # Remove ampersands
    alias = regex.sub('', alias) # Remove punctuation
    alias_words = \
    [w for w in alias.split() if w not in stopwords.words('english')]
    return alias_words
################################

with open('bonnarooBandList.txt','r') as fin:
    with open('bonnarooAliasList.json','w') as fout:
        aliasDict={} # Initialize alias dictionary
        for band in fin:
            band = band.rstrip('\n')
            # Remove ampersands
            band_query = band.lower().replace(' &amp;', '')
            # Remove punctuation
            band_query = regex.sub('', band_query)
            # Only grab first result
            result = \
                mbrainz.search_artists(artist=band_query, limit=1)
            # Remove stopwords
            band_query = clean_aliases(band_query, regex)
            #Initialize with stripped version of name
            # listed on Bonnaroo website
            aliasList = [band_query]
            try:
                for alias in result['artist-list'][0]['alias-list']:
                    alias = clean_aliases(alias['alias'], regex)
                    aliasList.append(alias) # Build alias List
            except: # Some artists do not return aliases
                pass # So don't do anything!
            aliasDict[band] = aliasList
        json.dump(aliasDict,fout)
    fout.close()
fin.close()
```
## The Final Histogram

Ok, so we now have a dictionary of *easily searchable* aliases for all artists that performed at Bonnaroo. All we have to do now is go through each tweet and see if any of the aliases for any of the artists are mentioned. We can then build a histogram of "mentions" for each artist by adding up all of the mentions in all of the tweets for a given artist.

In the code below, I do just this. By running the function at the bottom, ```get_bandPop```, we get a *pandas* Series in return that contains each artist and the number of times they were mentioned in all of the tweets.

```python
# To be used for removing punctuation
regex = re.compile('[%s]' % re.escape(string.punctuation))

def clean_sentence(sentence):
    """
    Converts each sentence to lowercase and removes
    punctuation.
    """
    sentence = sentence.lower().replace(' &amp;', '') # Remove ampersands
    sentence = regex.sub('', sentence) # Remove punctuation
    # sentence_words = [w for w in sentence.split() if w not in stopwords.words('english')]
    return sentence

def find_mention(sentence, phrase_list):
    """
    Takes a phase_list, which is a list of phrases where
    each phrase corresponds to a list of the words in the phrase, and
    checks to see whether all the words of any of the phrases are
    present in "sentence".
    """
    for words in phrase_list:
        words = set(words)
        if words.issubset(sentence):
            return True
    return False # None of the word lists were subsets

def check_each_alias(sentence, alias_dict):
    """
    Checks to see whether any of the aliases for
    each band mentioned in alias_dict are mentioned
    in "sentence".

    band_bool is a dictionary that contains all band
    names as keys and True or False as values corresponding
    to whether or not the band was mentioned in the sentence.
    """
    band_bool={}
    sentence = set(clean_sentence(sentence).split())
    for k, v in alias_dict.iteritems():
        band_bool[k] = find_mention(sentence, v)
    return pd.Series(band_bool)

def build_apply_fun(alias_dict):
    """
    Turn check_each_alias into an anonymous function.
    """
    apply_fun = lambda x : check_each_alias(x, alias_dict)
    return apply_fun

def get_bandPop(df, alias_dict):
    """
    For tweet DataFrame input "df", build histogram of of mentions
    for each band in alias_dict.
    """
    bandPop = df['text'].apply(build_apply_fun(alias_dict), alias_dict)
    bandPop = bandPop.sum(axis=0)
    bandPop.sort(ascending=False)
    return bandPop

```
And now, finally, all we have to do is type ```bandPop[:10].plot(kind='bar')``` (and maybe fiddle around in *matplotlib* for an hour adjusting properties of the figure) and we get a histogram of mentions for the top ten most popular bands at Bonnaroo:

![Top Ten Bands]({filename}/assets/img/bandPop_top10.png)

And of course it's Kanye! Is anybody surprised?

Wow, that was a lot of work for one, measly histogram! However, we now have a bunch of data analytical machinery that we can use to delve deeper into this dataset. In my [next post]({filename}/2014-10-6-festival-chatter-part3.md ), I'll do just that!
