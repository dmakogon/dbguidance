#Document databases

##Overview


##Comparing with key/value databases


##When to consider a document database

### Document vs key/value store

Sometimes it may seem beneficial to consider a key/value store, due to the ability to look up discrete items very efficiently. However, key/value stores tend to keep their value portion opaque; that is, querying against data outside of the key may result in inefficiencies that slow your queries down. Several features are usually built in to document stores, such as:

 - complex query operations (e.g. average/sum/min/max/join)
 - sorting and grouping data (key stores such as table storage only support a single-direction sort, and only on the aggregate key)
 - storing complex data (e.g. nested content, arrays)
 - querying on properties within the value portion, vs only querying on key (the primary differentiator between key stores and document stores)
