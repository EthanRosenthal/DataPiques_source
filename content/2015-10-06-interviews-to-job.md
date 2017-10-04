date: '2015-10-06'
layout: post
slug: interviews-to-job
title: Yet Another PhD to Data Science Post (Part III)

This is the final part in my series on going from PhD to Data Science (parts [I]({filename}/2015-09-23-start-to-insight.md) and [II]({filename}/2015-09-29-insight-to-interviews.md)). As previously mentioned, while I was demoing my Insight project at companies, I also spent a good bit of time studying for interviews. The technical areas of study for interviews can be largely grouped as

- Computer Science (CS)
- Machine Learning (ML)
- Statistics
- SQL

I'll first review some resources for these areas of study then talk about interviews.

## Study Materials

### Computer Science

A reasonable understanding of basic algorithms and data structures is expected. How might these be tested in an interview? Maybe you have to build a stack from scratch. Maybe you have to solve some brainteaser that turns out to just be Merge from mergesort. While I think that these sorts of tasks are a terrible indicator of one's data science abilities, I don't write the rules (yet). CS can have a fairly steep learning curve, and I think that there are likely diminishing returns as one delves into more complex subjects. It's all about studying things that have a higher probability of being in an interview.
For example, implementing a linked list is popular and does not require too much mental capital, while Dijkstra’s Algorithm would take longer to learn with less of a chance of showing up in an interview.

I found the following study materials helpful for learning CS:

#### <a href="https://developers.google.com/edu/python/?hl=en">Google Python Class</a>
Already mentioned in the previous post, but this is basically a prerequisite for anything.

#### <a href="https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=introduction%20to%20algorithms%20pdf" target="_blank"> Introduction to Algorithms</a>
This book is probably not for everyone, but I enjoyed reading through some of the chapters on searching, sorting, and basic data structures and then implementing the pseudocode in Python.

#### <a href="http://interactivepython.org/runestone/static/pythonds/index.html" target="_blank">Problem Solving with Algorithms and Data Structures</a>
I did not find it as easy to initially learn concepts from this website as the above textbook, but the clearly written examples of various data structures in Python code was helpful in cementing my understanding.

#### <a href="https://leetcode.com/">LeetCode</a>
This website was good for trying out a bunch of different CS brainteaser-type problems. It was also infuriating how hard the "Easy" problems were and all of the damn edge cases that they use when testing your code. I don't think it's worth attempting exercises past the Easy ones.

### Machine Learning

First of all, nobody expects you to be an expert in ML, and I seriously doubt somebody will have you derive backpropagation from scratch (hell, I doubt most companies have a use for neural networks, anyway). Instead, I think it is more important to be able to speak about your understanding of various ML algorithms and their tradeoffs, their tuning parameters (regularization, learning rate, ...), and ML best practices (cross validation, learning curves, ...). In terms of algorithms, I focused on linear and logistic regression, random forests, and support vector machines, but I could be totally off-base. An understanding of clustering techniques like K-means (and how to attempt to validate) is helpful for unsupervised work. And lastly, recommender systems are important: item and user-based collaborative filtering, tf-idf for building similarity matrices, and maybe matrix factorization.

I found the following study materials helpful for learning CS:

#### <a href="https://www.coursera.org/learn/machine-learning" target="_blank">Coursera Machine Learning</a>
I already mentioned this in the first post, but this cannot be reiterated enough - start here.

#### <a href="https://www.google.com/search?q=elements+of+statistical+learning&oq=elements+of+statistical+learning&aqs=chrome..69i57j69i60l3j69i61j69i60.4075j0j7&sourceid=chrome&es_sm=122&ie=UTF-8#safe=off&q=elements+of+statistical+learning+pdf" target="_blank">Elements of Statistical Learning</a>
Definitely not for everyone, but this book provides super thorough treatments on a lot of the foundations of modern ML. I chose a couple key chapters on areas that I thought were important and read the chapters while taking hand-written notes (which helps me to remember things). Definitely a dense book and great reading to help you get to sleep.

#### <a href="https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#safe=off&q=programming%20collective%20intelligence%20pdf" target="_blank">Programming Collective Intelligence</a>
Much more accessible than the above book. This book includes many real world applications of ML with actual Python code showing implementations. I found the treatment of recommender systems to be especially useful.

#### [Blog posts]({% post_url 2014-08-31-festival-chatter-part1 %})
Reading through other peoples' blog posts where they show you how to train a machine learning model can be especially helpful for closing the gap between theory and practice. It also helps make you aware of what sorts of methods are at your disposal with various software libraries. I particularly liked <a href="http://bugra.github.io/" target="_blank">Bugra Akyildiz's</a> two posts on <a href="http://bugra.github.io/work/notes/2014-11-22/an-introduction-to-supervised-learning-scikit-learn/" target="_blank">supervised</a> and <a href="http://bugra.github.io/work/notes/2014-11-16/an-introduction-to-unsupervised-learning-scikit-learn/" target="_blank">unsupervised</a> learning.

### Statistics

I have a weak handle on important statistics topics for data science interviews. I rarely got stats questions at interviews, but a cursory look through glassdoor data science interview questions shows that stats is not uncommon. Things that could likely show up in an interview: Bayesian probability brainteasers, expectation values and other statistics on various random variables and distributions (Bernoulli, Binomial, Normal, Poisson), hypothesis testing, confidence intervals, p-values, t-tests, and linear regression (kind of overlaps with ML).

I found the following materials useful for studying statistics:

#### <a href="https://www.khanacademy.org/math/probability" target="_blank">Khan Academy Probability and Statistics</a>
This was the only course I've ever watched on Khan Academy. The lectures are nice and short and to the point. These helped to get me acquainted with the concepts.

#### <a href="https://www.openintro.org/stat/textbook.php" target="_blank">OpenIntro Statistics</a>
I ended up doing some examples out of here which helped to solidify my Khan Academy learnings.

### SQL

SQL is such a strange thing to study. Compared to everything above, it is probably not as hard to understand. However, I ended up having to relearn it multiple times because I would forget everything if I was not actively writing SQL. For most people that already have a job, this is not a problem because they use SQL everyday. Sure enough, I now use SQL quite often even though I did not get any SQL questions at interviews. Go figure.

I found the following materials useful for studying SQL:

#### <a href="https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#safe=off&q=sql%20in%2010%20minutes%20pdf" target=_blank">SQL in 10 Minutes</a>
The title is a bit misleading - each *chapter* takes you 10 minutes. But still, this book is straight and to the point on the various basic concepts of SQL. Compared to the Coursera Intro to Data Science course where you sit through an intro to set theory en route to learning SQL, this book feels much more concise.

#### <a href="http://sqlzoo.net/" target="_blank">SQLZoo</a>
While it's good to read books, I find that attempting exercises reveals the gaps in my knowledge about a subject. SQLZoo was pretty great at helping me to realize that I didn't understand SQL. That is, until I went through their interactive examples. The website feels a bit dated, but the exercises were good.

#### <a href="https://www.coursera.org/course/db" target="_blank">Coursera Introduction to Databases</a>
Because I had to go back and relearn SQL so many times, I ended up getting sick of the SQLZoo questions. This Coursera class (which is in open enrollment) provided a nice set of alternative exercises. The class also has a nice web interface for interactively evaluating the exercises just like at SQLZoo.

## Interviews

There tends to be three stages to data scientist interviews - a generic phone screen, a technical off-site screen, and then an in-person interview. I go through my take on these below but you should also check out this excellent blog <a href="http://treycausey.com/data_science_interviews.html" target="_blank">post</a> which provides a more thorough explication.

### Generic phone screen
I was able to skip some of these because the project demos at Insight essentially served the same purpose. The phone screen often consists of chatting with an HR person about your resume. I think the point is to make sure that you can hold a conversation and speak about complicated topics on your resume to a non-expert. This may seem easy, but you should practice this! I was also routinely asked why I want to do data science. The urge here is to complain about academia, but the key is to be positive about whatever you say. You should talk about why you're excited about data science and not about why you want to leave academia.

### Technical screen
After the initial phone screen, there is usually a technical screen. This can come in many forms. Some places you may get a call and have to connect to a shared document where you will write code that the person on the other end of the line is able to see. Make sure you talk to the person on the other end and explain what you're doing - nobody wants to sit there listening to your heavy breathing and keys pounding in the background.

Sometimes the technical screen is a "data challenge". You might be given a set of anonymized or fake data from the company and asked to answer questions, derive insights, or write a report about the data. These can be fairly open-ended, so it helps to ask questions. If they ask for your code, then make sure it is clean and conforms to <a href="https://www.python.org/dev/peps/pep-0008/" target="_blank">PEP8</a> standards.

### In-person interview
These are miserable. They might take anywhere from 2-5+ hours and usually involve talking with multiple people from the company for ~45 min/person. If possible, ask who you will be meeting with and their role at the company. Stalk the hell out of them. If you're meeting with an engineer, you will likely get CS questions. If you're meeting with a product manager, you will likely get questions about how you would help the company's products.

Anything is fair game here which usually means writing code on a whiteboard. This is totally unnatural and probably a terrible method of interviewing. But, as with everything else in this process, you have to play to win. Try practicing problems on a board in front of a friend. During the process, explain your reasoning and approach. This is definitely the subject of a much longer post, so let me just say to practice, practice, practice.

Often you may get a "case study" question where you walk through how you might attempt to solve a machine learning problem at a company. Think about the company before you go in and think through what sorts of data they may have and how you might use that data in a machine learning problem. For example, if it is a digital advertising company, they probably care about click-through rates (CTR). What kind of problem is this? Well, maybe you could think of it as a binary classification problem. What sorts of models would you use? Oh, logistic regression? Why would you use that? What sorts of features would you use? Wow, you mentioned a lot of categorical variables (e.g. last visited domain) which blows up your feature space - what are some methods of shrinking that feature space? CTR is usually low, right? How would you then deal with the label-imbalance in your training set? And so on. I usually spent an hour the night before researching the company, thinking of potential questions like these, and then brushing up on areas that might be relevant (like, say, recommender systems for an eCommerce company).

Lastly, everybody will ask you if you have any questions for them. This can be tedious by the 4th person when you've already asked all your questions. However, you should always have questions. If you're stuck, you can always ask reusable questions about the interviewer - what made you excited to come to the company? In what way would we work together if I were to work here?

## And In The End

I ended up taking a job at <a href="https://www.birchbox.com/" target="_blank">Birchbox</a> where I have been happily employed since June. We have a wealth of data, and data science is core to the business - it's a good situation! If you're fortunate enough to be struggling with deciding where to work, I found this Stitch Fix blog <a href="http://multithreaded.stitchfix.com/blog/2015/05/27/interesting-work-not-interesting-jobs/" target="_blank">post</a> helpful.

## Existential Crises on a Soapbox

Let me close this series with a short rant:

Everybody has their own reasons for wanting to leave academia, but it does seem to be a universal experience that everybody struggles with this decision. I struggled as well as everybody I've talked to who wants to leave or has left academia. This subject could clearly cover many posts, so let me just touch on two points.

Firstly, I have not met anybody who has left academia and regretted it. On the other hand, I have met many people who have stayed in academia and regretted it.

Secondly, when researching potential companies to apply to in NYC, I thought a lot about how much I would have to sell my soul. I think it's easy to smugly think that if you do science then you do not have to worry about your moral place in the universe because you are furthering human knowledge, building technology to help mankind, or whatever else you write in grants. I am sure other disciplines are different, but I think that most physicists know that their research will not be directly useful for decades. And so, it's not like I laid in bed at night in grad school thinking about how my work would change the world. And yet! And yet for some reason when searching for jobs I worried about this. Why did I have this double standard? I remember thinking that finance was morally volatile (you can do great harm or great good). Advertising seemed morally neutral (it neither helps nor hurts most people). Some health industries could be morally positive. In the end, I took a job, and, just like in grad school, I still do not lay in bed thinking about how my work might fall on the universal moral scale. Just like in grad school, I still derive my enjoyment from working on interesting, hard problems. But unlike grad school, taking a job is not a 6-year committment. Treat it like a postdoc. Try out a new industry. Have fun!
