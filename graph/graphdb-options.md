Graph Database options
============

Hosted Services
---------------

VM-based solutions
------------------

While there are no right or wrong answers around this, here are a few popular graph database engines to consider, and why you might consider them.

###Neo4j

[Neo4j](http://neo4j.com/) provides a free Community and for-pay Enterprise edition. The Community edition has a free deployment already on [Azure Marketplace](http://azure.microsoft.com/en-us/marketplace/partners/neotechnology/neo4jcommunity201onubuntu1204lts/), so testing it out should be easy. The Enterprise edition is suitable for running large-scale graphs.

* Deployments are limited to a single machine.
	* Multi-machine deployments are used only for HA clustering.
	* PB-scale data would require partitionable graphs and a custom partitioning strategy/implementation.
* Cypher query language makes it easy to operate on the graphs.
	* Server-side marshalling means queries are efficient.
	* Custom query language means vendor lock-in.

###OrientDB

[OrientDB](http://orientdb.com/orientdb/) is a similar to Neo4j with a free Community edition and a for-pay Enterprise edition. One minor difference is that the free edition is fully Open-Source under the Apache2 license (technically, Neo4j's Community edition is OSS, but under GPL so typically not enterprise-friendly). The Community edition has a free deployment already on [Azure Marketplace](http://azure.microsoft.com/en-us/marketplace/partners/orientdb/orientdb-community-edition-orientdb-community-edition-2-0-10/), so testing it should be easy.

* Supports storage of full "JSON" documents (akin to e.g. Mongo or DocDB).
* Sharded, multi-master support means support for graphs of much larger size than Neo4j.
* Query syntax is, literally, SQL.
	* Minor extensions to the language to support graph concepts.
* Supports Write-ahead Logging, coupled with ACID transactions, allowing better fault-tolerance than many NoSQL stores.
* Community edition is not "nerfed" - supports HA deployments, no limits on scale.

###Titan

[Titan](http://thinkaurelius.github.io/titan/) is an Open-Source, Apache2-licensed Graph DB, with a unique slant. It has a couple of features to recommend it, but is still relatively new and has just been acquired by DataStax - this likely means stronger long-term support, but may limit support for non-DataStax storage backbone support. There is no Azure Marketplace build for Titan, so deploying it will require more work. A simple testing instance is easy to deploy to an Azure Linux VM, though, but may not give you any indication of "real world" deployments.

* Titan is a Graph DB "layer" over a canonical store.
	* Out-of-the-box support for Cassandra, HBase, or BDBs.
	* Fault-tolerance and CAP-related guarantees depend on the underlying store.
	* Transaction layer provides flexible guarantees, from strong ACID to eventual consistency.
	* Much higher scalability than single-node Graph DBs like Neo4j since persistence layer scales independently.
* Query layer is built on standard [TinkerPop stack](http://tinkerpop.incubator.apache.org/).
	* Gremlin query language has a more procedure-oriented format, but is standard and provides good fan-out capabilities.
	* Blueprints provides a consistent REST API for all supporting DBs.
	* TinkerPop is not yet widely adopted, so while in theory it provides shelter from vendor lock-in, in practice that guarantee is less secure. Neo4j and OrientDB both claim compliance.

##Picking a Graph Store

There are no hard-and-fast rules to picking one of these over the other, but a few questions you should ask to guide yourself in the right direction.

- **Are you migrating from an existing store? Is it Key/Value or Document-based?**

    If you're migrating from an existing Document-based store, OrientDB has the benefit of allowing you to keep your existing document schema while moving it into the new data store. If you have an existing K/V store, moving to any Graph DB should be possible, or you can consider a hybrid solution as outlined in [the main GraphDB document](graphdb.md).

- **Do you require Enterprise-level support?**

    In this case, you should consider Neo4j or OrientDB, as both have a built-in Enterprise edition and support model. Titan has Enterprise-level support, and that is even stronger now with the purchase by DataStax, but they don't have a focused Enterprise-edition so are lacking Enterprise-level tooling in some areas.

- **Is your data "big data"?**

    Are your graphs Petabytes in size, with hundreds of millions of nodes and edges? If so, you should consider OrientDB or Titan - both of which can scale horizontally, in different ways. 

- **Is your workload write-heavy? Will this be your primary store?**

    If this is the case, OrientDB is a solid choice due to write-ahead logs and ACID support as well as multi-master sharded deployments. 

- **How willing are you to trade performance for fault-tolerance?**

    In some models (batch-loaded, high-read/low-write, BI applications), you might favor a design suited to high performance queries where fault-tolerance is not as important. Titan backed by Berkeley DBs can be a high-performance solution in these cases. However, before optimizing for performance, it makes sense to benchmark on your own scenarios - Neo4j _and_ OrientDB also perform quite well on _big iron_ servers.