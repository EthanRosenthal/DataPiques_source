date: '2015-09-23'
layout: post
title: Yet Another PhD to Data Science Post (Part I)

The internet is awash with posts by former PhD students who have succesfully transitioned into data scientist roles in industry (see <a href="https://warrenmar.wordpress.com/2013/08/03/beginning-data-scientist/" target="_blank">here</a>, <a href="http://treycausey.com/getting_started.html">here</a>, <a href="http://womeninastronomy.blogspot.com/2013/01/datascience.html" target="_blank">here</a>, and tangentially <a href="https://www.chrisstucchio.com/blog/2012/leaving_academia.html" target="_blank">here</a>). I loved reading these posts while studying for job interviews because I felt like the more I saw examples of sucessful transitions, the more likely it seemed that such feats were actually achievable. I am going to try to touch on many of the aspects of leaving academia for data science while trying to limit the length of this post. This will not be a guide so much as my personal experience navigating this transition. I'll speak of my background, my study materials, and everything leading up to applying to the <a href="http://www.insightdatascience.com" target="_blank">Insight Data Science</a> program. In the next [post]({{page.next.url}}), I will talk about my experience at Insight and job interviews. Allow me to now add my story to the mix. Hopefully it serves as a boost of confidence for interested students.


## Background

I was a PhD student in Columbia University's physics department studying experimental condensed matter physics. My timeline was as follows:

- April 2014: Start studying data science (5th year of grad school).
- January-March 2015: Participate in Insight Data Science.
- March 2015: Interviews
- April 2015: Accept job offer
- May 2015: Defend thesis and graduate
- June 2015: Start working

Thus, it took a year to study and get a job which is about the minimum amount of time that I would recommend. Contrary to what many students seem to think, *if you wait until your last couple months of graduate school to decide to try to get a data science job, then you are going to have a bad time.*

My technical background consisted entirely of MATLAB programming. I had taken one programming class in college, "Computers and Numerical Algorithms", that used MATLAB. I then used MATLAB in my research for about 5 years. One half of the research programming consisted of custom image analysis scripts (lots of 2D Fourier analysis). The other half dealt with numerically evaluating various physics models on mesh grids (lots of linear algebra). I also spent a month performing Density Functional Theory (DFT) calculations on a remote computer which gave me some experience navigating the command line. I should note that I was an experimentalist, so I spent a good bit of time performing experimental work (read: not programming, read: fixing eternally breaking machines).

My point with writing the above paragraph is not to show how much or little programming experience I had but to instead provide a baseline for the audience. In hindsight, of course I wish that I had performed my research in Python instead of MATLAB as there seems to be a clear anti-MATLAB bias in the modern data science industry. I feel that this is totally unfounded (why is R okay and MATLAB not? I guess it's the whole free thing?), but so be it. Learning any programming language teaches you how to translate ideas into a logical syntax, and MATLAB is no different. Alas, you have to play the game to win, and *you will need more than MATLAB experience on your resume when you apply for jobs.*

If you are relatively early in grad school, then it is a great idea to try to migrate to Python or some other more job-relevant language. Things definitely get hard if you've spent years amassing a codebase in MATLAB or FORTRAN or ROOT or IDL or IGOR or any of these other capitalized legacy languages and you have to produce results fast.

## Preparation

After making the decision to go "all in" on data science in April 2014, I used my free time during the rest of the year to work on personal projects, take courses, and talk to people. I knew that I wanted to apply to Insight in October, and it helped to think of that month as a sort of mental goal post. Sometime during the summer, I also saw this excellent <a href="http://insightdatascience.com/blog/preparing_for_insight.html" target="_blank">post</a> on the Insight blog which helped to guide some of my studies.

### Getting Started

Having zero experience with Python, I decided to get my feet wet with various sorts of mini-projects. The first step was to get the necessary software and libraries installed. Like many people who have made the same unfortunate decision before me, I initially attempted to program on my Windows computer. After one too many late nights spent trying to get libraries installed to no avail, I setup a dual-boot installation of Ubuntu on my laptop. I highly recommend not bothering to waste time with trying to program Python in Windows (stick to Linux or OSX). The fact that it was easier for me to setup a separate Linux installation and navigate a brand new operating system than to try to get things to work in Windows should be sufficient evidence.

Although I did not install it at the time, the <a href="https://store.continuum.io/cshop/anaconda/" target="_blank">Anaconda</a> Python distribution is probably the best way for a newcomer to get quickly setup with the scientific Python stack. The idea is that the people at Continuum Analytics who created this distribution have gotten rid of the main impediment to getting started with scientific computing in Python - they automatically install most libraries that you would need when you download their distribution.

Anaconda also comes with <a href="http://ipython.org/" target="_blank">IPython</a> and the <a href="http://ipython.org/notebook.html" target="_blank">IPython notebook</a> which have grown into essential tools for me. IPython is an interactive console where you can type Python commands and evaluate them interactively (like the console in MATLAB). The IPython notebook (and now Jupyter notebook) is like a Mathematica notebook for Python programming. This sounds weird, and I am sure software engineers would scoff at the idea of using a weird GUI-thing for programming, but it is actually quite powerful. I like to "play" with data in an interactive console, but I forget all of the little commands that I write. By doing this in an IPython notebook, all of the previous commands are saved. No more remembering!

With the necessary software installed, I worked on a couple different things:

- A <a href="http://aaronroberts.squarespace.com/" target="_blank">friend</a> wanted to <a href="https://github.com/robertsaj2/BaseballProject" target="_blank">predict baseball game scores</a> and asked me to help him scrape the ESPN website for stats.

- I read through the lecture notes in the <a href="https://developers.google.com/edu/python/?hl=en" target="_blank">Google Python Class</a> and worked through some problems.

- I <a href="https://github.com/EthanRosenthal/stm-routines/tree/master/lifeas-tightbinding" target="_blank">translated</a> some of my research MATLAB code to Python.

None of these projects were particularly impressive, but I think it's important to accomplish *anything* with your code at the beginning. Realizing how easy it was to write a quick script to scrape websites was an eye-opener. And besides, these projects give you something to put on your <a href="https://www.github.com" target="_blank">GitHub</a> (and yes, you should *definitely* make a GitHub account. And don't leave it empty.).

### Festival Chatter

Wth some basic experience under my belt, I then attempted a more "end-to-end" project involving collecting, cleaning, and analyzing data. I documented the project in a series of blog posts. See parts [1]({% post_url 2014-08-31-festival-chatter-part1 %}), [2]({% post_url 2014-09-09-festival-chatter-part2 %}), [3]({% post_url 2014-10-6-festival-chatter-part3 %}), and [4]({% post_url 2014-11-25-festival-chatter-part4 %}) for more details. In a sentence - I used the Twitter API to collect every tweet mentioning the Bonnaroo music festival and saw what sorts of information I could tweeze out of this corpus. Although there was no machine learning, I learned quite a bit about cleaning text data, <a href="http://pandas.pydata.org/" target="_blank">pandas</a>, and the pain of <a href="http://matplotlib.org/" target="_blank">matplotlib</a>, Writing about the project gave me data science "evidence" for my resume. The posts also served as a good motivation to stick with the projects, write reasonably clean code, and spend time perfecting visualizations (which was probably the most time-consuming part!).

### Courses

I collected the Bonnaroo Twitter data at the beginning of June 2014 but did not get to writing blog posts until the end of the summer because I got pretty bogged down in Coursera courses. I took two courses which I'll briefly review below. It should be noted that I have no evidence whatsoever that companies actually care about what classes or MOOCs you have taken. Aiming for a "certificate" helped me to personally stay on track with the classes, but I do not think that it matters. Learning and being able to speak to the material is the most important part.

#### Andrew Ng's <a href="https://www.coursera.org/learn/machine-learning" target="_blank">Machine Learning</a>
This course is excellent. Everybody should take it. The only caveat is that it is taught in MATLAB/Octave. While this is perfectly fine for learning the math and concepts, it would have been nice to do it in Python in order to get more experience with the language and packages. So be it. The course covers a good bit of machine learning at a very accessible level, and Ng is a great teacher to boot. I do think that the recommender systems section is a bit light. Also, I think only L<sub>2</sub> regularization is discussed, so keep in mind that this is not the whole story.

#### Bill Howe's <a href="https://www.coursera.org/course/datasci" target="_blank">Intoduction to Data Science</a>
I had a love/hate relationship with this course. On the one hand, this course consisted of a great diversity of subjects. Going beyond conventional machine learning, you learn some basic natural language processing (bag of words <a href="https://en.wikipedia.org/wiki/Tf%E2%80%93idf" target="_blank">TF-IDF</a> for news article recommendations). The SQL exercises were interesting and hard (multiply matrices in SQL!). I learned out to use ```sys.argv``` to read command-line arguments in Python scripts. Unfortunately, the lectures were quite painful to sit through. I'd recommend taking the class just to try out some of the exercises.

### Talk to people!

With a summer of Python-ing and MOOC's under my belt, I came out of my metaphorical hibernation and reached out to some current data scientists for coffee or drinks. Some people I knew well, some I had not spoken to in years, and some were strangers I was put in touch with by other people. The incredible thing was that every single person I reached out to happily took time to meet up or talk on the phone with me. These conversations were invaluable for both answering my questions ("So, do you have to, like, *like* advertising to work at your place?") to getting me to think about what sort of job I was actually interested in (Type <a href="https://medium.com/@rchang/my-two-year-journey-as-a-data-scientist-at-twitter-f0c13298aee6" target="_blank">A or B</a>).

## Insight Data Science

After a busy six months, I applied to Insight Data Science at the beginning of October. In my [next post]({{page.next.url}}), I will talk about the process of applying and participating in Insight, interviewing at companies, and taking a job!
