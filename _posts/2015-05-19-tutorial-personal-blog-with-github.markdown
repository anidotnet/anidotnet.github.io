---
layout: post
title: "Tutorial: Create personal blog with Github"
date: "2015-05-19"
categories: [tutorial, blog]
tags: [tutorial, blog, github-pages, jekyll]
description: A tutorial on how to create a personal blog using Github.
---

For the obvious reason this has to be my very first blog entry. When I decided to have my own blog up and running, I had to choose from several options like Wordpress, Blogger, Tumblr and many more. After a little experiment, in the end I chose [Github pages](https://pages.github.com/). Yes, you heard it right! For those who don't know it yet Github is an excellent place to host your own blog. Not only it is completely free, it gives you ultimate control over your blog unlike other blog hosts.

The advantages you get in Github over other blog engines are like:

- Ultimate control over design
- Plethora of free templates
- Custom domain names
- Unlimited storage space in Github and many more.

But this setup requires you to know a couple of things before-hand, like Git, HTML/CSS/JS etc. If you are comfortable with these, you are good to go.

> **So how to create your blog with Github?**

There are several tutorials available in web on how to set it up. I'll be just redirecting you from here to the appropriate and most helpful links which I found useful. Here are the step by step approach.

## **Step 1:**

The first and foremost thing is to head towards [Github](https://github.com/) and create a new repository as **USERNAME**.github.io. Where replace the **USERNAME** with your own Github username. Like, for me it is [anidotnet.github.io](http://anidotnet.github.io). More details can be found on the [Github pages](https://pages.github.com/).

## **Step 2:**

Install [Jekyll](http://jekyllrb.com/) into your local system. It is the static site generator and a tool to create your blog from plain text. Github pages also uses Jekyll to serve your blog. Installing Jekyll into your local will also help you to preview your blog before publishing it to the web. Here is the installation guide as per your platform.

- Linux/MacOS : [http://jekyllrb.com/docs/installation/](http://jekyllrb.com/docs/installation/)
- Windows     : [http://jekyll-windows.juthilo.com/](http://jekyll-windows.juthilo.com/)

If you are on Windows, you might need to do some minor modifications after the installation. You might get "hitimes" error while running Jekyll. If you face that, run the below command in command prompt to reinstall the proper version of hitimes for Windows.

{% highlight winbatch %}
C:\> gem uni hitimes
C:\> gem ins hitimes -v 1.2.1 --platform ruby
{% endhighlight %}

## **Step 3:**

Head over to the Jekyll theme repositories mentioned below and chose your theme.

- [https://github.com/jekyll/jekyll/wiki/Themes](https://github.com/jekyll/jekyll/wiki/Themes)
- [http://jekyllthemes.org/](http://jekyllthemes.org/)

Once you chose your theme, clone the theme to your local machine as follows:

{% highlight bash %}
$ git clone https://github.com/johnotander/pixyll.git USERNAME.github.io
$ cd USERNAME.github.io
$ git remote set-url origin https://github.com/USERNAME/USERNAME.github.io.git
{% endhighlight %}

## **Step 4:**

Configure your blog according to your need by modifying `_config.yml` file. Here is one example from my own blog.

{% highlight yaml %}
# visit https://github.com/mojombo/jekyll/wiki/Configuration for more settings

paginate: 10 # pagination based on number of posts
paginate_path: "page:num"
exclude: ["README.md"] # files to exclude
highlighter: pygments
markdown: kramdown
excerpt_separator: ""   # Workaround for http://blog.slaks.net/2013-08-09/jekyll-tag-was-never-closed

defaults:
  -
    scope:
      path: "" # empty string for all files
    values:
      title: abstract class {...}

description: Abstract Class, Blog of Anindya Chatterjee
author:
  name: Anindya Chatterjee
  email: a@b.com
  github: anidotnet
  twitter: anidotnet
  #pinterest: asd123
  linkedin: anidotnet
  bio: blog of anindya chatterjee!
  email_md5: 9c74f9c66df98052bef13864bffcd09c

rss_path: feed.xml
categories_path: categories.html
tags_path: tags.html

BASE_PATH:
{% endhighlight %}

Now run Jekyll locally from your repository directory,

{% highlight bash %}
$ jekyll serve
{% endhighlight %}

and go to [http://localhost:4000](http://localhost:4000) to preview your changes. If you are happy with the initial configuration, commit the changes to your repo.

{% highlight bash %}
$ git add --all .
$ git commit -m "Config changes"
$ git push -u origin master
{% endhighlight %}

That's all. Your site is now up and running at https://USERNAME.github.io.

## **Step 5:**

Setup a blog writing software. You can use any text editor, but I preferred [Atom](https://atom.io/), which is also happened to be developed by Github and an excellent free text editor available for all major OS platforms. Once you install Atom, install these two packages from the Atom package manager:

- Jekyll - [https://atom.io/packages/jekyll](https://atom.io/packages/jekyll)
- Markdown Writer - [https://atom.io/packages/markdown-writer](https://atom.io/packages/markdown-writer)

These two packages will help you a lot while writing contents for your blog.

## **Step 6:**

Now it is time to write your first blog post. Fire up Atom and open your repository directory using <kbd>Alt+Ctrl+O</kbd>. Go to the menu - `Packages > Jekyll > New Post` and provide the title for your post and it will create a markdown file for your post. Start authoring it with Atom. Go to the menu - `Packages > Markdown Writer > Markup` if you need help with markdown syntaxes.

Once done with the authoring, preview your blog in local and commit it to Github to publish it.

## **Step 7:**

Now if you are feeling happy and innovative by now and want to do a bit of experiment on your blog, here is what you can do.

If you want to tweak the site's layout, you can open `_layouts/default.html` and start editing it. It is the master page for your site. If you want to decorate the post pages, edit the `_layouts/post.html` file. If you want to change the site's css, you can find it in the css directory. Don't modify anything under `_site` as it is auto-generated and will be overwritten by Jekyll.

If you want to set a custom domain name for your blog, go to this [link](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/).

Finally, if you feel like playing the GOD MODE, head over to [Jekyll Documentation](http://jekyllrb.com/docs/home/) page and start hacking!

-- The End --
