---
layout: post
title: Aggregates and Documents
---

The most effective principle we have found in determining what should be contained in a RavenDB document is to follow the principles around DDD aggregates. An aggregate is a collection of objects, with one of the those objects as the root, which represents the smallest collection of objects that must be kept consistent at a time. Typically, an aggregate corresponds to the extent of data modified in a single transaction.

Take the following example:
{% highlight csharp %}
class Invoice
{
	public string Id {get; set;}
	public List<InvoiceLine> Lines {get; set}
	public decimal Total {get; set;}
}
class InvoiceLine
{
	public string ItemDescription {get; set;}
	public decimal Amount {get; set;}
}
{% endhighlight %}
It is likely that Invoice.Total should always be consistent with Invoice.Lines.Sum(l => l.Amount).
This should not be eventually consistent, it should be immediately consistent. Other parts of the system may be making business decisions based on Invoice.Total, and it will not do for them to be making decisions based on an incorrect total.

Consequently, Invoice will be an aggregate, and a single invoice will end up in a single document.
{% highlight csharp %}
Invoice i = new Invoice()
{
	...
	Lines = ...
}
ravenSession.Store(i);
{% endhighlight %}

Things get more interesting if we consider what happens if we consider that the Invoice belongs to a customer. There are at least three possibilities for how the invoice is related to the customer.

### Use a Customer key
{% highlight csharp %}
class Invoice
{
	public string Id {get; set;}
	public string CustomerId {get; set;}
	public List<InvoiceLine> Lines {get; set}
	public decimal Total {get; set;}
}
{% endhighlight %}

In this case, CustomerId is the document key for whatever the Customer entity is. This obviously corresponds quite closely to how the situation may be envisage if using a relational database.

### Use a Customer Value object
{% highlight csharp %}
class CustomerValueObject
{
	public string Id {get; set;}
	public string Name {get; set;}
}
class Invoice
{
	public string Id {get; set;}
	public CustomerValueObject Customer {get; set;}
	public List<InvoiceLine> Lines {get; set}
	public decimal Total {get; set;}
}
{% endhighlight %}
In this case, the CustomerValueObject is a snapshot of the Id and Name of the Customer at the time the Invoice is created. If the customer’s name changes, it would need to be determined whether it is necessary to update any of the corresponding denormalized CustomerValueObject.Name properties.
While I have suggested that the focus of a document should be on issues of mutation and consistency, one advantage of this design is that if you have a collection of Invoices and want to use them for a view model, it is not necessary to hit the database to get the customer’s name (assuming for the sake of the example you want to show a list of invoices and the name of each invoice’s customer).

Note that exactly what CustomerValueObject is, is not the important point here. The main point is that it is not an instance of Customer, but rather in most cases a subset of the Customer object. On our project, we have settled for using something like this for virtually all references:
{% highlight csharp %}
class EntityReference
{
	public string Id {get; set;}
	public string Name {get; set;}
}
{% endhighlight %}
.

## Make Customer the aggregate, including the invoices
{% highlight csharp %}
class Customer
{
	public string Id {get; set;}
	public string Name {get; set;}
	public List<Invoice> Invoices {get; set;}
}
{% endhighlight %}
This is the sort of design that I would suggest may be problematic. It’s main apparent advantage is that it is possible to hit the database once, get a single document, and display every invoice that a customer has ever had. Furthermore, if the customer’s name changes, it avoids the (potential) problem of solution #2, that a denormalized customer name becomes inconsistent. The disadvantages are:

* If there is no consistency requirements between the Customer and the customer’s invoices, then this does not represent a valid aggregate. One of the reasons this may cause problems is not immediately clear in this highly-contrived example. However, if you imagine a system where there are numerous additions and edits of invoices happening very quickly, the extent of this aggregate is going to cause problems (if we assume that only one process gets to update an aggregate at a time). Everytime any invoice for a specific customer is edited, or a new one added, the entire Customer object for that customer is effectively locked.

* If there are a lot of invoices for a customer, then the document may end up becoming unmanageably large, even a performance problem.

* If this is being done primarily because it is influence by a particular view model, then the document runs the risk of moving in the my-entity-database-behaves-more-like-a-cache direction. A document that is essentially a view model is likely to be problematic when today's view model changes tomorrow, requiring slightly different data.

An easy question to ask one-self is this: am I deciding on the document (aggregate) boundaries on the basis of consistency requirements, or on the basis of a view of the data (for example, a view model required for a page). If the former, you are likely on the right path. On the other hand, if the extent of the document is determined by the fact that there exists some page in your application that shows some amount of data and it is possible to put all that data into a document, and hence, achieve 1 page = 1 db hit, then you may want to carefully consider the disadvantages.

### A Note on Aggregates and Domain Driven Design

Aggregate is a technical term in Domain Driven Design (DDD). The definitive explanation is found in [*Eric Evans, Domain-Driven Design*][Evans]
. An excellent worked example is found in [*Vaughn Vernon, Implementing Domain-Driven Design*][Vernon], (chapter 10 on Aggregates is one of the best in the book). For a taste of Vernon’s book see his articles, [Effective Aggregate Design][VernonAggregateArticles], which were subsequently revised and turned into the Aggregates chapter in his book.

[Evans]: http://www.informit.com/store/domain-driven-design-tackling-complexity-in-the-heart-9780321125217
[Vernon]: http://www.informit.com/store/implementing-domain-driven-design-9780133039894
[VernonAggregateArticles]: http://dddcommunity.org/library/vernon_2011/

