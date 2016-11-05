---
layout: post
title: Writing a Jekyll Blog on Github with Docker For Windows
date: "2016-11-05"
---

Having moved development computers a few times in the last year I was keen to make blog maintenance as simple as possible. Generally finding myself on a Windows machine as my primary work development environment I wanted that to be the primary blogging environment as well.

While a number of changes to jekyll and github's usage of jekyll have make it a lot easier to maintain a jekyll blog on windows, docker seems a better long term proposition.

The end result is that the following gets blogging up and run it in jekyll watch mode.

```ShellSession
git clone git@github.com:hombredequeso/hombredequeso.github.com.git  
cd hombredequeso.github.com  
docker run -v %cd%:/usr/src/app -p "4000:4000" starefossen/github-pages
```

The devil, of course, is in the detail.

The first detail to have caused problems was getting a correctly formatted `docker run`, specifically mounting a volume from the current directory in a cmd shell. `%cd%` is the end result to achieve that.

The second detail that seems to cause problems for people is ensuring that docker itself is able to share folders. To do that, go to `Docker > Settings > Shared Drives >` and make sure the drive with the blog on it is selected as _Shared_.

