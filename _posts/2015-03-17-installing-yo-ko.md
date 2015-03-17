---
layout: post
title: Installing yo ko
---

#{{ page.title }}#

<p class="meta">17 March 2015</p>


If you have watched [ Steve Sanderson - Architecting large Single Page Applications with Knockout.js](https://vimeo.com/97519516), you may be inspired to try out the Yeoman Knockout single-page application generator. However, if all the bits and pieces are not installed up on your machine, it may take sometime to sort through making it work.

The main challenges likely to be faced are having everything installed so that it 'just works'. In sum, it is necessary to have: nodejs installed, git installed and in the path, Yeoman installed, the Yeoman Knockout generator installed, bower installed, and an http server available. If you have all that sorted out, it just might work!

Here is something of the procedure required on a machine I used to try it out.

* Install [nodesjs](https://nodejs.org/).

* Ensure that git is somewhere in the path.

```
> git --version
git version 1.9.5.github.0
```

If git is not in the path, if it is already installed on the machine, add its location to the path env var.
Note that if using github for windows, [this](http://www.chambaud.com/2013/07/08/adding-git-to-path-when-using-github-for-windows) may be of assistance in figuring out where it is.
If git is not even installed on the machine (how could that be?), you could do a lot worse than [GitHub for Windows](https://windows.github.com/).

* Install [Yeoman](http://yeoman.io/).

```
> npm install -g yo
```

* Install the Yeoman Knockout generator.

```
> npm install -g generator-ko
```

* Install bower.

```
> npm install -g bower
```

* Create a single-page application website using Yeoman.

```
> yo ko
```

* A simple web server is required, to simply server up the files. The nodejs http-server is a good alternative.

```
> npm install http-server -g
```

* The dev version of the website should now be able to be run.

```
> http-server src
```

* Compile the application.

```
> gulp
```

* Now the release version of the website should run.

```
> http-server dist
```

* Run the tests.

```
> karma start
```

### Git and Firewalls
Firewalls are wonderful things, but if an error something like the following appears, it is likely you are behind a (corporate) firewall that does not have the default github port open.

```
bower ECMDERR       Failed to execute "git ls-remote --tags --heads git://github.com/millermedeiros/crossroads.js.git", exit code of #128

Additional error details:
fatal: unable to connect to github.com:
github.com[0: 192.30.252.131]: errno=No error
```

In that case, instruct git to use https instead of its default port.

```
> git config --global url."https://".insteadOf git://
```

