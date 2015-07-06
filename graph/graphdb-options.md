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
	* Syntax is similar enough to SQL to make transitioning easy.
	* Custom query language means vendor lock-in.

###OrientDB


###Titan

[Titan](http://thinkaurelius.github.io/titan/) is an Open-Source, Apache2-licensed Graph DB, with a unique slant. It has a couple of features to recommend it, but is still relatively new and has just been acquired by DataStax - this likely means stronger long-term support, but may limit support for non-DataStax storage backbone support.

* Titan is a Graph DB "layer" over a canonical store.
	* Out-of-the-box support for Cassandra, HBase, or BDBs.
	* Fault-tolerance and CAP-related guarantees depend on the underlying store.
	* Transaction layer provides flexible guarantees, from strong ACID to eventual consistency.
* Query layer is built on standard [TinkerPop stack](http://tinkerpop.incubator.apache.org/).
	* Gremlin query language has a more procedure-oriented format, but is standard and provides good fan-out capabilities.
	* Blueprints provides a consistent REST API for all supporting DBs.
	* TinkerPop is not yet widely adopted, so while in theory it provides shelter from vendor lock-in, in practice that guarantee is less secure. 