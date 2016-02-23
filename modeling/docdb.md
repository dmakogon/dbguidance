#Modeling data in a document database

##Embedding vs referencing

Document databases seem a natural fit for embedded content.
For example, imagine a blog post with comments:

```
{
  id: 1,
  title: "Modeling document data",
  body: "...",
  comments: [ {...},{...}...]
}
```
While this has a natural feel to it (embedding comments within a blog post),
it might have issues in terms of document size limits, and the need to update the
array of comments often, while rarely updating the content body.

So with these types of issues in mind, let's walk through some general thoughts on embedding vs referencing.

##Embedding

Embedding content might be a good fit in the following cases.

###Data from entities are queried together.

Imagine how you retrieve a book for your e-reader. Typically you grab the entire
book, not just the table of contents, or just a cover without the cover art.

###The child data is a dependent of the parent data
Picture an order line, as part of a larger order. You would typically retrieve
all order lines along with the general order information, since separate line items
don't have much use on their own. For example:

```
{
  id: "order1",
  customer: "customer1",
  orderDate: "2014-09-15T23:14:25.7251173Z"
  lines: [
    {product: "13inch screen" , price: 200.00, qty: 50 },
    {product: "Keyboard", price:23.67, qty:4},
    {product: "CPU", price:87.89, qty:1}
  ]      
}
```

###1-to-1 relationships

If, say, you had a single shipping address on your user info document, it makes sense to store these items together.

```
{
  id: 1,
  name: "David"
  shipAddress: {
    address: "1 Main St",
    City: "Washington",
    State: "DC",
    ZipCode: "11111"
  } 
}
```

###Similar volatility

With the abovementioned example with a single shipping address, chances are, the user
info and the shipping address only change infrequently. It's fairly rare that a
person changes their name or shipping location. So these two items share similar
volatility. in contrast: A user's current heartrat, for example, would have a very
different volatility (maybe once per second?).

###The set of sub-documents is _bounded_

Back to our example of storing a shipping address as a sub-document: Even if a user had several shipping addresses, chances are, it's just a handful. And something that's reasonable to limit in the app (e.g. max 5 shipping addresses).

##Referencing

Now let's consider something more familiar to relational database developers: _Referencing_ data. In a document store, this means storing references to other document ID's within a document. Unlike a relational store, there are no constructs in place to guarantee referential integrity, so this needs to be handled in the app itself.

Let's walk through some common scenarios where referencing might be the better choice over embedding.

###One-to-many relationships (unbounded)

Remember our blog post example? In the case of blog+comments, we could have potentially thousands (or millions) of comments. It's pretty much guaranteed that, eventually, we will reach the maximum document size limit. And then... our app is broken.

Further: Depending on the database engine used, it might become bandwidth-heavy to update large data arrays (especially if the entire document must be retrieved, modified, and re-sent back to the database engine).

So, this is a great case for referencing. For example:

```
{
  id: 1,
  type: "blog",
  title: "Modeling document data",
  body: "..."
},
{commentId:1,name:"Ryan",type:"comment",comment":"Best post!"},
{commentId:1,name:"Sandy",type:"comment",comment":"Brilliant!"},
...
```
