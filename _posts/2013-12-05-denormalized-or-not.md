---
layout: post
title: Denormalized Or Not?
---

#{{ page.title }}#

<p class="meta">5 December 2013</p>

It is worth considering futher the nature of the following code, discussed briefly (as option 2) in the previous post:
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
Furthermore, let us assume the existence of another entity, the Customer:
{% highlight csharp %}
class Customer
{
	public string Id {get; set;}
	public string Name {get; set;}
	...
}
{% endhighlight %}
Invoice.Customer.Name is set to the name of the customer at the time the invoice is created. If the customer’s name is changed for any reason (correction of clerical entry error, name change by deed poll, etc), then Invoice.Customer.Name will be different to the Customer.Name. The question at this point is what the nature of consistency should be between these two fields. Are they consistent within themselves, permissibly inconsistent, or should they be eventually consistent?

Technically, the simplest solution is that Invoice.Customer.Name should never change its value. It does not represent the current name of customer.Id. Rather, it represents the name of customer.Id at the time the invoice was generated. It is the name that appears printed on the piece of paper/pdf/email. As such, it should never change. It is consistent within its own context, namely that of the Invoice, and it is quite acceptable for it to be different to TheCustomerAggregate.Name.

Alternatively, if the Invoice.Customer.Name represent the current name of the customer in the system, then it is clearly inconsistent. Maybe it should be eventually consistent, in which case it becomes necessary to write code to update the denormalized references to TheCustomerAggregate.Name. This is the sort of updating of denormalized data that is quite correctly associated with increasing the probability of defects.

The various issues raised regarding the Invoice.Customer.Name field are easily muddied when the answers to two different questions become merged together:

* What does this field represent?

* What is this field going to be used for?

Too often, it seems there is a desire to update Invoice.Customer.Name so that the Invoice aggregate/document can be used as part of a view model. This runs two concerns together, domain operations (which can mutate an aggregate, the command side) and read operations (the query side). In RavenDB we have found it easiest to delegate to read operations to an index, and not compel the aggregate to serve dual Command and Query purposes. Sometimes, it turns out that the name on Invoice.Customer.Name is the one required to be used in a view model (or data export, or whatever), and sometimes Customer.Name is the one required. Either way, it is best not to project the needs of what is likely to be many view models onto the data stored as part of the aggregate.

 In the vast majority of cases, by carefully defining what a field means (usually as “the value of something at the time that an operation took place"), along with the the fact that very many entities in a system rarely if ever change (for example, currency codes, port codes) our solution is entirely devoid of the need to update denormalized fields (going by memory here, there might be a case somewhere by I can’t recall it). While it is not necessarily going to be the case for all domains, much of the time in business type applications, data which can be viewed as denormalized is more appropriately understood as values at the time of some operation. Consequently, the problems sometimes envisaged with having to update denormalized data simply do not arise, because the data is not, in fact, denormalized.

