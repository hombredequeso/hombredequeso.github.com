---
layout: post
title: It's In The Way You (Ab)Use It
---

#{{ page.title }}#
  **(Otherwise Known As: If You Build A Cache, You Will Get A Cache)**

<p class="meta">28 November 2013</p>


Databases can be used and abused. It’s nice to use them as intended, but can be quite fun to abuse them too, if you know what you are doing.
For example, if I create a table in a relational database that looks like this:
{% highlight sql %}
CREATE TABLE [dbo].[KeyValue](
	[Id] [int] NOT NULL,
	[SerializedData] [nvarchar](255) NOT NULL
) ON [PRIMARY]
{% endhighlight %}
And write code like this:
{% highlight csharp %}
class Invoice
{
	public int Id {get; set;}
	public int CustomerId {get; set;}
	public int ItemId {get; set;}
	public DateTime Date {get; set;} 
	public string Description {get; set;}
}

{
	...
	Invoice invoice = new Invoice(){
		CustomerId = customer.Id,
		ItemId = item.Id,	
		Date = Datetime.Now,
		Description = “Test Invoice”;
		SaveSerializedInvoiceInKeyValueTable(invoice);
}
{% endhighlight %}
Aside from having some very average code I am not going to be able to take advantage of the natural features in a relational database. Searching will be, well, problematic, and joining between tables — its just not going to happen. Nothing stops me using the database in this way, but really, if I want a key-value database I may well be better off using a key-value database like Riak or Redis.

The moral of this little illustration is, of course, that whatever the type of database being used, if you build a key-value store, you will end up with something that looks and behaves like a key-value store. If you build a cache, you will get a cache.

What is less clear is that while this may happen by deliberate design, it may also happen unintentionally, simply because the database is not being used as its design intended.

To help understand how document databases are typically intended to be used, a comparison between key-value databases and document databases may be useful. Both types are databases can be broadly classes together as aggregate based databases (Here, I am drawing on terminology from Sadalage and Fowler's book [NoSQL Distilled][NoSQLDistilled]. This means that both types of databases use a key to lookup some aggregate value, potentially stored as JSON as is often the case. It is in the differences between the two types that an indication as to how a document database should be used becomes more apparent, and here I quote:

"The two models differ in that in a key-value database, the aggregate is opaque to the database -- just some big blob of mostly meaningless bits. In contrast, a document database is able to see a structure in the aggregate. ... A document database imposes limits on what we can place in it, defining allowable structures and types. In return, however, we get more flexibility in access."
[NoSQL Distilled, p. 20][NoSQLDistilled]

Practially, the means by which the structure of data in a document database is taken advantage of is by some means of grouping together like documents, which in RavenDB (and MongoDB) is called collections. Documents in a collection have the same, or a similar enough structure that indexing of some sort can be performed, and they can be combined with other documents, potentially from other collections, in predictable ways, allowing for putting documents together (the closest idea to a relational database's join you are likely to find) and querying.

So, if you are using RavenDB and the documents in a collection start to look like a blob of meaningless data, you might find that you are accidently implementing a key-value database in a document database. It may, in fact, look like little more than a cache. In practice, if you are using the C# client to interact with RavenDB then you are unlikely to fall into this trap. However, it is also possible that if your aggregate (persisted document) is too big that you may find the document database starts to feel more like a key-value store.

On our project, in order to get the best results out of RavenDB, we have found it best to keep to the principle that a document typically corresponds to an aggregate (the mininum amount of data that must be kept in sync at any given time -- essentially the Domain Driven Design definition, see [Evans][Evans] and [Vernon][Vernon]). This is not necessarily the principle that everyone using RavenDB is following, nor is it necessarily a rule/law we always follow (I used the word 'principle' for a reason). However, for our program, a freight forwarding application following well defined business processes, it has served us well.

[NoSQLDistilled]: http://martinfowler.com/books/nosql.html
[Evans]: http://www.informit.com/store/domain-driven-design-tackling-complexity-in-the-heart-9780321125217
[Vernon]: http://www.informit.com/store/implementing-domain-driven-design-9780133039894
