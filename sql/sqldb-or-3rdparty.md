SQL Guidance
============
###FAQ: Choosing Between SQL Server PaaS (Azure SQLDB) and IaaS (Virtual Machine)


**FEATURES: Is Azure SQLDB able to provide all the features you need?**

Most but not all SQL Server 2014 Transact-SQL statements are fully supported in Microsoft Azure SQL Database. Additionally, not all the features and tools that exist today for SQL Server box product are fully (or partially supported. For example, if your application requires MSDTC or SQL Agent or SSIS, then SQL Server in Azure VM is the only possible choice. For a comprehensive list of supported TSQL statements, features and tools, see the links below:

  Azure SQL Database Guidelines and Limitations
  https://msdn.microsoft.com/en-us/library/ff394102.aspx 

  Azure SQL Database Tools and Utilities Support
  https://msdn.microsoft.com/en-us/library/ee621784.aspx 

  Azure SQL Database Transact-SQL Reference
  https://msdn.microsoft.com/en-us/library/ee336281.aspx


**PERFORMANCE: Which is the transaction throughput required by your application?**

Moving a database to the Cloud, does not matter if using PaaS or IaaS, requires careful planning and workload characteristics knowledge to avoid problems. Azure SQLDB provides several levels of Service Level Objectives (SLO), each one with its own assigned resources: 

  Azure SQL Database Service Tiers and Performance Levels
  https://msdn.microsoft.com/en-us/library/azure/dn741336.aspx  

If the highest Premium Edition will not be able to satisfy application throughput requirements, SQL Server in Azure VM should be evaluated. Azure provides different sizes and hardware resources for Virtual Machines, then also in this case upper limits should be evaluated to ensure resource requirements will be satisfied:

  Sizes for Virtual Machines
  https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-size-specs


**SIZE: Which is the maximum expected size for your database?**

Currently, the maximum database size that you can have in Azure SQLDB is 500GB (Premium SKU). If you require more than that, SQL Server in Azure VM should be evaluated: on G-SERIES SKU you can allocate up to 64TB total disk space (Standard_G5). Extreme caution should be used here: allocating a big database, even if possible from a size perspective, may be not feasible from a performance and transaction throughput perspective, then appropriate tests should be done to ensure CPU, memory and disks will be good enough.

  Performance Best Practices for SQL Server in Azure Virtual Machines
  https://msdn.microsoft.com/en-us/library/azure/dn133149.aspx 


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


**SEGREGATION: Your security requirements allow an Internet visible endpoint?**

When creating an Azure SQLDB database, a public URL and IP will be exposed to the Internet, then potentially visible: even if Azure (and Azure SQLDB specifically) provides security mechanisms like firewall rules and DDoS protection, it is not possible to hide this endpoint and prevent Internet visibility. Additionally, Azure SQLDB requires TCP port 1433 for data access and this cannot be changed. On the other hand, SQL Server in Azure VM can be deployed inside a Virtual Network with no Internet endpoint exposed, only applications/services and VMs inside the same VNET will be able to access the database over private (DIP) endpoint. Azure SQLDB cannot be included in Azure Virtual Networks.

  Azure SQL Database Firewall
  https://msdn.microsoft.com/en-us/library/azure/ee621782.aspx 


**VERSIONING & CONTROL: Is your application deeply tight to a specific SQL Server version?**

Azure SQLDB is Microsoft PaaS offering for SQL Server technology, this means that Microsoft takes care of updating and patching of underlying database infrastructure. Azure SQLDB is always and constantly evolving, but you don’t have to schedule service maintenance activities. While Microsoft puts any reasonable commercial effort to guarantee that no breaking changes will be introduced, certification and management requirements may prohibit adoption to a small percentage of customers: if this is the case, and you need full control over SQL Server versioning and updating, you need to use SQL Server in Azure VM approach.


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

  Azure SQL Database Elastic Database Tools
  https://msdn.microsoft.com/en-us/library/azure/dn831885.aspx 


**CONFIGURATION: Do you require OS level or SQL Server instance level access?**

On the database box, if you need to install any additional software (monitoring agents, backup tools, service daemons, diagnostics and tracing), configure the Operating System or access SQL Server instance level properties (trace flags, sp_configure, etc.), then your only option is to use SQL Server in Azure VM. Azure SQLDB operates as PaaS, will not permit OS level or SQL instance level access.


**AUTHENTICATION: Does your application require Windows Integrated Authentication?**

If this is the case, you need to use SQL Server in Azure VM since Azure SQLDB only support SQL standard authentication based on logins and passwords.

  Windows Azure SQL Database SQL Authentication
  http://social.technet.microsoft.com/wiki/contents/articles/2693.windows-azure-sql-database-sql-authentication.aspx 


**SECURITY: Does your application require fixed IP for database access?**

In some scenarios, especially in hybrid environments where on-premise access is required, IP address white-listing is mandatory by local IT and/or Security department to permit outbound connections. Azure SQLDB public IPs can change without notice and cannot be reserved. For this reason, if your company has this security requirement, you need to use SQL Server in Azure VM and configure “Reserved IP” for your Cloud Service. This configuration will also offer the possibility to change the default TCP port for SQL Server connections (1433).

  Reserved IP addresses for Cloud Services & Virtual Machines
  http://azure.microsoft.com/blog/2014/05/14/reserved-ip-addresses 


**BACKUP: Do you need physical access to backup archive?**

By design and default, with no configuration required to activate, Azure SQLDB routinely perform database backup: weekly full, daily differential and 5-minutes transaction log backups. You can trigger restore using Azure Portal or REST API, but you will not have direct physical access to backup files. If your company requires physical access to backup files for compliance, archival or security reasons, you need to use SQL Server in Azure VMs and manually download backup files.

  Azure SQL Database Backup and Restore
  https://msdn.microsoft.com/en-us/library/azure/jj650016.aspx 

##Comparison chart

|       Topic        |     Azure SQLDB (PaaS)      |     SQL Server VM (IaaS)     |
| :----------------- | :-------------------------: | :--------------------------: |
| FEATURES           | Less features than box      | Full box product features    |
| PERFORMANCE        | Max 1000DTU in Premium Tier | Depends on VM SKU/Storage    |
| DB SIZE            | Max 500GB in Premium Tier   | 64TB on G-SERIES             |
| WORKLOAD           | Sizing by average usage     | Sizing based on peaks        |
| HIGH-AVAILABILITY  | Built-in by platform        | Manual config by AlwaysOn AG |
| FAULT HANDLING     | Need fault-handling & retry | Optional fault-h. & retry    |
| LOCALITY           | No co-location with App     | Co-located by VMs and VNETs  |
| SEGREGATION        | Internet exposed endpoint   | Internal private endpoint    |
| VERSIONING         | No control on upgrades      | Full control over DB upgrade |
| TCO                | Very low, self-managed      | High (as on-premise)         |
| ADMINISTRATION     | No full DBA required        | Full staffed DBA required    |
| MANAGEMENT         | Easy to manage many DBs     | Complex to manage many DBs   |
| SCALE OUT          | Tools& Frameworks available | No easy scale-out            |
| CONFIGURATION      | No setup customization      | Full access to OS and SQL    |
| AUTHENTICATION     | Only SQL standard auth.     | SQL standard and integrated  |
| SECURITY           | No Fixed IP available       | Fixed IP possible by VM      |
| BACKUP             | Backup files not accessible | Full control of backup files |


