---
layout: post
title: Webforms and Gulp
---

#{{ page.title }}#

<p class="meta">21 April 2015</p>



Recently at work we have been moving our front end development over to newer technologies. This was motivated by a number of factors.

*	The existing javascript packaging, using the Telerik ScriptManager was not necessarily difficult to use, but tended to be fiddly to work with.

*	The existing setup required msbuild to package the javascript, slowing development.

*	There was a desire to be able to use newer front-end development technologies, but most would prove very difficult to put into the existing development pipeline.

*	Javascript unit testing was being done with [jstestdriver](http://code.google.com/p/js-test-driver), not a bad choice a few years ago, but now a largely defunct project.


We were looking for a solution that

* Would allow us to easily take advantage of more recent front-end development tools and technologies.

* Disconnect front end development from the rest of the application as much as possible.

* Would not require compilation using Visual Studio when doing front end development. One of the important factors here was to speed up front-end development, reducing the feedback cycle time when editing javascript or html.

* Kept the development environment as close as possible to the production environment.

It was decided the way forward was to use [Gulp](http://gulpjs.com) for processing the javascript development pipeline, and [Karma](http://karma-runner.github.io) for running javascript tests. Putting all this into an existing ASP.NET Webforms application presented a few little hurdles. A sample application with the major features of how it all hangs together can be found [here](https://github.com/hombredequeso/AspNetAndGulpDemo).

Adding karma testing was relatively easy. Using Gulp for the javascript development pipeline posed a few more issues. The majority of these revolve around the fact that there are essentially two independent compilation processes, msbuild for the C# server side, and gulp for the javascript. Where previously these two sides were intertwined through the Telerik ScriptManager, now they are completely independent. Their independence makes front-end development easier and quicker, but of course, at some point they must come together. The critical point for this is where a page requests a javascript file. Our particular solution to this can be seen in the way [MyCacheBustedpage.aspx](https://github.com/hombredequeso/AspNetAndGulpDemo/blob/master/Webapp/MyDomain/MyCacheBustedPage.aspx) uses the [CacheBuster](https://github.com/hombredequeso/AspNetAndGulpDemo/blob/master/Webapp/CacheBuster.cs) class to request the current merged, minified, cache-busted javascript file, which was produced by the gulp pipeline.

The end result of this work has largely been positive. Front-end development is generally easier, faster, with quicker feedback cycles whether working with javascript unit tests or viewing pages. Javascript development with Karma tests does not necessarily require Visual Studio at all. When developing and viewing pages Visual Studio may be running to serve up the pages with useful data, but the development environment itself can be anything (I frequently use vim), and gulp compilation on save is only a second or two long. 

