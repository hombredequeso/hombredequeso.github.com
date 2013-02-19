---
layout: post
title: Review, Implementing Domain-Driven Design, Chapter 1
---

#{{ page.title }}#

<p class="meta">19 February 2013</p>

In the beginning, we had the [*Big Blue Book (Eric Evans, Domain-Driven Design)*][Evans]. Now, we have the [*Big Red Book (Vaughn Vernon, Implementing Domain-Driven Design)*][Vernon]. Vernon's goal is to provide a somewhat more practical guide to using Domain-Driven Design. Where Evans' book is quite a heavy read, Vernon's book is likely to be more accessible, both in its approach to the content and in style.

Structurally, the Big Red Book takes a major departure from Evans book, in presenting the strategic patterns discussed in the final part of Evans book first, before moving on to the tactical patterns that get discussed earlier (primarily in part 2) of Evans book. For many people, this means that many of the tactical patterns that they primarily identify with domain driven design (aggregate, value object, entity, repository, factory) are discussed after the lesser known strategic patterns (bounded context, context map). I expect that this will have the effect of elevating the strategic patterns to the more prominent place they deserve in people's thinking about DDD.

Chapter 1 addresses some of the high level questions around domain-driven design: why and when you should use domain-driven design, a basic introduction to ubiquitous language with and its connection to the code, and the business value of domain-driven design.

One part that especially stood out to me was on when to use domain-driven design. Vernon gives a scoring table against which you measure your project, and if you score greater than 7 suggests that you consider using DDD. For instance, having 30-40 user stories/use cases, will score you 2 points. Variations on your system growing over the years, in various/unexpected ways score 3,4 or 5 points. Suffice to say, I think every project I have ever worked on would score more than 7 points. Perhaps that should not be a surprise, but depending on what wisdom you read elsewhere, a good number of people seem to suggest that DDD is only applicable to a small number of the largest and most complex projects.

The material through the book is closely tied to a specific example which is used to illustrate the various aspects of DDD, namely a corporate collaboration suite together with a scrum management product. I am especially interested to see how this approach works out. One of the recurring issues found in discussions about DDD on the internet is the request for examples and/or samples by people starting out trying to learn the DDD approach. More often than not this seems to be met with the response that anything requiring DDD is so complex that a sample will not illustrate what is required, or the provision of a any sample is roundly bagged for either not being a complex enough domain, or adding needless infrastructural complexity. I know that I experienced similar frustrations myself when starting out with DDD, and there is only so far you can get with questions on forums without some sort of more overarching guidance. With any luck, Vernon's use of an example throughout a book of this length (611 pgs) will steer the right course.

[Evans]: http://www.informit.com/store/domain-driven-design-tackling-complexity-in-the-heart-9780321125217
[Vernon]: http://www.informit.com/store/implementing-domain-driven-design-9780133039894