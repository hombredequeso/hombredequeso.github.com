---
layout: post
title: Why So Functional?
---

#{{ page.title }}#

<p class="meta">29 January 2016</p>

In a recent project at work the decision was made to use F# for parts of the system, specifically a website and back-end processing service. A number of reasons contributed to this decision. A common thread was that changes in our style of using an object-oriented language, C#, was naturally leading us towards a more functional style of coding. In this post I outline some of these issues.
# Immutability
Overtime, more situations appeared in which problems were solved primarily using immutable objects. This stood in contrast to the more object-oriented approach of encapsulating state within a object, mutated by methods on the object. Initially this tended to appear around the periphery of the primary business domains, but over time it crept into various domains as well. Typically, in approaches such as Domain Driven Design, immutable objects are associated with value objects, as opposed to entities. Elsewhere, immutable objects are prevalent at boundaries between systems within the application, say as DTO's (Data Transfer Objects). But when immutable objects start to form central parts of the business functionality, it is a something of a different matter.
# Value Equality
Increased use of value equality was also experienced within the application. This stood in contrast to reference equality, or entity equality determined by an Id property. To a certain extent, this goes along with the increased use of immutable objects. In more and more places beyond the traditional value objects it made sense to work with value equality. In C# this means implementing the IEquatable<T> interface on a class, with all the extra code that goes along with that.
# Static Methods
An increase in static methods partially occurred along-side the increased use of immutable objects. Elsewhere, however, other reasons contributed. For instance, message handling classes started to get more static methods on them. Sometimes this was driven by the realization that a good number of such methods simply could be static, and so should be. In other cases it occurred to make testing easier. For instance, the DoSomething method here:

{% highlight csharp %}
class MyHandler: IHandleMessages<MyCommand>
{
    private readonly ISession _session;
    public MyHandler(ISession session)
    {
        _session = session;
    }
    public void Handle(MyCommand cmd)
    {
        ...
        DoSomething(x, y, z)
    }
    public void DoSomething(int x, int y, int z)
    {
        ...
        // use _session
        ...
    }
}
{% endhighlight %}

can be rewritten as:
{% highlight csharp %}
        
class MyHandler: IHandlerMessages<MyCommand>
{
    ...
    public static void DoSomething(ISession session, int x, int y, int z)
    {
        ...
    }
}
{% endhighlight %}
An immediate result of this change is that the DoSomething method is freed from dependency on the MyHandler class, and it's dependency on ISession is clearly highlighted. Beyond this its new found freedom from the MyHandler class can be reimplemented as:
{% highlight csharp %}
static class ThingsToDo
{
    public static void DoSomething(ISession session, int x, int y, int z)
}
{% endhighlight %}
While the benefits for a contrived example such as this are somewhat limited, it might be a different matter if MyHandler looked more like:
{% highlight csharp %}
class MyHandler: IHandleMessages<MyCommand>
{
    private readonly ISession _session;
    private readonly ISomeFactory _factory;
    private readonly ISomeService _service;
    public MyHandler(ISession session, ISomeFactory factory, ISomeService service)
    {
        _session = session;
        _factory = factory;
        _service = service;
    }
    public void Handle(MyCommand cmd)
    {
        //... handle using _factory and _service, then:
        DoSomething(x, y, z)
    }
    public void DoSomething(int x, int y, int z)
    {
        ...
        // use _session
        ...
    }
}
{% endhighlight %}
If DoSomething is not dependent on ISomeFactory and ISomeService, there is no need to bundle it together with them in MyHandler. Practically, this has the immediate benefit of making testing cleaner and easier. This benefit occurs because the actual dependencies are more accurately represented in the code.
A further development for DoSomething may well be to refactor it to return something:
{% highlight csharp %}
static class Things
{
    public static T DoSomething(ISession session, int x, int y, int z)
}
{% endhighlight %}
This can give it the characteristic that it becomes easier to reuse in other contexts.

It is also worth pausing for a moment to consider the nature of the relationship between MyHandler and ISession. Considering the idea of an object within the object-oriented programming paradigm as state encapsulated within an object, with methods available to the object which manipulate that state, it can be seen that a message handler like MyHandler is not quite the same thing as say, a customer object in which the customer's state is encapsulated within the object and manipulated by methods on that object. MyHandler's use of constructor injection is a convenient way to use the object-oriented paradigm, typically together with an IOC library to compose the outer dependencies of a system.

With that it mind, requirements of an IOC aside, there is little difference between the first version of MyHandler above and the following, at which point there is nothing particularly object-oriented about its implementation, aside from the linguistic requirement of having a static class:
{% highlight csharp %}
static class MyHandler
{
    public static void Handle(ISession session, MyCommand cmd)
    {
        ...
        DoSomething(session, x, y, z)
    }
    public static void DoSomething(ISession session, int x, int y, int z)
    {
        ...
        // use _session
        ...
    }
}
{% endhighlight %}
# Recurrently Appearing Patterns
Certain recurrent patterns were also gaining use which pressed further in a similar direction, notably the use of event-store like designs. Event-store like designs do not necessary result in a system of immutable objects. However, the events themselves are immutable. If the events are the means of persistence then that begins to change the characteristics of the system's persistence, as nothing is edited, only new entries appended. Of course, this is not purely functional as adding new entries to something is itself mutating the something. But it does lead to thinking about the program in new ways.
Beyond the events on their own, the reconstitution of aggregates within a domain, or any other entity that could be constituted from a series of events (we used it to constitute JSON entities on a REST API) presents further food for thought.

Within an object-oriented paradigm, events may be used to reconstitute aggregates via a method such a constructor injection:
{% highlight csharp %}
public class MyAggregate
{
    public MyAggregate(IEnumerable<IEvents> myAggregateEvents)
    {
        // replay myAggregateEvents here to reformulate AggregateState
    }
    T AggregateState;
}
{% endhighlight %}
Whether by design or accident, it may not be long before an equivalent variant on the object-oriented paradigm arises:
{% highlight csharp %}
public static class MyAggregate
{
    public static T Apply(T initialState, IEvent e)
    {
        // apply e to initialState, and return the new state.
    }

    public static T Apply(IEnumerable<IEvent> eventStream)
    {
        T emptyState  ...;
        T currentState = emptyState;
        foreach (var e in eventStream)
            currentState = Apply(currentState, e)

    }
}
{% endhighlight %}

The problem is not so much that these patterns need to be learned. Given enough time and enough situations, I suspect most developers are likely to 'discover' such patterns, simply because there are circumstances in which they are advantageous, or simply a more natural implementation, over the more object-oriented approach.
# SOLID principles
Beyond the somewhat eclectic points listed above, there are more design oriented reasons for why object-oriented programming may naturally lead towards a more functional approach. In a post that reflects our own experience, Mark Seemann has noted in [SOLID: the next step is functional](http://blog.ploeh.dk/2014/03/10/solid-the-next-step-is-functional/), that consistent and thorough-going application of the SOLID principles within an object-oriented environment has the effect of turning one's programming into a more functional paradigm.

# Composition Over Inheritance
It is hardly a new observation that composition is generally to be preferred over inheritance. Inheritance is one of the strongest forms of dependency. And once one has a bit of experience with how the wonder of inheritance is not all apples and oranges, or cats and dogs, it does not take long to appreciate the wisdom of this advice. Of course, once the emphasis in code shifts towards composition, the primary object-oriented mechanisms for achieving polymorphic behaviour takes a back seat to one of the basic building blocks of functional programming.

# Conclusion
The features discussed above primarily emerged over a period of time as a change in programming style within an object-oriented programming mindset. We were aware that these features had simpler implementations, or were more naturally implemented and enforced in a functional paradigm, a fact that led us to consider using a functional programming language.
