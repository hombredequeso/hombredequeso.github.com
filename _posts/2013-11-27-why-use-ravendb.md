---
layout: post
title: Why You Should Consider Using RavenDb
---

#{{ page.title }}#

<p class="meta">27 November 2013</p>

[Why You Should Never Use MongoDB][Mei] is a detailed post describing the experiences of the open source Diaspora project with MongoDb. I do not think it would be a spoiler for anyone if I tell you that it didn’t turn out too well for them. For myself, the outcome seemed nearly inevitable somewhere during the discussion of what constitues a document, with the large scope of data that a document held.

What was a little more difficult to see coming was the various conclusions the article came to. Most notably, I would highlight these: 

* The *only* thing it’s (MongoDb) good at is storing arbitrary pieces of JSON. “Arbitrary,” in this context, means that you don’t care *at all* what’s inside that JSON.

* I’ve heard many people talk about dropping MongoDB in to their web application as a replacement for MySQL or PostgreSQL. There are no circumstances under which that is a good idea. Schema flexibility sounds like a great idea, but the only time it’s actually useful is when the structure of your data has no value. If you have an implicit schema — meaning, if there are things you are expecting in that JSON — then MongoDB is the wrong choice.

I do not have any production experience with MongoDB, but I do have experience with [RavenDB] [RavenDB]. It is a document database and as such, has a number of fundamental similarities to MongoDB. Certainly, at a high level it is possible to read the author’s critique and conclusions as applying equally to RavenDB.

It would be fair to say that neither the author’s description of how MongoDB was used on this project and problems they ran into, nor the conclusions, rang true with my experience of RavenDB. By the time I was reading the conclusions, it was almost like reading about another kind of database (and maybe I was - it sounded a lot like a key-value store to me).

However, it is not my purpose primarily to discuss the article in detail. One of the strengths of the article was it’s form, that of a case study. As such, I think it appropriate to offer up a counter case study of a project using a document database that did not end up a train-wreck.

Over the past year or two, I have worked on a freight-forwarding application that used [RavenDB][RavenDB]. The database stores data related to costing including buy rates, sell rates, and standard tariffs. This data is used for such purposes as determining the cost and sell amounts of a shipment, and marginal variations in quotes.

It would be fair to say that for the developers on the team, most would tend to prefer using RavenDB over a relational database. That said, lest one peg us as RavenDB zealots, for our most recent greenfields project we chose to use a relational database.

Furthermore, when I look at our database I simply do not recognise the (mis)characterization that each document is nothing more than an arbitrary piece of JSON. Somewhere between the outcome of the Diaspora project and the conclusions drawn from it something went wrong.

There is one thing that I suspect we would all agree on, that choosing a database for a project is a matter of choosing one appropriate to the problem at hand, be that a relational database, a document database, graph database or something else. Where we would disagree is over appropriate uses of document databases. I certainly am not claiming RavenDB as an appropriate choice for the Diaspora project. I do, however, find the conclusions of the article problematic, as they contradict my own happy experience using RavenDB, which I will present in the upcoming series of blog posts.

[Mei]: http://www.sarahmei.com/blog/2013/11/11/why-you-should-never-use-mongodb/
[RavenDB]: http://ravendb.net/

