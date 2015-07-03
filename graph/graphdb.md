Graph Databases
===============

**Note: This is under active development, so consider any advice in here preliminary**

###Overview

Graph DBs are purpose-built for storing and querying graph-based/network data structures. They are typically not meant to replace your default data store, but to supplement it for queries that are graphical in nature.

This isn't the Excel-like *graph*, but rather networks of connections like Facebook friends or Twitter follow-graphs, but could even be used for mapping out IT server topologies or product purchases.

###When to consider graph databases?

Some questions to consider before deciding to use a Graph DB:

- **Does your data require heavy denormalization or many `JOINs` in order to query it effectively?**

    If so, your data might benefit from being stored in a Graph DB. Typically these allow graph-based query syntaxes that make running deep join queries easy to write and efficient to execute.

- **Is your data highly-connected?**

    Even if you don't need to query using many `JOINs`, your data still might be heavily connected (lots of FKs) and could still benefit from a Graph DB. For instance, even if you can currently get by with querying on a few tables independently, you might benefit from being able to ask graph-related questions.

    For instance, consider a typical online store-front. Any SQL DB would work fine for working with the typical OLTP workload of running the business, but a Graph DB would allow questions like "who buys the same products" or "people who buy X also buy Y" easily.

###Combining graph databases with other data stores

Graph DBs can often supplement "canonical" stores quite well, in a few different ways. A couple of possible joint-deployment scenarios are listed below:

1. **Using a Graph DB as (part of) your reporting or BI layer.**

    TBD.

1. **Using Graph DB as your query engine to index into your key-value store.**

    TBD.