---
layout: post
title: Domain-Driven Design, What is it?
---

#{{ page.title }}#

<p class="meta">19 February 2013</p>

For many people, Domain-Driven Design is closely associated with entities, value objects, aggregates, factories, repositories - “The Building Blocks of a Model-Driven Design” ([*Evans, Pt 2*][Evans]). However, these building blocks arise within the context of what may be considered the guiding principle of DDD, a Ubiquitous Language. [Vernon’s *Implementing Domain-Driven Design*][Vernon] effectively reverses Evans’ order of dealing with DDD concepts, in dealing with the strategic patterns first (bounded contexts, core domain, sub-domains, …), then the tactical patterns (entities, value objects, aggregates, …). But like Evans, Vernon closely ties his discussion with the concept of a Uniquitous Language.

As Vernon notes, DDD “is essentially a large pattern language” ([Vernon, p. xxxv][Vernon]). The [*Gang of Four*][Gamma] popularised the design patterns idea with their seminal Design Patterns book, in which a large number of relatively low level design patterns are presented. Fowler’s [*Patterns of Enterprise Application Architecture*][Fowler] presented a number of higher level design patterns. In that tradition, Evans’ DDD may be thought of as design patterns for complex software.

Individually, most of the patterns in DDD have been described before. What makes Evans’s treatment of them significant is that they are drawn together within a broader context that offers them a direction and precision they lack individually.

When attempting to understand DDD, it is helpful to hold this broader context in mind. DDD is a collection of patterns appropriate for building complex software. The principle integrating these patterns and guiding their usage is that of a Ubiquitous Language.


[Evans]: http://www.informit.com/store/domain-driven-design-tackling-complexity-in-the-heart-9780321125217
[Vernon]: http://www.informit.com/store/implementing-domain-driven-design-9780133039894
[Gamma]: http://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612
[Fowler]: http://www.amazon.com/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420