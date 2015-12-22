SQL Guidance
============
###FAQ: Choosing Between SQL Server PaaS (Azure SQLDB) and IaaS (Virtual Machine)


**FEATURES: Is Azure SQLDB able to provide all the features you need?**

Most but not all SQL Server 2014 Transact-SQL statements are fully supported in Microsoft Azure SQL Database. Additionally, not all the features and tools that exist today for SQL Server box product are fully (or partially supported. For example, if your application requires MSDTC or SQL Agent or SSIS, then SQL Server in Azure VM is the only possible choice. For a comprehensive list of supported TSQL statements, features and tools, see the links below:

  Azure SQL Database General Limitations and Guidelines
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-general-limitations 

  Azure SQL Database Transact-SQL information
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-transact-sql-information


**PERFORMANCE: Which is the transaction throughput required by your application?**

Moving a database to the Cloud, does not matter if using PaaS or IaaS, requires careful planning and workload characteristics knowledge to avoid problems. Azure SQLDB provides several levels of Service Level Objectives (SLO), each one with its own assigned resources: 

  SQL Database options and performance: Understand what's available in each service tier
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-service-tiers  

If the highest Premium Edition will not be able to satisfy application throughput requirements, SQL Server in Azure VM should be evaluated. Azure provides different sizes and hardware resources for Virtual Machines, then also in this case upper limits should be evaluated to ensure resource requirements will be satisfied:

  Sizes for Virtual Machines
  https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-size-specs


**SIZE: Which is the maximum expected size for your database?**

Currently, the maximum database size that you can have in Azure SQLDB is 1TB (Premium P11 SKU). If you require more than that, SQL Server in Azure VM should be evaluated: on G-SERIES SKU you can allocate up to 64TB total disk space (Standard_G5). Extreme caution should be used here: allocating a big database, even if possible from a size perspective, may be not feasible from a performance and transaction throughput perspective, then appropriate tests should be done to ensure CPU, memory and disks will be good enough.

  Performance best practices for SQL Server in Azure Virtual Machines
  https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-sql-server-performance-best-practices 


**WORKLOAD: Is your application workload constant over time?**

If you chose to install SQL Server in Azure VM, you will need probably to size it for peak workload: if your application workload presents frequent peaks and steady usage at different time, you may be forced to overcommit resource allocation in term of number of cores (then memory) and storage. VM SKU and size changes in Azure Virtual Machines are possible, but there are some limitations and it is essentially an offline operation. Vice-versa, Azure SQLDB provides a very efficient built-in mechanism to dynamically change SKU, then allocated resources, very quickly and completely on-line with no application downtime. If your workload is dynamic and un-predictable by nature, Azure SQLDB is the best solution.

  Changing Database Service Tiers and Performance Levels
  https://msdn.microsoft.com/en-us/library/azure/dn369872.aspx


**HIGH-AVAILABILITY: Which is your application high-availability SLA?**

Azure SQLDB, in its V12 versions, is able to provide 99,99% high-availability SLA. Using SQL Server in Azure VM will require provisioning of at least two virtual machines in the same availability set and additional configuration of AlwaysOn Availability Group feature: this configuration will be able to provide only 99,95% HA SLA.

  General Availability: Azure SQL Database Basic, Standard, and Premium service tiers
  http://azure.microsoft.com/en-us/updates/general-availability-sql-database-basic-standard-and-premium-service-tiers


**FAULT HANDLING: Your application code include retry logic and transient fault handling?**

Including proper retry logic and transient fault handling remediation in the code should be a universal best practice, both on-premised and in the cloud, either IaaS or PaaS. If this characteristic is missing, application problems may raise on both Azure SQLDB and SQL Server in Azure VM, but in this scenario the latter is recommended over the former.

  The Transient Fault Handling Application Block
  https://msdn.microsoft.com/en-us/library/hh680934(v=pandp.50).aspx 


**LOCALITY: Your application is very chatty and requires close proximity to databases?**

If network latency between application and database is critical for the performance, SQL Server in Azure VM should be used: deploying both components in the same Virtual Network and same datacenter will guarantee minimum latency. Additionally, in some extreme situations, it is also possible to install the application on the same SQL Server VM. Azure SQLDB may require additional network hops and proximity is not guaranteed. 

  Azure Network Latency & SQL Server Optimization
  http://blogs.msdn.com/b/igorpag/archive/2013/12/15/azure-network-latency-test-and-sql-server-optimization.aspx

**SEGREGATION: Your security requirements allow an Internet visible endpoint?**

When creating an Azure SQLDB database, a public URL and IP will be exposed to the Internet, then potentially visible: even if Azure (and Azure SQLDB specifically) provides security mechanisms like firewall rules and DDoS protection, it is not possible to hide this endpoint and prevent Internet visibility. Additionally, Azure SQLDB requires TCP port 1433 for data access and this cannot be changed. On the other hand, SQL Server in Azure VM can be deployed inside a Virtual Network with no Internet endpoint exposed, only applications/services and VMs inside the same VNET will be able to access the database over private (DIP) endpoint or using Azure ILB (see below). Azure SQLDB cannot be included (yet) in Azure Virtual Networks.

  Azure SQL Database Firewall
  https://msdn.microsoft.com/en-us/library/azure/ee621782.aspx 
  
  SQL Server 2014 High-Availability and Multi-Datacenter Disaster Recovery with Multiple Azure ILBs
  http://blogs.msdn.com/b/igorpag/archive/2014/12/22/sql-2014-high-availability-and-multi-datacenter-disaster-recovery-with-multiple-azure-ilbs.aspx 


**VERSIONING & CONTROL: Is your application deeply tight to a specific SQL Server version?**

Azure SQLDB is Microsoft PaaS offering for SQL Server technology, this means that Microsoft takes care of updating and patching of underlying database infrastructure. Azure SQLDB is always and constantly evolving, but you don’t have to schedule service maintenance activities. While Microsoft puts any reasonable commercial effort to guarantee that no breaking changes will be introduced, certification and management requirements may prohibit adoption to a small percentage of customers: if this is the case, and you need full control over SQL Server versioning and updating, you need to use SQL Server in Azure VM approach. Even if you don’t have control over Azure SQLDB versions, you can change the compatibility level of your database and be sure Azure SQLDB will adhere to that specifications:

  ALTER DATABASE Compatibility Level (Transact-SQL)
  https://msdn.microsoft.com/en-us/library/bb510680.aspx

  Compatibility level 130 for Azure SQL Database V12
  https://azure.microsoft.com/it-it/updates/compatibility-level-130-for-azure-sql-database-v12

Actually, compatibility level supported in Azure SQLDB V12 are 100 (SQL Server 2008 and SQL Server 2008 R2), 110 (SQL Server 2012), 120 (SQL Server 2014), or 130 (SQL Server 2016 Community Technology Preview 3), that is all the currently supported SQL Server box versions, in addition to SQL Server 2016 CTP3 that is currently in preview.

**TCO: Did you evaluate the full cost of both SQL Server PaaS and IaaS alternatives?**

If one of these options will satisfy all your requirements, which one you should use? Determining precise cost of Azure SQLDB and SQL Server in Azure VM is not difficult, proper pricing tooling and web pages exist, obviously you will chose the least expensive. What is more difficult is how to make an equal cost comparison between PaaS and IaaS offering, that’s why you should read the article below before making any maths on cost comparison:

  Azure SQLDB and SQL Server VM - How to make an equal cost comparison
  http://blogs.msdn.com/b/igorpag/archive/2015/03/20/azure-sqldb-and-sql-server-vm-how-to-make-an-equal-cost-comparison.aspx 


**ADMINISTRATION: Do you have full-staffed DBA resources?**

SQL Database offers the scale and functionality of an enterprise data center without the administrative overhead that is associated with on-premise or Virtual Machine instances of SQL Server. This self-managing capability will greatly reduce DBA requirements removing the overhead on managing, monitoring and maintaining database infrastructure. SQL Database also manages load balancing and, in case of a server failure, transparent fail-over to a healthy machine hosting one of the backup copies of your database with no human intervention. If you don’t have a DBA, Azure SQLDB should be the preferred choice.


**MANAGEMENT: How many database entities you will have to manage?**

If your application architecture and requirements ask for a non-trivial number of databases to manage, you should definitely look at Azure SQLDB PaaS option. SQL Server declined in its IaaS form, will still require DBA, additionally if you have many databases to manage, even with support of tooling and automation, this will require more IT resources. Additionally, Azure SQLDB recently introduced new technologies and capabilities to manage a large number of database at scale:

  Elastic Database Jobs overview
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-jobs-overview 


**SCALE OUT: Do you need the possibility to scale out dynamically on many databases?**

SQL Server in Azure VM provide limited scale-up capabilities, essentially dictated by Azure VM SKU and sizes available. Additionally, if your application adopts a tenancy model based on database-level tenant isolation, you may need to create a large number of databases. In addition to administrative problems, application architectural changes must be also considered. If you want to dynamically scale out your database set and at the same time provide minimal impact on your code, you should evaluate Azure SQLDB option and some recent new features introduced "Elastic Database Client library", "Elastic Database Jobs" and "Split-Merge tool".

  Elastic Database features overview
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-scale-introduction   


**CONFIGURATION: Do you require OS level or SQL Server instance level access?**

On the database box, if you need to install any additional software (monitoring agents, backup tools, service daemons, diagnostics and tracing), configure the Operating System or access SQL Server instance level properties (trace flags, sp_configure, etc.), then your only option is to use SQL Server in Azure VM. Azure SQLDB operates as PaaS, will not permit OS level or SQL instance level access.


**AUTHENTICATION: Does your application require Windows Integrated Authentication?**

If this is the case, you need to use SQL Server in Azure VM since Azure SQLDB only support SQL standard authentication based on logins and passwords.

  Windows Azure SQL Database SQL Authentication
  http://social.technet.microsoft.com/wiki/contents/articles/2693.windows-azure-sql-database-sql-authentication.aspx 

NOTE: Azure SQLDB supporting Azure Active Directory authentication is currently in preview. Even if able to support “integrated” authentication mechanism, it requires proper infrastructure to be in place and only  supports the .NET Framework Data Provider for SqlServer (at least version .NET Framework 4.6).

  Connecting to SQL Database by Using Azure Active Directory Authentication
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-aad-authentication


**SECURITY: Does your application require fixed IP for database access?**

In some scenarios, especially in hybrid environments where on-premise access is required, IP address white-listing is mandatory by local IT and/or Security department to permit outbound connections. Azure SQLDB public IPs can change without notice and cannot be reserved. For this reason, if your company has this security requirement, you need to use SQL Server in Azure VM and configure “Reserved IP” for your Cloud Service. This configuration will also offer the possibility to change the default TCP port for SQL Server connections (1433).

  Reserved IP addresses for Cloud Services & Virtual Machines
  http://azure.microsoft.com/blog/2014/05/14/reserved-ip-addresses 


**BACKUP: Do you need physical access to backup archive?**

By design and default, with no configuration required to activate, Azure SQLDB routinely perform database backup: weekly full, daily differential and 5-minutes transaction log backups. You can trigger restore using Azure Portal or REST API, but you will not have direct physical access to backup files. If your company requires physical access to backup files for compliance, archival or security reasons, you need to use SQL Server in Azure VMs and manually download backup files.

  Business Continuity Overview
  https://azure.microsoft.com/en-us/documentation/articles/sql-database-business-continuity

Another point to consider is the retention period of saved backups: in Azure SQLDB, at the moment, automatic backups created by the infrastructure have a maximum retention period of 35 days for Point-in-Time-Restore (PITR). If you need a longer period, you should use SQL Server installed in Azure Virtual Machines.


**HYBRID: You need to integrate with on-premise AlwaysOn Availability Group (AG) topology?**

If you have existing SQL Server on-premise installation using AlwaysOn Availability Groups (AG) feature for high-availability and cross-datacenter disaster-recovery, a SQL Server instance installed inside Azure Virtual Machines can join this existing topology as an additional replica. This is not possible for Azure SQLDB. Additionally, if you want to use SQL Server “Log Shipping” DR feature, same restrictions apply.

  High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines
  https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions

NOTE: Although Azure SQLDB can participate as a “Subscriber”, in a “Transactional Replication” topology with on-premise SQL Server instances or Virtual Machines in the Cloud, this is not considered HA/DR mechanism.
 
  Transactional Replication to Azure SQL DB now in public preview
  https://azure.microsoft.com/it-it/blog/transactional-replication-to-azure-sql-db 

**CROSS-DB ACCESS: You need to access multiple databases at the same time?**

In Azure SQLDB, currently is not possible to change database context once the connection is established, USE statement is not allowed to point to another database in the same connection. It is also not possible to create “Linked Servers” pointing to other Azure SQLDB databases nor using 4-parts name syntax to point external DBs. Finally, distributed transactions using Distributed Transaction Coordinator (DTC) for 2-phase commit protocol is now allowed.


##Comparison chart

|       Topic        |          Azure SQLDB (PaaS)             |           SQL Server VM (IaaS)               |
| :----------------- | :-------------------------------------: | :------------------------------------------: |
| FEATURES           | Less features than box                  | Full box product features                    |
| PERFORMANCE        | Max 1750DTU in Premium Tier             | Depends on VM SKU/Storage                    |
| DB SIZE            | Max 1TB in Premium P11                  | 64TB on G-SERIES                             |
| WORKLOAD           | Sizing by average usage                 | Sizing based on peaks                        |
| HIGH-AVAILABILITY  | Built-in by platform                    | Manual config by AlwaysOn AG                 |
| FAULT HANDLING     | Need fault-handling & retry             | Optional fault-h. & retry                    |
| LOCALITY           | No co-location with App                 | Co-located by VMs and VNETs                  |
| SEGREGATION        | Internet exposed endpoint               | Internal private endpoint                    |
| VERSIONING         | No control on upgrades                  | Full control over DB upgrade                 |
| TCO                | Very low, self-managed                  | High (as on-premise)                         |
| ADMINISTRATION     | No full DBA required                    | Full staffed DBA required                    |
| MANAGEMENT         | Easy to manage many DBs                 | Complex to manage many DBs                   |
| SCALE OUT          | Tools& Frameworks available             | No easy scale-out                            |
| CONFIGURATION      | No setup customization                  | Full access to OS and SQL                    |
| AUTHENTICATION     | Only SQL standard auth.                 | SQL standard and integrated                  |
| SECURITY           | No Fixed IP fixed 1433 port             | Fixed IP, port can be change                 |
| BACKUP             | Files not accessible, 35 days PITR      | Full control of backup files, unlimited PITR |
| HYBRID             | No AlwaysOn AG support                  | Can join on-premise AlwaysOn AG topology     |
| CROSS-DB ACCESS    | NO: DTC, Linked Srv, USE, 4-parts names | YES: DTC, Linked Srv, USE, 4-parts names     |



