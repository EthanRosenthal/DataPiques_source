date: '2015-09-29'
layout: post
slug: insight-to-interviews
title: Yet Another PhD to Data Science Post (Part II)

Welcome to Part II of my journey from academic to industry data scientist. In my previous [post]({filename}/2015-09-23-start-to-insight.md), I wrote of my preparation leading up to the application to <a href="http://www.insightdatascience.com" target="_blank">Insight Data Science</a>. I will now talk about the Insight application process, the actual program, and demoing my project at companies. I will save studying for interviews and the actual interview process for the final [post]({filename}/2015-10-06-interviews-to-job.md).

## Application to Insight

The Insight written application is fairly straightforward. I went for the shotgun approach - I wrote everything that I could for each of the entry boxes hoping that something would stick. I think it's important to actually *have* something to enter into each of the boxes. If they ask for a link to your LinkedIn page, then you should have a filled out LinkedIn page.

After making it past the first round, I was invited to do a Skype interview. I was tasked with sharing my screen and walking the interviewer through an interesting project of mine. One should try to show off as many "industrial data science" tools as possible during this stage. It may be tempting to pick a research project because it shows your smarts, but this is likely ill-advised unless said project uses lots of relevant Python or R. I chose to walk though some of my [Festival Chatter]({filename}/2014-08-31-festival-chatter-part1.md}) project for which I created an IPython Notebook to show execution of various scripts and to produce some plots. This was a better suited talk than any of my research could have been (it's hard to get people excited about nematic fluctuations in superconductors).

It should also be noted that your communication skills are likely being judged during this interview. I know this because listening to the other fellows give talks at Insight was night-and-day better than any scientific conference that I have ever been too.

Things seemed to go well during my interview. In the midst of doing laundry a couple weeks later, I got an email saying that I had been accepted. I was totally pumped, and then I stared down at the laundry and realized that it was not going to do itself.

## Caveats

Before getting into my experience at Insight, I think it's important to understand what bootcamps can and cannot do. Many programs boast high placement rates and starting salaries. We are in a fantastic time to be in the data industry, so these reports could well be true. Inisght mentions a 100% placement rate on their website, and it is easy to see that and think, "Hell yeah! If I just get accepted then I've got a guaranteed job!" However, no bootcamp can *make* a company hire you. It is still up to you to interview and land a job. Bootcamps are merely a foot in the door at many companies (albeit an excessively large foot).

## Insight Schedule

The schedule of Insight goes roughly as follows:

- 3 weeks working on a project
- 1 week refining project demonstration
- 3 weeks demoing at companies
- Interview until you get hired

Notice that there are no real lectures or classes. You learn by doing, and you learn from your peers. This method of learning is likely not for everyone. I enjoyed it because it reminded me of how I learned in the lab, but that's just me. Also, Insight seems to do an incredible job of screening for genuinely nice people which greatly facilitated peer learning.

During the time that you are working on your project, companies that are looking to hire data scientists send people to the Insight offices and talk about data science at their company and then take questions. This is so valuable as a job-seeker. I had spent so much time reading useless job postings or trying to figure what a company actually does from their vague website. Here, I could hear directly from the horses mouth and ask whatever questions I wanted. It also became apparent how different the role of a data scientist is across companies. In turn, the more I heard about the different roles, the better I could figure out what I was actually interested in.

## The Project

The canonical Insight project consists of getting data from somewhere, building some sort of algorithm that uses the data as an input, and then creating a website wherein the user inputs something and the algorithm spits something out. I know that that was a vague sentence, but I had to keep it vague to cover the diversity of projects! And I didn't even do a good job - one person in my session built a Chrome extension for Airbnb which breaks the Insight mold.

The idea is that the project shows your end-to-end skills. You can gather and massage data, store it in a database, use/train algorithms, deal with front-end aspects to the website, and pitch the whole thing with business applications in mind. In 3-4 weeks.

### TutorWorthy

I approached my project hoping to use data to train a conventional classification or regression problem so that I could demonstrate knowledge of machine learning and get more experience with <a href="http://scikit-learn.org/" target="_blank">scikit-learn</a>. And so, I scraped all profiles for NYC academic tutors off of the website <a href="https://www.wyzant.com/" target="_blank">WyzAnt</a>. The idea was that I would build a model to predict how much a tutor should charge. A user would be able to input their WyzAnt profile, and I would tell them if they were charging too much or too little.

This sounds like a straight forward regression problem, right? Build features out of the profiles (location, reviews, academic background, etc...) and train any sort of regression model. The problem was that people would charge wildly different amounts even when they had quite similar profiles. I am sure that there is an economic term for markets in which prices do not converge upon their market values. This was the issue with WyzAnt, and I should know it - I was charging much less for my own private tutoring than many of the people with similar profiles on the website!

And so, in the parlance of modern startups, I pivotted. Instead of a pricing model, I built a tool that would show the user a distribution of prices charged by similar tutors so that the user could find their own market value. This was relatively basic and unsupervised - I measured various similarities (cosine, Jaccard, etc...) between the user's profile features and all other tutors and then displayed a kernal density plot of the the market prices weighted by the similarities. While the tool was fun, there was no machine learning going on which I think lost the interest of some companies.

Here is a link to the finished project, <a href="http://tutorworthy.ethanrosenthal.com/" target="_blank">TutorWorthy</a>. Unfortunately, WyzAnt changed something about their website which broke my scraper. Because my website would scrape in real time, it does not work anymore. However, you can click the slides tab to see my "pitch deck".

### Think Hard

There was a flaw in my initial thinking about the project - did you notice it?

> A user would be able to input their WyzAnt profile, and I would tell them if they were charging too much or too little.

This statement means that I was working off of the assumption that there would be people that do not charge the "correct" amount. And yet, I was attempting to train a model on these peoples' features and predict their rates. If one is assuming that the ground truth of a machine learning model is not correct from the output, then it seems pretty silly to try to train a model! In hindsight this appears pretty obvious to me, but it was not so at the time. This should serve as a warning to those hoping to build some sort of product out of a machine learning model - from a common sense standpoint, should the features be able to predict the data?

## Demos

We ended up each demoing our projects (in groups) at about 8 or so companies that we chose from the large list of companies that had come into Insight. This was very cool because we actually got to spend an hour or two at the offices of 8 different companies in New York City. This was also another chance for us to gauge the companies. Some companies revealed themselves to be full of warm, curious people, while others were worse than cocky professors at scientific conferences. I thought a good test was to to see if anybody laughed at any of our jokes.

The companies were given our resumes at the demos and were encouraged to later contact anybody who they felt like interviewing. Talk about being directly judged against your peers, huh?

## Studying

In between demoing and frantically checking email to see if any companies got back to us, we studied for interviews. But I'll talk about that [next]({filename}/2015-10-06-interviews-to-job.md) time
