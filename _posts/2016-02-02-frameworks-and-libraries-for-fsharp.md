---
layout: post
title: Frameworks and Libraries for an F# Project
---

#{{ page.title }}#

<p class="meta">2 February 2016</p>


Finding ourselves [pushed in the direction of a more functional programming paradigm](http://hombredequeso.id.au/2016/01/29/why-so-functional.html), our team chose a new, stand-alone project on which to try out F#. At first sight, it was not the sort of project that would show off the benefits of a functional language. But it did have the advantage of being relatively simple (or at least, as simple as anything looks before you actually try to do it). Historically, that suited our team's approach to new technologies: try it first on something fairly simple, something that if things went wrong wouldn't ruin the company; then if it works out, broaden its scope. And so we set out on our new project.

Commencing a project in a new language poses a number of distinct challenges. Such challenges can be even greater when a change in programming paradigm is thrown into the mix, as is case in moving from C# to F#. Some of the main challenges we faced included:

* determining appropriate frameworks and libraries to use

* structuring an F# solution

* effectively using the F# language


# Frameworks
As a .NET language, F# has the capability of working with a very large number of existing .NET libraries, primarily written in C#. In addition, there is also a small, but rapidly growing number of F# libraries.
Our project required a website, with distributed background services, which ended up hosted in Azure. The final choice of key frameworks/libraries was:

* [Nancy](http://nancyfx.org/): For the website. All out code was written in F#, using the C# Nancy library.

* [DBML file type provider](https://msdn.microsoft.com/en-us/library/hh361039.aspx) An F# library used for SQL Azure database access.

* [NServiceBus](http://particular.net/nservicebus): a framework for distributed communication between back-end services written in C#. All our code was written in F#, however the messaging assemblies were in C# to simplify communication with additional nservicebus endpoints written in C#.

* [BrockAllen.MembershipReboot](https://github.com/brockallen/BrockAllen.MembershipReboot) For authentication/authorization, another C# library.

* [FsUnit](https://github.com/fsprojects/FsUnit) : a unit testing assembly providing functional testing syntax. We used it on top of NUnit as the base testing framework..

* [FsCheck](https://github.com/fscheck/FsCheck): an F# library for property based testing.

    
# Frameworks: The Trade-Offs
This choice of libraries and frameworks came with a number of advantages and disadvantages:
One of the most significant advantages, which most heavily swayed our choice, was the benefit of using familiar tools.
A second advantage is that the more mature libraries and frameworks have a host of support options, the most obvious being the sheer volume of support from our friend google. This is not to suggest that the support from the F# community for newer F# specific solutions is anything less than great, but simply a question of volume of support, particuarly around the odds that someone somewhere has hit the same problem as you and written about it.

Probably the biggest disadvantage in using existing C# based libraries is that they are written, rather predictably, for usage in an object-oriented context. It is undoubtedly true that F# is a hybrid language, capable of being used in a functional, or object-oriented way. However, its full benefits, and arguably most elegent code, is functional. Interacting with C# libraries forces one into an object-oriented mode of programming. We found this posed a few challenges. It meant that rather than setting out to write specifically F# code, at these points in the program we found ourselves effectively writing C# code translated into F#. Such code mostly swerved between passably adequate, through to pretty ugly. Undoubtedly, some of that was due to inexperience. However, even with the benefit of rework given more experience, such code rarely seems to rise above the level of adequate. Overall, the use of existing C# based libraries is something of a two edged sword. It provides familiarity, avoids having to learn yet another library/framework, and when chosen carefully offers a good deal of support. On the other hand, it pushes one into a 'translated from C#' mode of programming which shields one from the extent of the jump between programming paradigms, and at worst retards one’s progress in the language as the ingrained habits (good and bad) of one paradigm prevent one from understanding the new paradigm.

# Conclusion
So in hindsight would we do the same again? On balance, I find myself thinking we probably made the right decision. Yes, there was a week or so of weeping and gnashing of teeth while we put it all together. Sometimes I pine for the purity that using a more functional library would have given (In particular I'm looking at you [Suave](https://suave.io/)). But overall, using libraries and frameworks we were familiar with, notably Nancy and NServiceBus, enabled us to get something going without having to also learn a new framework, with all its features and, well, idosyncracies. Now in production, the choice of nservicebus in particular, means that we continue to get the benefit of using a familiar distributed messaging system, with all the functionality and tooling it has build up over years.

I suspect that the worst part of this trade-off is that the very early stages of getting a project going involve heavily working with the frameworks and libraries. For the developer this means that having seen all the wonderful things that functional F# offers, the first production-like experience one might have of F# is little more than struggling to mentally translate C# code into F#. Not only can that be a frustrating experience, it doesn’t shine a light on the best things F# has to offer. Yet, all being said and done, we remain content with our choice of technologies, and in the longer term, it has left us working with mature libraries we are familiar with (which has helped in sorting out production issues). More importantly, as time goes by the glue-code between the libraries and our code requires little attention, leaving us to enjoys F# the way nature intended it.


