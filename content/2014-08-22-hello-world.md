date: '2014-08-22'
layout: post
title: Setting up a website and separate blog repository hosted on GitHub

As the title of the blog suggests, I would like to use this space to write about anything "data"-related that piques my interest. Likely, this will consist of personal and academic projects.

As the title of this post suggests, I would like to explain how I created this blog and my [website](http://ethanrosenthal.com).

## Setting up the website - [ethanrosenthal.com](http://ethanrosenthal.com)

During my 5 years at Columbia, I have sporadically messed around with coding html and css in an attempt to make a personal website. Various iterations were hosted on the physics department servers, but, in the interest of long-term hosting, I decided to migrate everything to [GitHub](https://github.com/). This was largely inspired by these blog posts from [Joshua Lande](http://joshualande.com/jekyll-github-pages-poole/) and [Burton DeWilde](http://bdewilde.github.io/blog/2014/08/10/on-starting-over-with-jekyll/) though implemented a bit differently.

#### GitHub Pages

GitHub has a feature called [GitHub Pages](https://pages.github.com/) that provides one free website per account. Setting up your free website is quite easy (and detailed better at the above link):

+ Create a new repository titled *username*.github.io
+ Clone the repository to your computer
+ "Initialize" the repo by creating an *index.html* file

Your new GitHub website is *username*.github.io. You can now build your website in the repo you created, and GitHub takes care of the rest.

All I had to do was copy all of the files from the physics server into my new repo, and my website was built at [ethanrosenthal.github.io](ethanrosenthal.github.io) (because "ethanrosenthal" is my GitHub username).

#### Adding a custom domain

I wanted to use a custom domain instead of ethanrosenthal.github.io, so I bought ethanrosenthal.com through [namecheap.com](namecheap.com) for ~$10/year. In order setup the custom domain on namecheap, go to "Your Domains/Products," click the purchased custom domain, and then click "All Host Records." You should now fill in the top three rows as follows:

__Host Name__ | __IP ADDRESS/URL__  | __RECORD TYPE__ | __TTL__
 ------------ | ------------------  | --------------- | --------
@             | 192.30.252.153      |  A (Address)    | 1800
@             | 192.30.252.154      |  A (Address)    | 1800
www           | *username*.github.io|  CNAME (Alias)  | 1800

Below is a screenshot of my filled in namecheap dashboard:

![namecheap dashboard]({filename}assets/img/2014-08-24-namecheap-dashboard.png)

In addition to the above modifications that you have to make, you need to include a file in your *username*.github.io repo titled "CNAME" which contains your custom domain name in it. For example, mine simply says "ethanrosenthal.com". It may take some time for these changes to fully propagate, but, eventually, your custom domain should successfully redirect to the GitHub Pages repo (though still display the custom domain in the url bar).

## Setting up the blog - [blog.ethanrosenthal.com](http://blog.ethanrosenthal.com)

In addition to one free personal website, GitHub Pages allows for unlimited "Project" Pages. You can create a Project Page for any repository in your GitHub account. The Project Page is essentially a website associated with your repo. This is how I chose to setup the blog. I created a new repo called "blog" and filled it with all of the files for a full-fledged blog.

#### Jekyll and Poole

Considering it took me many years to create my website, I opted to use a blog template rather than start from scratch. GitHub Pages are powered by [Jekyll](http://jekyllrb.com), a static site generator. Blogging is quite simple - you can write your blog posts in Markdown, and Jekyll pretty much takes care of anything else that you do not feel like customizing. This even includes the layout of everything if you choose to download [Poole](http://getpoole.com/). Simply go to the [Poole GitHub](https://github.com/poole/poole), clone the repo, and then type

```bash
jekyll serve
```

in your terminal. Jekyll will now build a static website, as specified and laid out by Poole, in the folder _site/. The key difference between your free GitHub website and GitHub Project Pages is that you need to push your website to a separate "gh-pages" branch in order to build the Project Pages website associated with your repository. So, after building your static website on your local machine, you must switch to the gh-pages branch, add/commit your files, and then push to GitHub. The Jekyll-built *repo* website will now be located at *username*.github.io/*repo*

#### Custom Project Pages domains

I ran into some issues with my blog Project Page not formatting correctly because of my custom domain. I am still not exactly sure why this was happening, but creating a custom domain for the blog solved the problem. To do this, I added the following under SUB-DOMAIN SETTINGS on the namecheap dashboard

__Host Name__ | __IP ADDRESS/URL__  | __RECORD TYPE__ | __TTL__
 ------------ | ------------------  | --------------- | --------
blog          | ethanrosenthal.github.io. |  CNAME (Alias) | 1800

I also added a CNAME file to the blog repo containing the line "blog.ethanrosenthal.com". Now, the blog is located at [blog.ethanrosenthal.com](http://blog.ethanrosenthal.com/), and everything redirects and formats fine.


## Lastly

There are a couple other things that I have done, like adding an Archive section and Google Analytics tracking, but these tasks have been documented in the blog posts that I linked to at the top. Hopefully, I will have some more interesting projects to post soon.
