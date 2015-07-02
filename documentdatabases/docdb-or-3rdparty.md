Document Databases
==========================
Choosing between DocumentDB and 3rd-party Document Stores
---------------------

*Disclaimer - this is a work in progress. Just my ramblings right now.*

Azure offers DocumentDB, a native document database engine as-a-service. The purpose of this document is to help guide you toward a decision between the two although, ultimately, this is *only guidance* - you'll need to make the call.

**Note: This guidance is still under development**

###Questions to think about:

 - **Are you ok with managing the database engine, or are you looking specifically for a service-based database engine?** Database engine management/monitoring/support is not something to be taken lightly.

    Several 3rd-party hosters provide document database services (for example, MongoLab provides MongoDB hosting). However, there are going to be limits to capabilities and storage limits which would not be limited in your own hosted database deployment.

    DocumentDB is built specifically to be a managed database engine.

 - **Is your solution cloud-only or hybrid?** If your app will live solely in Azure, DocumentDB is a viable option for you, especially if you're looking for a managed service. Today, DocumentDB is available in five regions, and this will expand.

    If you're building a hybrid application that requires both in-the-cloud and on-premises database footprints, and you need the same engine running both in Azure and on-prem, then you'll want to focus on a 3rd-party database engine. Today, DocumentDB is an Azure-only offering.

- **Do you have an existing 3rd-party document database deployment?** If your application is already using a particular document database engine, you'll have a smooth transition to Azure, in that database engines are deployable to Azure in the same way you'd deploy one on-premises. Note: This assumes your database engine of choice is compatible with the current selection of supported Linux and Windows Virtual Machine images for Azure.

   A more important question is whether you're using features specific to a 3rd-party document database engine. If your app is just using core CRUD operations, and not using features specific to, say, MongoDB's Aggregation Framework, you might find it straightforward to migrate your data to DocumentDB, and letting you shift to a service-based document engine, rather than a VM-based engine.

- **Do you have specific performance targets?** Performance requirements can lead to very specific Virtual Machine specs and Storage specs, to achieve a given performance target (typically using high-performance VMs optimized for attached durable SSD storage). By contrast, DocumentDB manages performance by with selectable (and changeable) performance tiers per partition. From purely a scale perspective, DocumentDB provides a straightforward scale model, which may be controlled either through the portal or programmatically (including command-line script options).

   Typically, a VM-based document database node, to handle more transactions, would first be scaled *up* in hardware size and disk performance, with eventual scale *out* when individual node size limits are reached. With DocumentDB, scale *up* is also the first approach, by choosing one of the three available Request Unit (RU) tiers, which range from 250 to 2500 RU/sec. Once this is exceeded, the model is to scale *out* with additional partitions, and then shard data across the partitions. Sharding today is automatically handled in DocumentDB's .NET driver.Document Databases
