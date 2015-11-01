date: '2014-08-31'
layout: post
title: Festival Chatter (Part 1) - Collecting Bonnaroo Tweets from the Streaming Twitter API

It seems like summer music festivals get more and more popular every year. I guess this could be the subject of its own post, but let's stick with my personal anecdotal evidence for the time being. I remember only a handful of music festivals in the U.S. when I was in high school - Bonnaroo, All Good, 10,000 Lakes, and Coachella. I am sure that there were others, but it was nowhere near as ubiquitous as present day. Even so, [Bonnaroo](http://www.bonnaroo.com/) remains one of the most popular festivals east of the Mississippi.

I wonder if social media is partly responsible for the increased interest in music festivals? Who doesn't get jealous when they see somebody post a picture of a sunny day with thousands of other people that aren't you watching some big name artist? And since there are rarely pictures of mud-soaked tents or decrepit porta-potties, maybe people today are less likely to consider the discomforts of camping out than they would have in the past?

Unfortunately, I think that it is pretty hard to quantify this particular influence of social media. Let us work off of the assumption that there is *some* influence from social media, though. If so, then it would be beneficial for artists to quantify their own popularity as well as their primary audience demographics. This seems like a safe line of thought given that there are entire [companies](https://www.nextbigsound.com/) devoted to tracking artists' social media presence. But without a full company at my disposal, what can I learn just using my personal computer?

**To answer that question, I decided to track all tweets related to Bonnaroo during the entire 4 days of the festival.**

But enough ranting - let's get some data.

## The Twitter Streaming API

Twitter is one of the easiest services to track social media presence. I proclaim this as fact because it was quite simple for me to do it, and, if you continue reading, then you will be able to track tweets, too. And thanks to the beauty of the internet, where everybody seems to provide information for free, I was able to use this [post](http://www.danielforsyth.me/analyzing-a-nhl-playoff-game-with-twitter/) from Daniel Forsyth to practically verbatim copy code and set up my own Twitter tracker. I will describe some of the process, but you should really just read his post.

#### Get Your Twitter App

Before you can begin collecting tweets on your computer, you must first setup an account on the [Twitter developer site](https://dev.twitter.com). After creating an account, click your icon in the upper right corner and go to "My applications". You must now create an "App" which will allows your computer to connect to the Twitter API.

You have to give a title, description, and website for this app. You can honestly list whatever you want here, so do not worry if you do not have a website. After creating the app, click the API Keys tab. Scroll to the bottom and click "Create my access token". It takes about 30 seconds for the tokens to be created, so wait a bit and then refresh the page.

You should now have an "Access token" and "Access token secret" listed at the bottom of the page. Combined with the "API key" and "API secret" at the top of the page, you have everything you need to start tracking tweets. By the way, these codes are all part of the [OAuth](http://en.wikipedia.org/wiki/OAuth) system that I know absolutely nothing about but somehow allows you to securely connect to the Twitter API.

#### Tweepy and Pymongo - Because Python Always

Data scientists love Python (which is again anecdotal - somebody should aggregate job postings). Coming from MATLAB, I have found Python to be about as low of a barrier to entry as I could hope for. In the interest of keeping the language consistent throughout the data pipeline, [Tweepy](http://www.tweepy.org/) is a simple Python library for interacting with the Twitter API. Below is the code that I used to track all tweets that mention "Bonnaroo" (not case sensitive) or any mispelling of Bonnaroo that I could think of. I consistently mispell it, so it seems likely that others would, too. By the way, this code is *completely* stolen from that [post](http://www.danielforsyth.me/analyzing-a-nhl-playoff-game-with-twitter/) that I mentioned earlier, so I claim absolutely zero credit.

```python

# This code was basically totally stolen from
# http://www.danielforsyth.me/analyzing-a-nhl-playoff-game-with-twitter/

import tweepy
import sys
import pymongo

######### The below authentication has been omitted
consumer_key=""
consumer_secret=""

access_token=""
access_token_secret=""
#########

auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth)

class CustomStreamListener(tweepy.StreamListener):
    def __init__(self, api):
        self.api = api
        super(tweepy.StreamListener, self).__init__()

        self.db = pymongo.MongoClient().bonnaroo

    def on_status(self, status):
        status.text=str(unicode(status.text).encode("utf-8"))
        print status.text , "\n"

        data ={}
        data['text'] = status.text
        data['created_at'] = status.created_at
        data['geo'] = status.geo
        data['source'] = status.source

        self.db.Tweets.insert(data)


    def on_error(self, status_code):
        print >> sys.stderr, 'Encountered error with status code:', status_code
        return True # Don't kill the stream

    def on_timeout(self):
        print >> sys.stderr, 'Timeout...'
        return True # Don't kill the stream

sapi = tweepy.streaming.Stream(auth, CustomStreamListener(api))
sapi.filter(track=['bonnaroo','bonaroo','bonarroo','bonnarroo']) # Get all mispellings
```
Remember those API keys that you got for your Twitter app? You should set the `consumer_key` and `consumer_secret` variables equal to the "API key" and "API secret" codes, respectively. Likewise, the "Access token" and "Access token secret" codes should be mapped to the `access_token` and `access_token_secret` variables. Make sure to omit your tokens if you ever share your code (as I've done above).

The above code creates an object that records any tweet that contains any of the strings that I have listed in the bottom line. Four properties of the tweet are recorded: the text, the date and time, the GPS location (if given), and the source (Twitter client) of the tweet. These four properties are collected in a dictionary variable called `data` and inserted into a MongoDB database.

Why MongoDB? Because NoSQL is the new SQL? No, because MongoDB is what the code that I copied used. The code uses [PyMongo](http://api.mongodb.org/python/current/), a Python library for interacting with MongoDB. For this application, I do not think that it really matters what type of database you use - you could just as well use MySQL or something.

Depending on your filter terms that you want to track, you may end up collecting a *lot* of tweets. In that scenario, you may want to set a limit to the total number of tweets that you collect. To do this, initialize a variable

```python
self.tweetCount = 0
```

in the `__init__` function. Then, place the following code in the `on_status` function:

```python
cap = 10000 # Whatever number you want
self.tweetCount+=1
if self.tweetCount < cap:
    return True
else:
    return False
```

Just do not forget to remove this code when you do not want a cap! That is why I am writing this post about Bonnaroo instead of the Governor's Ball music festival (for which I have exactly 10,000 tweets if anybody wants them).

So, if you can install Python, MongoDB, and the Tweepy and PyMongo libraries, then you can track tweets. Bonnaroo ran from June 12th to June 15th this year. I started collecting tweets on the afternoon of the 11th because I was hoping to hear some chatter from people traveling to Tennessee. I stopped the tweet collection during the afternoon of the 16th in order to collect the backend of the chatter. And somehow, the code just ran, I never got disconnected, and I ended up with **157,600** tweets. In the next post, I will finally write about the data that was collected.
