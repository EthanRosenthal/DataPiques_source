date: '2014-10-06'
layout: post
title: Festival Chatter (Part 3) - Bonnaroo Analysis in the Fourth Dimension

In this series of posts ([part 1]({filename}/2014-08-31-festival-chatter-part1.md), [part 2]({filename}/2014-09-09-festival-chatter-part2.md)), I have been showing how to use Python and other data scientist tools to analyze a collection of tweets related to the 2014 Bonnaroo Music and Arts Festival. So far, the investigation has been limited to summary data of the full dataset. The beauty of Twitter is that it occurs in realtime, so we can now peer into the fourth dimension and learn about these tweets as a function of time.

## More Organic

Before we view the Bonnaroo tweets as a time series, I would like to make a quick comment about the organic-ness of the tweets. If you recall from the previous [post]({filename}/2014-09-09-festival-chatter-part2.md), I removed duplicates and retweets from my collection in order to make the tweet database more indicative of true audience reactions. On further investigation, it seems that there were many spammy media sources still in the collection. To make the tweets even more organic, I decided to look at the source of the tweets.

Because Kanye West was the most popular artist from the previous posts' analysis, I decided to look at the top 15 sources that mentioned him:

```
twitterfeed                    1585
dlvr.it                         749
Twitter for iPhone              366
IFTTT                           256
Hootsuite                       201
Twitter for Websites            188
Twitter Web Client              127
Facebook                        120
Twitter for Android             119
WordPress.com                   102
Tumblr                           81
Instagram                        73
iOS                              42
TweetDeck                        38
TweetAdder v4                    37
```
twitterfeed and dlvr.it are social media platforms for deploying mass tweets, and a look at some of these tweets reveals this fact. So, I decided to create a list of "organic sources", which consists of mobile Twitter clients, and use these to cull the tweet collection

```python
organic_sources = ['Twitter for iPhone', 'Twitter Web Client',
                    'Facebook', 'Twitter for Android', 'Instagram']
organics = organics[organics['source'].isin(organic_sources)]
```
With this new dataset, I re-ran the band popularity histogram from the previous post, and I was surprised to see that Kanye got bumped down to third place! It looks like Kanye's popular with the media, but Jack White and Elton John were more popular with the Bonnaroo audience.

![New Top Ten Bands]({filename}/assets/img/bandPop_top10_orig_sources.png)


## 4th Dimensional [Transition](http://youtu.be/5vwV6htrjVY)

Let's now look at the time dependence of the tweets. For this, we would like to use the ```created_at``` field as our index and tell pandas to treat its elements as datetime objects.

```python
# Clean up field
organics['created_at'] = [tweetTime['$date'] for tweetTime in organics['created_at']]

organics['created_at'] = pd.to_datetime(Series(organics['created_at']))
organics = organics.set_index('created_at',drop=False)
organics.index = organics.index.tz_localize('UTC').tz_convert('EST')
```

To look at the number of tweets per hour, we have to resample our tweet collection.

```python
ts_hist = organics['created_at'].resample('60t', how='count')
```

The majority of my time spent creating this blog post consisted of fighting with *matplotlib* trying to get decent looking plots. I thought it would be cool to try to make a "[fill between](http://matplotlib.org/users/recipes.html)" plot, which took way longer to figure out than it should have. The key is that ```fill_between``` takes 3 inputs: an array for the x-axis and two y-axis arrays between which the function fills color. If one just wants to plot a regular curve and fill to the x-axis, one must create an array of zeros that is the same length as the curve. Also, I get pretty confused with which commands should be called with ax, plt, and fig. Anyway, the code and corresponding figure are below.


```python
 # Prettier pandas plot settings
 # Not sure why 'default' is not the default...
pd.options.display.mpl_style='default'
x_date = tshist.index
zero_line = np.zeros(len(x_date))

fig, ax = plt.subplots()
ax.fill_between(x_date, zero_line, ts_hist.values, facecolor='blue', alpha=0.5)
# Format plot
plt.setp(ax.get_xticklabels(),fontsize=12,family='sans-serif')
plt.setp(ax.get_yticklabels(),fontsize=12,family='sans-serif')
plt.xlabel('Date',fontsize=30)
plt.ylabel('Counts',fontsize=30)

plt.show()
```

![Tweets per Hour]({filename}/assets/img/tweet_count_ts.png)

As you can see, tweet frequency was pretty consistent during each day of the festival and persisted until the early hours of each morning.

## Band Popularity Time Series

We can now go back to questions from the previous post and look at how the top five bands' popularity changed with time. Using my program from the previous post, ```buildMentionHist```, we can add a column for each band to our existing ```organics``` dataframe. Each row of the bands' columns will contain a True or False value corresponding to whether or not the artist was mentioned in the tweet. We resample the columns like above but do this in bins of 10 minutes.

```python
import buildMentionHist as bmh
import json

path = 'bonnaroooAliasList.json'
alias_dict = [json.loads(line) for line in open(path)][0]
bandPop = organics['text'].apply(bmh.build_apply_fun(alias_dict),
                                 alias_dict)
top_five = bandPop.index.tolist()[:5] # Get top five artists' names
bandPop = pd.concat([organics, bandPop], axis=1)

top_five_ts = DataFrame()
for band in top_five:
    top_five_ts[band] = bandPop[bandPop[band] == True]['text'].resample('10min', how='count')

```

We now have a dataframe called ```top_five_ts``` that contains the time series information for the top five most popular bands at Bonnaroo. All we have to do now is plot these time series. I wanted to again make some fill between plots but with different colors for each band. I used the [prettyplotlib](http://olgabot.github.io/prettyplotlib/) library to help with this because it has nicer looking default colors. I plot both the full time series and a "zoomed-in" time series that is closer to when the artists' popularities peaked on Twitter. I ran into a lot of trouble trying to get the dates and times formatted correctly on the x-axis of the zoomed-in plot, so I have included that code below. There is probably a better way to do it, but at least this finally worked.


```python
import pytz
import prettyplotlib as ppl
from prettyplotlib import brewer2mpl

for band in top_five_ts:
    ppl.fill_between(top_five_ts.index.tolist(),0.,top_five_ts[band])

ax = plt.gca()
fig = plt.gcf()
set2 = brewer2mpl.get_map('Set2','qualitative',8).mpl_colors

# Note: have to make legend by hand for fill_between plots.
# BEGIN making legend
for color in set2:
    legendProxies.append(plt.Rectangle((0, 0), 1, 1, fc=color))

leg = legend(legendProxies, topfive, loc=2)
leg.draw_frame(False)
# END making legend


# BEGIN formatting xaxis
datemin = datetime(2014,6,13,12,0,0)
datemax = datetime(2014,6,16,12,0,0)
est = pytz.timezone('EST')
plt.axis([est.localize(datemin), est.localize(datemax),  0, 80])
fmt = dates.DateFormatter('%m/%d %H:%M',tz=est)
ax.xaxis.set_major_formatter(fmt)
ax.xaxis.set_tick_params(direction='out')
# END formatting xaxis

plt.xlabel('Date',fontsize=30)
plt.ylabel('Counts',fontsize=30)
```
Here is the full time series:

![Top Five TS Popularity]({filename}/assets/img/topfive_tweet_ts.png)

And here is the zoomed-in time series:

![Top Five TS Popularity Zoom]({filename}/assets/img/topfive_tweet_ts_zoom.png)

If we look at when each band went on stage, we can see that each bands' popularity spiked while they were performing. This is good - it looks like we are measuring truly "organic" interest on Twitter!

__Band__        | __Performance Time__
----------------|---------------------
Jack White      | 6/14 10:45PM - 12:15AM
Elton John      | 6/15 9:30PM - 11:30PM
Kanye West      | 6/13 10:00PM - 12:00AM
Skrillex        | 6/14 1:30AM - 3:30AM
Vampire Weekend | 6/13 7:30PM - 8:45PM

## Delving into the Text

Up until now, I have not looked too much about the actual text of the tweets other than to find a mention of an artist. Using the *nltk* library, we can learn a little more about some general qualities of the text. The simplest quantity is looking at the most frequently used words. To do this, I go through every tweet and break all of the words up into individual elements of a list. In the language of natural language processing, we are "tokenizing" the text. Common english stopwords are omitted, as well as any mentions of the artists or artists' aliases. I use a regular expression code to only grab words from the sentences and ignore punctuation (except for apostrophes). I also take our ```alias_dict``` from the previous post and make sure that those words are not collected when tokenizing the tweets.

```python
from nltk.tokenize import RegexpTokenizer
from nltk.corpus import stopwords
import re

def custom_tokenize(text, custom_words=None, clean_custom_words=False):
    """
    This routine takes an input "text" and strips punctuation
    (except apostrophes), converts each words to lowercase,
    removes standard english stopwords, removes a set of
    custom_words (optional), and returns a list of all of the
    leftover words.

    INPUTS:
    text = text string that one wants to tokenize
    custom_words = custom list or dictionary of words to omit
                    from the tokenization.
    clean_custom_world = Flag as True if you want to clean
                            these words.
                         Flag as False if mapping this function
                            to many keys. In that case,
                            pre-clean the words before running
                            this function.
    OUTPUTS:
    words = This is a list of the tokenized version of each word
            that was in "text"
    """
    tokenizer = RegexpTokenizer(r"[\w']+")
    stop_url = re.compile(r'http[^\\s]+')
    stops = stopwords.words('english')

    if clean_custom_words:
        custom_words = tokenize_custom_words(custom_words)

    words = [w.lower() for w in text.split() if not re.match(stop_url, w)]
    words = tokenizer.tokenize(' '.join(words))
    words = [w for w in words if w not in stops and w not in custom_words]

    return words

def tokenize_custom_words(custom_words):
    tokenizer = RegexpTokenizer(r"[\w']+")
    custom_tokens = []
    stops = stopwords.words('english')

    if type(custom_words) is dict: # Useful for alias_dict
        for k, v in custom_words.iteritems():
            k_tokens = [w.lower() for w in k.split() if w.lower() not in stops]
            # Remove all punctuation
            k_tokens = tokenizer.tokenize(' '.join(k_tokens))
            # Remove apostrophes
            k_tokens = [w.replace("'","") for w in k_tokens]
            # Below takes care of nested lists, then tokenizes
            v_tokens = [word for listwords in v for word in listwords]
            v_tokens = tokenizer.tokenize(' '.join(v_tokens))
            # Remove apostrophes
            v_tokens = [w.replace("'","") for w in v_tokens]
            custom_tokens.extend(k_tokens)
            custom_tokens.extend(v_tokens)
    elif type(custom_words) is list:
        custom_tokens = [tokenizer.tokenize(words) for words in custom_words]
        custom_tokens = [words.replace("'","") for words in custom_tokens]

    custom_tokens = set(custom_tokens)
    return custom_tokens

```

Using the above code, I can apply the ```custom_tokenize``` function to each row of my ```organics``` dataframe. Before doing this, though, I make sure to run the ```tokenize_custom_words``` function on the alias dictionary. Otherwise, I would end up cleaning the aliases for every row in the dataframe which is a waste of time.

```python
import custom_tokenize as tk

clean_aliases = tk.tokenize_custom_words(alias_dict)
token_df = organics['text'].apply(tk.custom_tokenize,
                                  custom_words=clean_aliases,
                                  clean_custom_words=False)
```

Lastly, I collect all of the tokens into one giant list and use the ```FreqDist``` *nltk* function to get the word frequency distribution.

```python
# Need to flatten all tokens into one big list:
big_tokens = [y for x in token_df.values for y in x]
distr = nltk.FreqDist(big_tokens)
distr = distr.pop('bonnaroo') # Obviously highest frequency
distr.plot(25)
```

![Word Frequency Distribution]({filename}/assets/img/token_freq.png)

A couple things caught my eye - the first being that people like to talk about themseleves (see popularity of " i'm "). Also it was pretty popular to misspell "Bonnaroo" (see popularity of " bonaroo "). I wanted to see if there was any correlation between mispellings and maybe people being intoxicated at night, but the time series behavior of the mispellings looks similar in shape (though not magnitude) to the full tweet time series that is plotted earlier in the post.

```python
misspell = token_df.apply(lambda x: 'bonaroo' in x)
misspell = misspell[misspell].resample('60t', how='count')
```

![Misspelling Time Series]({filename}/assets/img/misspell_bonaroo_ts.png)

The other thing that interested me was the the word "best" was one of the top 25 most frequent words. Assuming that "best" correlates with happiness, we can see that people got happier and happier as the festival progressed:

!["Best" Time Series]({filename}/assets/img/best_ts.png)

This is, of course, a fairly simplistic measure of text sentiment. In my next post, I would like to quantify more robust measures of Bonnaroo audience sentiment.

By the way, the code used in this whole series on Bonnaroo is available on my [GitHub](https://github.com/EthanRosenthal/festival-chatter).
