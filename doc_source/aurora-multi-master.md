# Working with Aurora Multi\-Master Clusters<a name="aurora-multi-master"></a>

 Following, you can learn about Aurora multi\-master clusters\. In a multi\-master cluster, all DB instances have read/write capability\. Multi\-master clusters have different availability characteristics, support for database features, and procedures for monitoring and troubleshooting than single\-master clusters\. 

**Topics**
+ [Overview of Aurora Multi\-Master Clusters](#aurora-multi-master-overview)
+ [Creating an Aurora Multi\-Master Cluster](#aurora-multi-master-creating)
+ [Managing Aurora Multi\-Master Clusters](#aurora-multi-master-managing)
+ [Application Considerations for Aurora Multi\-Master Clusters](#aurora-multi-master-application)
+ [Performance Considerations for Aurora Multi\-Master Clusters](#aurora-multi-master-performance)
+ [Approaches to Aurora Multi\-Master Clusters](#aurora-multi-master-tutorial)

## Overview of Aurora Multi\-Master Clusters<a name="aurora-multi-master-overview"></a>

 Use the following background information to help you choose a multi\-master or single\-master cluster when you set up a new Aurora cluster\. For you to make an informed choice, we recommend that you first understand how you plan to adapt your schema design and application logic to work best with a multi\-master cluster\. 

 For each new Amazon Aurora cluster, you can choose whether to create a single\-master or multi\-master cluster\. 

 Most kinds of Aurora clusters are *single\-master* clusters\. For example, provisioned, Aurora Serverless, parallel query, and Global Database clusters are all single\-master clusters\. In a single\-master cluster, a single DB instance performs all write operations and any other DB instances are read\-only\. If the writer DB instance becomes unavailable, a failover mechanism promotes one of the read\-only instances to be the new writer\. 

 In a *multi\-master cluster*, all DB instances can perform write operations\. The notions of a single read/write primary instance and multiple read\-only Aurora Replicas don't apply\. There isn't any failover when a writer DB instance becomes unavailable, because another writer DB instance is immediately available to take over the work of the failed instance\. We refer to this type of availability as *continuous availability*, to distinguish it from the high availability \(with brief downtime during failover\) offered by a single\-master cluster\. 

 Multi\-master clusters work differently in many ways from the other kinds of Aurora clusters, such as provisioned, Aurora Serverless, and parallel query clusters\. With multi\-master clusters, you consider different factors in areas such as high availability, monitoring, connection management, and database features\. For example, in applications where you can't afford even brief downtime for database write operations, a multi\-master cluster can help to avoid an outage when a writer instance becomes unavailable\. The multi\-master cluster doesn't use the failover mechanism, because it doesn't need to promote another DB instance to have read/write capability\. With a multi\-master cluster, you examine metrics related to DML throughput, latency, and deadlocks for all DB instances instead of a single primary instance\. 

 Currently, multi\-master clusters require Aurora MySQL version 1, which is compatible with MySQL 5\.6\. When specifying the DB engine version in the AWS Management Console, AWS CLI, or RDS API, choose `5.6.10a`\. 

 To create a multi\-master cluster, you choose **Multiple writers** under **Database features** when creating the cluster\. Doing so enables different behavior for replication among DB instances, availability, and performance than other kinds of Aurora clusters\. This choice remains in effect for the life of the cluster\. Make sure that you understand the specialized use cases that are appropriate for multi\-master clusters\. 

**Topics**
+ [Multi\-Master Cluster Terminology](#aurora-multi-master-terms)
+ [Multi\-Master Cluster Architecture](#aurora-multi-master-architecture)
+ [Recommended Workloads for Multi\-Master Clusters](#aurora-multi-master-workloads)
+ [Advantages of Multi\-Master Clusters](#aurora-multi-master-advantages)
+ [Limitations of Multi\-Master Clusters](#aurora-multi-master-limitations)

### Multi\-Master Cluster Terminology<a name="aurora-multi-master-terms"></a>

 You can understand the terminology about multi\-master clusters by learning the following definitions\. These terms are used throughout the documentation for multi\-master clusters\. 

**Writer**  
 A DB instance that can perform write operations\. In an Aurora multi\-master cluster, all DB instances are writers\. This is a significant difference from Aurora single\-master clusters, where only one DB instance can act as a writer\. With a single\-master cluster, if the writer becomes unavailable, the failover mechanism promotes another DB instance to become the new writer\. With a multi\-master cluster, your application can redirect write operations from the failed DB instance to any other DB instance in the cluster\. 

**Multi\-master**  
 An architecture for Aurora clusters where each DB instance can perform both read and write operations\. Contrast this with *single\-master*\. Multi\-master clusters are best suited for segmented workloads, such as for multitenant applications\. 

**Single\-master**  
 The default architecture for Aurora clusters\. A single DB instance \(the primary instance\) performs writes\. All other DB instances \(the Aurora Replicas\) handle read\-only query traffic\. Contrast this with *multi\-master*\. This architecture is appropriate for general\-purpose applications\. In such applications, a single DB instance can handle all the data manipulation language \(DML\) and data definition language \(DDL\) statements\. Scalability issues mostly involve `SELECT` queries\. 

**Write conflict**  
 A situation that occurs when different DB instances attempt to modify the same data page at the same time\. Aurora reports a write conflict to your application as a deadlock error\. This error condition causes the transaction to roll back\. Your application must detect the error code and retry the transaction\.   
 The main design consideration and performance tuning goal with Aurora multi\-master clusters is to divide your write operations between DB instances in a way that minimizes write conflicts\. That is why multi\-master clusters are well\-suited for sharded applications\. For details about the write conflict mechanism, see [Conflict Resolution for Multi\-Master Clusters](#aurora-multi-master-conflicts)\. 

**Sharding**  
 A particular class of segmented workloads\. The data is physically divided into many partitions, tables, databases, or even separate clusters\. The containers for specific portions of the data are known as *shards*\. In an Aurora multi\-master cluster, each shard is managed by a specific DB instance, and a DB instance can be responsible for multiple shards\. A sharded schema design maps well to the way you manage connections in an Aurora multi\-master cluster\. 

**Shard**  
 The unit of granularity within a sharded deployment\. It might be a table, a set of related tables, a database, a partition, or even an entire cluster\. With Aurora multi\-master clusters, you can consolidate the data for a sharded application into a single Aurora shared storage volume, making the database continuously available and the data easy to manage\. You decide which shards are managed by each DB instance\. You can change this mapping at any time, without physically reorganizing the data\. 

**Resharding**  
 Physically reorganizing sharded data so that different DB instances can handle specific tables or databases\. You don't need to physically reorganize data inside Aurora multi\-master clusters in response to changing workload or DB instance failures\. You can avoid resharding operations because all DB instances in a cluster can access all databases and tables through the shared storage volume\. 

**Multitenant**  
 A particular class of segmented workloads\. The data for each customer, client, or user is kept in a separate table or database\. This design ensures isolation and helps you to manage capacity and resources at the level of individual users\. 

**Bring\-your\-own\-shard \(BYOS\)**  
 A situation where you already have a database schema and associated applications that use sharding\. You can transfer such deployments relatively easily to Aurora multi\-master clusters\. In this case, you can devote your effort to investigating the Aurora benefits such as server consolidation and high availability\. You don't need to create new application logic to handle multiple connections for write requests\. 

**Global read\-after\-write \(GRAW\)**  
 A setting that introduces synchronization so that any read operations always see the most current state of the data\. By default, the data seen by a read operation in a multi\-master cluster is subject to replication lag, typically a few milliseconds\. During this brief interval, a query on one DB instance might retrieve stale data if the same data is modified at the same time by a different DB instance\. To enable this setting, change `aurora_mm_session_consistency_level` from its default setting of `INSTANCE_RAW` to `REGIONAL_RAW`\. Doing so ensures cluster\-wide consistency for read operations regardless of the DB instances that perform the reads and writes\. For details on GRAW mode, see [Consistency Model for Multi\-Master Clusters](#aurora-multi-master-consistency)\. 

### Multi\-Master Cluster Architecture<a name="aurora-multi-master-architecture"></a>

 Multi\-master clusters have a different architecture than other kinds of Aurora clusters\. In multi\-master clusters, all DB instances have read/write capability\. Other kinds of Aurora clusters have a single dedicated DB instance that performs all write operations, while all other DB instances are read\-only and handle only `SELECT` queries\. Multi\-master clusters don't have a primary instance or read\-only Aurora Replicas\. 

 Your application controls which write requests are handled by which DB instance\. Thus, with a multi\-master cluster, you connect to individual instance endpoints to issue DML and DDL statements\. That's different than other kinds of Aurora clusters, where you typically direct all write operations to the single cluster endpoint and all read operations to the single reader endpoint\. 

 The underlying storage for Aurora multi\-master clusters is similar to storage for single\-master clusters\. Your data is still stored in a highly reliable, shared storage volume that grows automatically\. The core difference lies in the number and type of DB instances\. In multi\-master clusters, there are *N* read/write nodes\. Currently, the maximum for *N* is 2\. 

 Multi\-master clusters have no dedicated read\-only nodes\. Thus, the Aurora procedures and guidelines about Aurora Replicas don't apply to multi\-master clusters\. You can temporarily make a DB instance read\-only to place read and write workloads on different DB instances\. To do so, see [Using Instance Read\-Only Mode](#using-instance-read-only-mode)\. 

 Multi\-master cluster nodes are connected using low\-latency and low\-lag Aurora replication\. Multi\-master clusters use all\-to\-all peer\-to\-peer replication\. Replication works directly between writers\. Every writer replicates its changes to all other writers\. 

 DB instances in a multi\-master cluster handle restart and recovery independently\. If a writer restarts, there is no requirement for other writers to also restart\. For details, see [High Availability Considerations for Aurora Multi\-Master Clusters](#aurora-multi-master-HA)\. 

 Multi\-master clusters keep track of all changes to data within all database instances\. The unit of measurement is the data page, which has a fixed size of 16 KB\. These changes include modifications to table data, secondary indexes, and system tables\. Changes can also result from Aurora internal housekeeping tasks\. Aurora ensures consistency between the multiple physical copies that Aurora keeps for each data page in the shared storage volume, and in memory on the DB instances\. 

 If two DB instances attempt to modify the same data page at almost the same instant, a write conflict occurs\. The earliest change request is approved using a quorum voting mechanism\. That change is saved to permanent storage\. The DB instance whose change isn't approved rolls back the entire transaction containing the attempted change\. Rolling back the transaction ensures that data is kept in a consistent state, and applications always see a predictable view of the data\. Your application can detect the deadlock condition and retry the entire transaction\. 

 For details about how to minimize write conflicts and associated performance overhead, see [Conflict Resolution for Multi\-Master Clusters](#aurora-multi-master-conflicts)\. 

### Recommended Workloads for Multi\-Master Clusters<a name="aurora-multi-master-workloads"></a>

 Multi\-master clusters work best with certain kinds of workloads\. 

#### Active\-Passive Workloads<a name="aurora-multi-master-workloads-active-passive"></a>

 With an *active\-passive* workload, you perform all read and write operations on one DB instance at a time\. You hold any other DB instances in the Aurora cluster in reserve\. If the original active DB instance becomes unavailable, you immediately switch all read and write operations to the other DB instance\. With this configuration, you minimize any downtime for write operations\. The other DB instance can take over all processing for your application without performing a failover\. 

#### Active\-Active Workloads<a name="aurora-multi-master-workloads-active-active"></a>

 With an *active\-active* workload, you perform read and write operations to all the DB instances at the same time\. In this configuration, you typically segment the workload so that the different DB instances don't modify the same underlying data at the same time\. Doing so minimizes the chance for write conflicts\. 

 Multi\-master clusters work well with application logic that's designed for a *segmented workload*\. In this type of workload, you divide write operations by database instance, database, table, or table partition\. For example, you can run multiple applications on the same cluster, each assigned to a specific DB instance\. Alternatively, you can run an application that uses multiple small tables, such as one table for each user of an online service\. Ideally, you design your schema so that write operations for different DB instances don't perform simultaneous updates to overlapping rows within the same tables\. Sharded applications are one example of this kind of architecture\. 

 For examples of designs for active\-active workloads, see [Using a Multi\-Master Cluster for a Sharded Database](#aurora-multi-master-sharding)\. 

### Advantages of Multi\-Master Clusters<a name="aurora-multi-master-advantages"></a>

 You can take advantage of the following benefits with Aurora multi\-master clusters: 
+  Multi\-master clusters improve Aurora's already high availability\. You can restart a read/write DB instance without causing other DB instances in the cluster to restart\. There is no failover process and associated delay when a read/write DB instance becomes unavailable\. 
+  Multi\-master clusters are well\-suited to sharded or multitenant applications\. As you manage the data, you can avoid complex resharding operations\. You might be able to consolidate sharded applications with a smaller number of clusters or DB instances\. For details, see [Using a Multi\-Master Cluster for a Sharded Database](#aurora-multi-master-sharding)\. 
+  Aurora detects write conflicts immediately, not when the transaction commits\. For details about the write conflict mechanism, see [Conflict Resolution for Multi\-Master Clusters](#aurora-multi-master-conflicts)\. 

### Limitations of Multi\-Master Clusters<a name="aurora-multi-master-limitations"></a>

**Note**  
 Aurora multi\-master clusters are highly specialized for continuous availability use cases\. Thus, such clusters might not be generally applicable to all workloads\. Your requirements for performance, scalability, and availability might be satisfied by using a larger DB instance class with an Aurora single\-master cluster\. If so, consider using a provisioned or Aurora Serverless cluster\. 

#### AWS and Aurora Limitations<a name="aurora-multi-master-limitations-aws"></a>

 The following limitations currently apply to the AWS and Aurora features that you can use with multi\-master clusters: 
+  Currently, you can have a maximum of two DB instances in a multi\-master cluster\. 
+  Currently, all DB instances in a multi\-master cluster must be in the same AWS Region\. 
+  You can't enable cross\-Region replicas from multi\-master clusters\. 
+ Multi\-master clusters are available in the following AWS Regions:
  + US East \(N\. Virginia\) Region
  + US East \(Ohio\) Region
  + US West \(Oregon\) Region
  + Asia Pacific \(Mumbai\) Region
  + Asia Pacific \(Seoul\) Region
  + Asia Pacific \(Tokyo\) Region
  + Europe \(Frankfurt\) Region
  + Europe \(Ireland\) Region
+  The `Stop` action isn't available for multi\-master clusters\. 
+  The Aurora survivable page cache, also known as the survivable buffer pool, isn't supported for multi\-master clusters\. 
+  A multi\-master cluster doesn't do any load balancing for connections\. Your application must implement its own connection management logic to distribute read and write operations among multiple DB instance endpoints\. Typically, in a bring\-your\-own\-shard \(BYOS\) application, you already have logic to map each shard to a specific connection\. To learn how to adapt the connection management logic in your application, see [Connection Management for Multi\-Master Clusters](#aurora-multi-master-connectivity)\. 
+  Multi\-master clusters have some processing and network overhead for coordination between DB instances\. This overhead has the following consequences for write\-intensive and read\-intensive applications: 
  +  Throughput benefits are most obvious on busy clusters with multiple concurrent write operations\. In many cases, a traditional Aurora cluster with a single primary instance can handle the write traffic for a cluster\. In these cases, the benefits of multi\-master clusters are mostly for high availability rather than performance\. 
  +  Single\-query performance is generally lower than for an equivalent single\-master cluster\. 
+  You can't take a snapshot created on a single\-master cluster and restore it on a multi\-master cluster, or the opposite\. Instead, to transfer all data from one kind of cluster to the other, use a logical dump produced by a tool such as AWS Database Migration Service \(AWS DMS\) or the mysqldump command\. 
+  You can't use the parallel query, Aurora Serverless, or Global Database features on a multi\-master cluster\. 

   The multi\-master aspect is a permanent choice for a cluster\. You can't switch an existing Aurora cluster between a multi\-master cluster and another kind such as Aurora Serverless or parallel query\. 
+  The zero\-downtime patching \(ZDP\) and zero\-downtime restart \(ZDR\) features aren't available for multi\-master clusters\. 
+  Integration with other AWS services such as AWS Lambda, Amazon S3, and AWS Identity and Access Management isn't available for multi\-master clusters\. 
+  The Performance Insights feature isn't available for multi\-master clusters\. 
+  You can't clone a multi\-master cluster\. 
+  You can't enable the backtrack feature for multi\-master clusters\. 

#### Database Engine Limitations<a name="aurora-multi-master-limitations-engine"></a>

 The following limitations apply to the database engine features that you can use with a multi\-master cluster: 
+  You can't perform binary log \(binlog\) replication to or from a multi\-master cluster\. This limitation means you also can't use global transaction ID \(GTID\) replication in a multi\-master cluster\. 
+  The event scheduler isn't available for multi\-master clusters\. 
+  The hash join optimization isn't enabled on multi\-master clusters\. 
+  The query cache isn't available on multi\-master clusters\. 
+  You can't use certain SQL language features on multi\-master clusters\. For the full list of SQL differences, and instructions about adapting your SQL code to address these limitations, see [SQL Considerations for Multi\-Master Clusters](#aurora-multi-master-SQL)\. 

## Creating an Aurora Multi\-Master Cluster<a name="aurora-multi-master-creating"></a>

 You choose the multi\-master or single\-master architecture at the time you create an Aurora cluster\. The following procedures show where to make the multi\-master choice\. If you haven't created any Aurora clusters before, you can learn the general procedure in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

### Console<a name="aurora-multi-master.console"></a>

 To create an Aurora multi\-master cluster from the AWS Management Console, you make the following choices\. On the first screen, you select an Aurora cluster: 

![\[Creating an Aurora multi-master cluster: choosing Aurora database engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-multi-master-create-database-01.png)

 You also choose MySQL 5\.6 compatibility and location **Regional**: 

![\[Creating an Aurora multi-master cluster: choosing Aurora MySQL compatible with MySQL 5.6\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-multi-master-create-database-02.png)

 On the second screen, choose **Multiple writers** under **Database features**\. 

![\[Creating an Aurora multi-master cluster: choosing Multiple Writers\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-multi-master-create-database-03.png)

 Fill in the other settings for the cluster\. This part of the procedure is the same as the general procedure for creating an Aurora cluster in [Creating a DB Cluster](Aurora.CreateInstance.md#Aurora.CreateInstance.Creating)\. 

 After you create the multi\-master cluster, add two DB instances to it by following the procedure in [Adding Aurora Replicas to a DB Cluster](aurora-replicas-adding.md)\. Use the same AWS instance class for all DB instances within the multi\-master cluster\. 

 After you create the multi\-master cluster and associated DB instances, you see the cluster in the AWS Management Console **Databases** page as follows\. All DB instances show the role **Writer**\. 

![\[Databases page from RDS console showing a multi-master cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-multi-master-cluster-list-01.png)

### AWS CLI<a name="aurora-multi-master-creating.cli"></a>

 To create a multi\-master cluster with the AWS CLI, run the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command and include the option `--engine_mode=multimaster`\. 

 The following command shows the syntax for creating an Aurora cluster with multi\-master replication\. For the general procedure to create an Aurora cluster, see [Creating a DB Cluster](Aurora.CreateInstance.md#Aurora.CreateInstance.Creating)\. 

For Linux, macOS, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora \
     --engine-version 5.6.10a --master-username user-name --master-user-password password \
     --db-subnet-group-name my_subnet_group --vpc-security-group-ids my_vpc_id \
     --engine-mode multimaster
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora ^
     --engine-version 5.6.10a --master-username user-name --master-user-password password ^
     --db-subnet-group-name my_subnet_group --vpc-security-group-ids my_vpc_id ^
     --engine-mode multimaster
```

 After you create the multi\-master cluster, add a second DB instance to it by following the procedure in [Adding Aurora Replicas to a DB Cluster](aurora-replicas-adding.md)\. Use the same AWS instance class for all DB instances within the multi\-master cluster\. 

### RDS API<a name="aurora-multi-master-creating.api"></a>

 To create a multi\-master cluster with the RDS API, run the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation\. Specify the value `multimaster` for the `EngineMode` parameter\. For the general procedure to create an Aurora cluster, see [Creating a DB Cluster](Aurora.CreateInstance.md#Aurora.CreateInstance.Creating)\. 

 After you create the multi\-master cluster, add two DB instances to it by following the procedure in [Adding Aurora Replicas to a DB Cluster](aurora-replicas-adding.md)\. Use the same AWS instance class for all DB instances within the multi\-master cluster\. 

### Adding a DB Instance to a Multi\-Master Cluster<a name="aurora-multi-master-add-instance"></a>

 You need more than one DB instance to see the benefits of a multi\-master cluster\.  You can create another DB instance afterward using the procedures from [Adding Aurora Replicas to a DB Cluster](aurora-replicas-adding.md)\. The difference for multi\-master clusters is that the new DB instance has read/write capability instead of a read\-only Aurora Replica\. Use the same AWS instance class for all DB instances within the multi\-master cluster\. 

## Managing Aurora Multi\-Master Clusters<a name="aurora-multi-master-managing"></a>

 You do most management and administration for Aurora multi\-master clusters the same way as for other kinds of Aurora clusters\. The following sections explain the differences and unique features of multi\-master clusters for administration and management\. 

**Topics**
+ [Monitoring an Aurora Multi\-Master Cluster](#aurora-multi-master-monitoring)
+ [Data Ingestion Performance for Multi\-Master Clusters](#aurora-multi-master-ETL)
+ [Exporting Data from a Multi\-Master Cluster](#aurora-multi-master-exporting)
+ [High Availability Considerations for Aurora Multi\-Master Clusters](#aurora-multi-master-HA)
+ [Replication Between Multi\-Master Clusters and Other Clusters](#aurora-multi-master-replication)
+ [Upgrading a Multi\-Master Cluster](#aurora-multi-master-upgrades)

### Monitoring an Aurora Multi\-Master Cluster<a name="aurora-multi-master-monitoring"></a>

 Most of the monitoring and diagnostic features supported by MySQL and Aurora single\-master clusters are also supported for multi\-master clusters: 
+  MySQL error logs, general logs and slow query logs\. 
+  MySQL built\-in diagnostic features such as SHOW commands, status variables, InnoDB runtime status tables, and so on\. 
+  MySQL Performance Schema\. 
+  Advanced Auditing\. 
+  CloudWatch metrics\. 
+  Enhanced Monitoring\. 

 Aurora multi\-master clusters don't currently support the following monitoring features: 
+  Performance Insights\. 

### Data Ingestion Performance for Multi\-Master Clusters<a name="aurora-multi-master-ETL"></a>

 One best practice for DML operations on a multi\-master cluster is to keep transactions small and brief\. Also, route write operations for a particular table or database to a specific DB instance\. Doing a bulk import might require relaxing the guidance for transaction size\. However, you can still distribute the write operations to minimize the chance of write conflicts\. 

**To distribute the write workload from a bulk import**

1.  Issue a separate `mysqldump` command for each database, table, or other object in your schema\. Store the results of each `mysqldump` in a file whose name reflects the object being dumped\. As an alternative, you can use a specialized dump and import tool that can automatically dump multiple tables in parallel, such as `mydumper`\. 

1.  Run a separate `mysql` session for each data file, connecting to the appropriate instance endpoint that handles the corresponding schema object\. Again, as an alternative, you can use a specialized parallel import command, such as `myloader`\. 

1.  Run the import sessions in parallel across the DB instances in the multi\-master cluster, instead of waiting for each to finish before starting the next\. 

 You can use the following techniques to import data into an Aurora multi\-master cluster: 
+  You can import logical \(SQL\-format\) dumps from other MySQL\-compatible servers to Aurora multi\-master clusters, if the statements don't use any features that aren't supported in Aurora\. For example, a logical dump from a table containing MySQL Full\-Text Search \(FTS\) indexes doesn't work because the FTS feature is not supported on multi\-master clusters\. 
+  You can use managed services such as DMS to migrate data into an Aurora multi\-master cluster\. 
+  For migrations into an Aurora multi\-master cluster from a server that isn't compatible with MySQL, follow existing instructions for heterogeneous Aurora migrations\. 
+  Aurora multi\-master clusters can produce MySQL\-compatible logical dumps in SQL format\. Any migration tool \(for example, AWS DMS\) that can understand such format can consume data dumps from Aurora multi\-master clusters\. 
+  Aurora doesn't support binary logging with the multi\-master cluster as the binlog master or worker\. You can't use binlog\-based CDC tools with multi\-master clusters\. 
+  When migrating from non\-MySQL\-compatible servers, you can replicate into a multi\-master cluster using the continuous change data capture \(CDC\) feature of AWS DMS\. That type of replication transmits SQL statements to the destination cluster, thus the restriction on binlog replication doesn't apply\. 

 For a detailed discussion of migration techniques and recommendations, see the [Amazon Aurora Migration Handbook](https://d0.awsstatic.com/whitepapers/Migration/amazon-aurora-migration-handbook.pdf) AWS whitepaper\. Some of the migration methods listed in the handbook might not apply to Aurora multi\-master clusters, but the document is a great overall source of knowledge about Aurora migration topics\. 

### Exporting Data from a Multi\-Master Cluster<a name="aurora-multi-master-exporting"></a>

 You can save a snapshot of a multi\-master cluster and restore it to another multi\-master cluster\. Currently, you can't restore a multi\-master cluster snapshot into a single\-master cluster\. 

 To migrate data from a multi\-master cluster to a single\-master cluster, use a logical dump and restore with a tool such as `mysqldump`\. 

 You can't use a multi\-master cluster as the source or destination for binary log replication\. 

### High Availability Considerations for Aurora Multi\-Master Clusters<a name="aurora-multi-master-HA"></a>

 In an Aurora multi\-master cluster, any DB instance can restart without causing any other instance to restart\. This behavior provides a higher level of availability for read/write and read\-only connections than for Aurora single\-master clusters\. We refer to this availability level as *continuous availability*\. In multi\-master clusters, there is no downtime for write availability when a writer DB instance fails\. Multi\-master clusters don't use the failover mechanism, because all cluster instances are writable\. If a DB instance fails in a multi\-master cluster, your application can redirect the workload towards the remaining healthy instances\. 

 In a single\-master cluster, restarting the primary instance makes write operations unavailable until the failover mechanism promotes a new primary instance\. Read\-only operations also experience a brief downtime because all the Aurora Replicas in the cluster restart\. 

 To minimize downtime for applications in a multi\-master cluster, implement frequent SQL\-level health checks\. If a DB instance in a multi\-master cluster becomes unavailable, you can decide what to do based on the expected length of the outage and the urgency of write operations in the workload\. If you expect the outage to be brief and the write operations aren't urgent, you can wait for the DB instance to recover before resuming the workload that is normally handled by that DB instance\. Alternatively, you can redirect that workload to a different DB instance\. The underlying data remains available at all time to all DB instances in the cluster\. The highly distributed Aurora storage volume keeps the data continuously available even in the unlikely event of a failure affecting an entire AZ\. For information about the timing considerations for switching write operations away from an unavailable DB instance, see [Using a Multi\-Master Cluster as an Active Standby](#aurora-multi-master-active-standby)\. 

### Replication Between Multi\-Master Clusters and Other Clusters<a name="aurora-multi-master-replication"></a>

 Multi\-master clusters don't support incoming or outgoing binary log replication\. 

### Upgrading a Multi\-Master Cluster<a name="aurora-multi-master-upgrades"></a>

 Aurora multi\-master clusters use the same version numbering scheme, with major and minor version numbers, as other kinds of Aurora clusters\. However, the **Enable auto minor version upgrade** setting doesn't apply for multi\-master clusters\. 

 When you upgrade an Aurora multi\-master cluster, typically the upgrade procedure moves the database engine from the current version to the next higher version\. If you upgrade to an Aurora version that increments the version number by more than one, the upgrade uses a multi\-step approach\. Each DB instance is upgraded to the next higher version, then the next one after that, and so on until it reaches the specified upgrade version\. 

 The approach is different depending on whether there are any backwards\-incompatible changes between the old and new versions\. For example, updates to the system schema are considered backwards\-incompatible changes\. You can check whether a specific version contains any backwards\-incompatible changes by consulting the release notes\. 

 If there aren't any incompatible changes between the old and new versions, each DB instance is upgraded and restarted individually\. The upgrades are staggered so that the overall cluster doesn't experience any downtime\. At least one DB instance is available at any time during the upgrade process\. 

 If there are incompatible changes between the old and new versions, Aurora performs the upgrade in offline mode\. All cluster nodes are upgraded and restarted at the same time\. The cluster experiences some downtime, to avoid an older engine writing to newer system tables\. 

 Zero\-downtime patching \(ZDP\) isn't currently supported for Aurora multi\-master clusters\. 

## Application Considerations for Aurora Multi\-Master Clusters<a name="aurora-multi-master-application"></a>

 Following, you can learn any changes that might be required in your applications due to differences in feature support or behavior between multi\-master and single\-master clusters\. 

**Topics**
+ [SQL Considerations for Multi\-Master Clusters](#aurora-multi-master-SQL)
+ [Connection Management for Multi\-Master Clusters](#aurora-multi-master-connectivity)
+ [Consistency Model for Multi\-Master Clusters](#aurora-multi-master-consistency)
+ [Multi\-Master Clusters and Transactions](#aurora-multi-master-transactions)
+ [Write Conflicts and Deadlocks in Multi\-Master Clusters](#aurora-multi-master-deadlocks)
+ [Multi\-Master Clusters and Locking Reads](#aurora-multi-master-locking-reads)
+ [Performing DDL Operations on a Multi\-Master Cluster](#aurora-multi-master-DDL)
+ [Using Autoincrement Columns](#using-auto-increment-columns)
+ [Multi\-Master Clusters Feature Reference](#multi-master-feature-reference)

### SQL Considerations for Multi\-Master Clusters<a name="aurora-multi-master-SQL"></a>

 The following are the major limitations that apply to the SQL language features you can use with a multi\-master cluster: 
+  In a multi\-master cluster, you can't use certain settings or column types that change the row layout\. You can't enable the `innodb_large_prefix` configuration option\. You can't use the column types `MEDIUMTEXT`, `MEDIUMBLOB`, `LONGTEXT`, or `LONGBLOB`\. 
+  You can't use the `CASCADE` clause with any foreign key columns in a multi\-master cluster\. 
+  Multi\-master clusters can't contain any tables with full\-text search \(FTS\) indexes\. Such tables can't be created on or imported into multi\-master clusters\. 
+  DDL works differently on multi\-master and single\-master clusters\. For example, the fast DDL mechanism isn't available for multi\-master clusters\. You can't write to a table in a multi\-master cluster while the table is undergoing DDL\. For full details on DDL differences, see [Performing DDL Operations on a Multi\-Master Cluster](#aurora-multi-master-DDL)\. 
+  You can't use the `SERIALIZABLE` transaction isolation level on multi\-master clusters\. On Aurora single\-master clusters, you can use this isolation level on the primary instance\. 
+  Autoincrement columns are handled using the `auto_increment_increment` and `auto_increment_offset` parameters\. Parameter values are predetermined and not configurable\. The parameter `auto_increment_increment` is set to 16, which is the maximum number of instances in any Aurora cluster\. However, multi\-master clusters currently have a lower limit on the number of DB instances\. For details, see [Using Autoincrement Columns](#using-auto-increment-columns)\. 

 When adapting an application for an Aurora multi\-master cluster, approach that activity the same as a migration\. You might have to stop using certain SQL features, and change your application logic for other SQL features: 
+  In your `CREATE TABLE` statements, change any columns defined as `MEDIUMTEXT`, `MEDIUMBLOB`, `LONGTEXT`, or `LONGBLOB` to shorter types that don't require off\-page storage\. 
+  In your `CREATE TABLE` statements, remove the `CASCADE` clause from any foreign key declarations\. Add application logic if necessary to emulate the `CASCADE` effects through `INSERT` or `DELETE` statements\. 
+  Remove any use of InnoDB fulltext search \(FTS\) indexes\. Check your source code for `MATCH()` operators in `SELECT` statements, and `FULLTEXT` keywords in DDL statements\. Check if any table names from the `INFORMATION_SCHEMA.INNODB_SYS_TABLES` system table contain the string `FTS_`\. 
+  Check the frequency of DDL operations such as `CREATE TABLE` and `DROP TABLE` in your application\. Because DDL operations have more overhead in multi\-master clusters, avoid running many small DDL statements\. For example, look for opportunities to create needed tables ahead of time\. For information about DDL differences with multi\-master clusters, see [Performing DDL Operations on a Multi\-Master Cluster](#aurora-multi-master-DDL)\. 
+  Examine your use of autoincrement columns\. The sequences of values for autoincrement columns are different for multi\-master clusters than other kinds of Aurora clusters\. Check for the `AUTO_INCREMENT` keyword in DDL statements, the function name `last_insert_id()` in `SELECT` statements, and the name `innodb_autoinc_lock_mode` in your custom configuration settings\. For details about the differences and how to handle them, see [Using Autoincrement Columns](#using-auto-increment-columns)\. 
+  Check your code for the `SERIALIZABLE` keyword\. You can't use this transaction isolation level with a multi\-master cluster\. 

### Connection Management for Multi\-Master Clusters<a name="aurora-multi-master-connectivity"></a>

 The main connectivity consideration for multi\-master clusters is the number and type of the available DNS endpoints\. With multi\-master clusters, you often use the instance endpoints, which you rarely use in other kinds of Aurora clusters\. 

 Aurora multi\-master clusters have the following kinds of endpoints: 

**Cluster endpoint**  
 This type of endpoint always points to a DB instance with read/write capability\. Each multi\-master cluster has one cluster endpoint\.   
 Because applications in multi\-master clusters typically include logic to manage connections to specific DB instances, you rarely need to use this endpoint\. It's mostly useful for connecting to a multi\-master cluster to perform administration\.   
 You can also connect to this endpoint to examine the cluster topology when you don't know the status of the DB instances in the cluster\. To learn that procedure, see [Describing Cluster Topology](#describing-cluster-topology)\. 

**DB instance endpoint**  
 This type of endpoint connects to specific named DB instances\. For Aurora multi\-master clusters, your application typically uses the DB instance endpoints for all or nearly all connections\. You decide which DB instance to use for each SQL statement based on the mapping between your shards and the DB instances in the cluster\. Each DB instance has one such endpoint\. Thus the multi\-master cluster has one or more of these endpoints, and the number changes as DB instances are added to or removed from a multi\-master cluster\.   
 The way you use DB instance endpoints is different between single\-master and multi\-master clusters\. For single\-master clusters, you typically don't use this endpoint often\. 

**Custom endpoint**  
 This type of endpoint is optional\. You can create one or more custom endpoints to group together DB instances for a specific purpose\. When you connect to the endpoint, Aurora returns the IP address of a different DB instance each time\. In multi\-master clusters, you typically use custom endpoints to designate a set of DB instances to use mostly for read operations\. We recommend not using custom endpoints with multi\-master clusters to load\-balance write operations, because doing so increases the chance of write conflicts\. 

 Multi\-master clusters don't have reader endpoints\. Where practical, issue `SELECT` queries using the same DB instance endpoint that normally writes to the same table\. Doing so makes more effective use of cached data from the buffer pool, and avoids potential issues with stale data due to replication lag within the cluster\. If you don't locate `SELECT` statements on the same DB instances that write to the same tables, and you require strict read after write guarantee for certain queries, consider running those queries using the global read\-after\-write \(GRAW\) mechanism described in [Consistency Model for Multi\-Master Clusters](#aurora-multi-master-consistency)\. 

 For general best practices of Aurora and MySQL connection management, see the [Amazon Aurora Migration Handbook](https://d0.awsstatic.com/whitepapers/Migration/amazon-aurora-migration-handbook.pdf) AWS whitepaper\. 

 For information about how to emulate read\-only DB instances in multi\-master DB clusters, see [Using Instance Read\-Only Mode](#using-instance-read-only-mode)\. 

 Follow these guidelines when creating custom DNS endpoints and designing drivers and connectors for Aurora multi\-master clusters: 
+  For DDL, DML, and DCL statements, don't use endpoints or connection routing techniques that operate in round\-robin or random fashion\. 
+  Avoid long\-running write queries and long write transactions unless these transactions are guaranteed not to conflict with other write traffic in the cluster\. 
+  Prefer to use autocommitted transactions\. Where practical, avoid `autocommit=0` settings at global or session level\. When you use a database connector or database framework for your programming language, check that `autocommit` is turned on for applications that use the connector or framework\. If needed, add `COMMIT` statements at logical points throughout your code to ensure that transactions are brief\. 
+  When global read consistency or read\-after\-write guarantee is required, follow recommendations for global read\-after\-write \(GRAW\) described in [Consistency Model for Multi\-Master Clusters](#aurora-multi-master-consistency)\. 
+  Use the cluster endpoint for DDL and DCL statements where practical\. The cluster endpoint helps to minimize the dependency on the hostnames of the individual DB instances\. You don't need to divide DDL and DCL statements by table or database, as you do with DML statements\. 

### Consistency Model for Multi\-Master Clusters<a name="aurora-multi-master-consistency"></a>

 Aurora multi\-master clusters support a global read\-after\-write \(GRAW\) mode that is configurable at the session level\. This setting introduces extra synchronization to create a consistent read view for each query\. That way, queries always see the very latest data\. By default, the replication lag in a multi\-master cluster means that a DB instance might see old data for a few milliseconds after the data was updated\. Enable this feature if your application depends on queries seeing the latest data changes made by any other DB instance, even if the query has to wait as a result\. 

**Note**  
 Replication lag doesn't affect your query results if you write and then read the data using the same DB instance\. Thus, the GRAW feature applies mainly to applications that issue multiple concurrent write operations through different DB instances\. 

 When using the GRAW mode, don't enable it for all queries by default\. Globally consistent reads are noticeably slower than local reads\. Therefore, use GRAW selectively for queries that require it\. 

 Be aware of these considerations for using GRAW: 
+  GRAW involves performance overhead due to the cost of establishing a cluster\-wide consistent read view\. The transaction must first determine a cluster\-wide consistent point in time, then replication must catch up to that time\. The total delay depends on the workload, but it's typically in the range of tens of milliseconds\. 
+  You can't change GRAW mode within a transaction\. 
+  When using GRAW without explicit transactions, each individual query incurs the performance overhead of establishing a globally consistent read view\. 
+  With GRAW enabled, the performance penalty applies to both reads and writes\. 
+  When you use GRAW with explicit transactions, the overhead of establishing a globally consistent view applies once for each transaction, when the transaction starts\. Queries performed later in the transaction are as fast as if run without GRAW\. If multiple successive statements can all use the same read view, you can wrap them in a single transaction for a better overall performance\. That way, the penalty is only paid once per transaction instead of per query\. 

### Multi\-Master Clusters and Transactions<a name="aurora-multi-master-transactions"></a>

 Standard Aurora MySQL guidance applies to Aurora multi\-master clusters\. The Aurora MySQL database engine is optimized for short\-lived SQL statements\. These are the types of statements typically associated with online transaction processing \(OLTP\) applications\. 

 In particular, make your write transactions as short as possible\. Doing so reduces the window of opportunity for write conflicts\. The conflict resolution mechanism is *optimistic*, meaning that it performs best when write conflicts are rare\. The tradeoff is that when conflicts occur, they incur substantial overhead\. 

 There are certain workloads that benefit from large transactions\. For example, bulk data imports are significantly faster when run using multi\-megabyte transactions rather than single\-statement transactions\. If you observe an unacceptable number of conflicts while running such workloads, consider the following options: 
+  Reduce transaction size\. 
+  Reschedule or rearrange batch jobs so that they don't overlap and don't provoke conflicts with other workloads\. If practical, reschedule the batch jobs so that they run during off\-peak hours\. 
+  Refactor the batch jobs so that they run on the same writer instance as the other transactions causing conflicts\. When conflicting transactions are run on the same instance, the transactional engine manages access to the rows\. In that case, storage\-level write conflicts don't occur\. 

### Write Conflicts and Deadlocks in Multi\-Master Clusters<a name="aurora-multi-master-deadlocks"></a>

 One important performance aspect for multi\-master clusters is the frequency of write conflicts\. When such a problem condition occurs in the Aurora storage subsystem, your application receives a deadlock error and performs the usual error handling for deadlock conditions\. Aurora uses a lock\-free optimistic algorithm that performs best when such conflicts are rare\. 

 In a multi\-master cluster, all the DB instances can write to the shared storage volume\. For every data page you modify, Aurora automatically distributes several copies across multiple Availability Zones \(AZs\)\. A write conflict can occur when multiple DB instances try to modify the same data page within a very short time\. The Aurora storage subsystem detects that the changes overlap and performs conflict resolution before finalizing the write operation\. 

 Aurora detects write conflicts at the level of the physical data pages, which have a fixed size of 16 KiB\. Thus, a conflict can occur even for changes that affect different rows, if the rows are both within the same data page\. 

 When conflicts do occur, the cleanup operation requires extra work to undo the changes from one of the DB instances\. From the point of view of your application, the transaction that caused the conflict encounters a deadlock and Aurora rolls back that whole transaction\. Your application receives error code 1213\. 

 Undoing the transaction might require modifying many other data pages whose changes were already applied to the Aurora storage subsystem\. Depending on how much data was changed by the transaction, undoing it might involve substantial overhead\. Therefore, minimizing the potential for write conflicts is a crucial design consideration for an Aurora multi\-master cluster\. 

 Some conflicts result from changes that you initiate\. These changes include SQL statements, transactions, and transaction rollbacks\. You can minimize these kinds of conflicts through your schema design and the connection management logic in your application\. 

 Other conflicts happen because of simultaneous changes from both a SQL statement and an internal server thread\. These conflicts are hard to predict because they depend on internal server activity that you might not be aware of\. The two major kinds of internal activity that cause these conflicts are garbage collection \(known as *purge*\), and transaction rollbacks performed automatically by Aurora\. For example, Aurora performs rollbacks automatically during crash recovery or if a client connection is lost\. 

 A transaction rollback physically reverts page changes that were already made\. A rollback produces page changes just like the original transaction does\. A rollback takes time, potentially several times as long as the original transaction\. While the rollback is proceeding, the changes it produces can come into conflict with your transactions\. 

 Garbage collection has to do with multi\-version concurrency control \(MVCC\), which is the concurrency control method used by the Aurora MySQL transactional engine\. With MVCC, data mutations create new row versions, and the database keeps multiple versions of rows to achieve transaction isolation while permitting concurrent access to data\. Row versions are removed \(*purged*\) when they're no longer needed\. Here again, the process of purging produces page changes, which might conflict with your transactions\. Depending on the workload, the database can develop a *purge lag*: a queue of changes waiting to be garbage collected\. If the lag grows substantially, the database might need a considerable amount of time to complete the purge, even if you stop submitting SQL statements\. 

 If an internal server thread encounters a write conflict, Aurora retries automatically\. In contrast, your application must handle the retry logic for any transactions that encounter conflicts\. 

 When multiple transactions from the same DB instance cause these kinds of overlapping changes, Aurora uses the standard transaction concurrency rules\. For example, if two transactions on the same DB instance modify the same row, one of them waits\. If the wait is longer than the configured timeout \(`innodb_lock_wait_timeout`, by default 50 seconds\), the waiting transaction aborts with a "Lock wait timeout exceeded" message\. 

### Multi\-Master Clusters and Locking Reads<a name="aurora-multi-master-locking-reads"></a>

 Aurora multi\-master clusters support locking reads in the following forms\. 

```
      SELECT ... FOR UPDATE
      SELECT ... LOCK IN SHARE MODE
```

 For more information about locking reads, see the [MySQL Reference Manual](https://dev.mysql.com/doc/refman/5.6/en/innodb-locking-reads.html)\. 

 Locking read operations are supported on all nodes, but the lock scope is local to the node on which the command was run\. A locking read performed on one writer doesn't prevent other writers from accessing or modifying the locked rows\. Despite this limitation, you can still work with locking reads in use cases that guarantee strict workload scope separation between writers, such as in sharded or multitenant databases\. 

 Consider the following guidelines: 
+  Remember that a node can always see its own changes immediately and without delay\. When possible, you can colocate reads and writes on the same node to eliminate the GRAW requirement\. 
+  If read\-only queries must be run with globally consistent results, use GRAW\. 
+  If read\-only queries care about data visibility but not global consistency, use GRAW or introduce a timed wait before each read\. For example, a single application thread might maintain connections C1 and C2 to two different nodes\. The application writes on C1 and reads on C2\. In such case, the application can issue a read immediately using GRAW, or it can sleep before issuing a read\. The sleep time should be equal to or longer than the replication lag \(usually approximately 20–30 ms\)\. 

 The read\-after\-write feature is controlled using the `aurora_mm_session_consistency_level` session variable\. The valid values are `INSTANCE_RAW` for local consistency mode \(default\) and `REGIONAL_RAW` for cluster\-wide consistency: 

### Performing DDL Operations on a Multi\-Master Cluster<a name="aurora-multi-master-DDL"></a>

 The SQL data definition language \(DDL\) statements have special considerations for multi\-master clusters\. These statements sometimes cause substantial reorganization of the underlying data\. Such large\-scale changes potentially affect many data pages in the shared storage volume\. The definitions of tables and other schema objects are held in the `INFORMATION_SCHEMA` tables\. Aurora handles changes to those tables specially to avoid write conflicts when multiple DB instances run DDL statements at the same time\. 

 For DDL statements, Aurora automatically delegates the statement processing to a special server process in the cluster\. Because Aurora centralizes the changes to the `INFORMATION_SCHEMA` tables, this mechanism avoids the potential for write conflicts between DDL statements\. 

 DDL operations prevent concurrent writes to that table\. During a DDL operation on a table, all DB instances in the multi\-master cluster are limited to read\-only access to that table until the DDL statement finishes\. 

 The following DDL behaviors are the same in Aurora single\-master and multi\-master clusters: 
+  A DDL performed on one DB instance causes other instances to terminate any connections actively using the table\. 
+  Session\-level temporary tables can be created on any node using the `MyISAM` or `MEMORY` storage engines\. 
+  DDL operations on very large tables might fail if the DB instance doesn't have sufficient local temporary storage\. 

 Note the following DDL performance considerations in multi\-master clusters: 
+  Try to avoid issuing large numbers of short DDL statements in your application\. Create databases, tables, partitions, columns, and so on, in advance where practical\. Replication overhead can impose significant performance overhead for simple DDL statements that are typically very quick\. The statement doesn't finish until the changes are replicated to all DB instances in the cluster\. For example, multi\-master clusters take longer than other Aurora clusters to create empty tables, drop tables, or drop schemas containing many tables\. 

   If you do need to perform a large set of DDL operations, you can reduce the network and coordination overhead by issuing the statements in parallel through multiple threads\. 
+  Long\-running DDL statements are less affected, because the replication delay is only a small fraction of the total time for the DDL statement\. 
+  Performance of DDLs on session\-level temporary tables should be roughly equivalent on Aurora single\-master and multi\-master clusters\. Operations on temporary tables happen locally and are not subject to synchronous replication overhead\. 

#### Using Percona Online Schema Change with Multi\-Master Clusters<a name="using-percona-online-schema-change-with-multi-master"></a>

 The `pt-online-schema-change` tool works with multi\-master clusters\. You can use it if your priority is to run table modifications in the most nonblocking manner\. However, be aware of the write conflict implications of the schema change process\. 

 At a high level, the `pt-online-schema-change` tool works as follows: 

1.  It creates a new, empty table with the desired structure\. 

1.  It creates `DELETE`, `INSERT` and `UPDATE` triggers on the original table to redo any data changes on the original table on top of the new table\. 

1.  It moves existing rows into the new table using small chunks while ongoing table changes are automatically handled using the triggers\. 

1.  After all the data is moved, it drops the triggers and switches the tables by renaming them\. 

 The potential contention point occurs while the data is being transferred to the new table\. When the new table is initially created, it's completely empty and therefore can become a locking hot point\. The same is true in other kinds of database systems\. Because triggers are synchronous, the impact from the hot point can propagate back to your queries\. 

 In multi\-master clusters, the impact can be more visible\. This visibility is because the new table not only provokes lock contention, but also increases the likelihood of write conflicts\. The table initially has very few pages in it, which means that writes are highly localized and therefore prone to conflicts\. After the table grows, writes should spread out and write conflicts should no longer be a problem\. 

 You can use the online schema change tool with multi\-master clusters\. However, it might require more careful testing and its effects on the ongoing workload might be slightly more visible in the first minutes of the operation\. 

### Using Autoincrement Columns<a name="using-auto-increment-columns"></a>

 Aurora multi\-master clusters handle autoincrement columns using the existing configuration parameters `auto_increment_increment` and `auto_increment_offset`\. For more information, see the [MySQL Reference Manual](https://dev.mysql.com/doc/refman/5.6/en/replication-options-master.html#sysvar_auto_increment_increment)\. 

 Parameter values are predetermined and you can't change them\. Specifically, the `auto_increment_increment` parameter is hardcoded to 16, which is the maximum number of DB instances in any kind of Aurora cluster\. 

 Due to the hard\-coded increment setting, autoincrement values are consumed much more quickly than in single\-master clusters\. This is true even if a given table is only ever modified by a single DB instance\. For best results, always use a `BIGINT` data type instead of `INT` for your autoincrement columns\. 

 In a multi\-master cluster, your application logic must be prepared to tolerate autoincrement columns that have the following properties: 
+  The values are noncontiguous\. 
+  The values might not start from 1 on an empty table\. 
+  The values increase by increments greater than 1\. 
+  The values are consumed significantly more quickly than in a single\-master cluster\. 

 The following example shows how the sequence of autoincrement values in a multi\-master cluster can be different from what you might expect\. 

```
mysql> create table autoinc (id bigint not null auto_increment, s varchar(64), primary key (id));

mysql> insert into autoinc (s) values ('row 1'), ('row 2'), ('row 3');
Query OK, 3 rows affected (0.02 sec)

mysql> select * from autoinc order by id;
+----+-------+
| id | s     |
+----+-------+
|  2 | row 1 |
| 18 | row 2 |
| 34 | row 3 |
+----+-------+
3 rows in set (0.00 sec)
```

 You can change the `AUTO_INCREMENT` table property\. Using a nondefault value only works reliably if that value is larger than any of the primary key values already in the table\. You can't use smaller values to fill in an empty interval in the table\. If you do, the change takes effect either temporarily or not at all\.  This behavior is inherited from MySQL 5\.6 and is not specific to the Aurora implementation\. 

### Multi\-Master Clusters Feature Reference<a name="multi-master-feature-reference"></a>

 Following, you can find a quick reference of the commands, procedures, and status variables specific to Aurora multi\-master clusters\. 

#### Using Read\-After\-Write<a name="using-read-after-write"></a>

 The read\-after\-write feature is controlled using the `aurora_mm_session_consistency_level` session variable\. The valid values are `INSTANCE_RAW` for local consistency mode \(default\) and `REGIONAL_RAW` for cluster\-wide consistency\. 

 An example follows\. 

```
mysql> select @@aurora_mm_session_consistency_level;
+---------------------------------------+
| @@aurora_mm_session_consistency_level |
+---------------------------------------+
| INSTANCE_RAW                          |
+---------------------------------------+
1 row in set (0.01 sec)
mysql> set session aurora_mm_session_consistency_level = 'REGIONAL_RAW';
Query OK, 0 rows affected (0.00 sec)
mysql> select @@aurora_mm_session_consistency_level;
+---------------------------------------+
| @@aurora_mm_session_consistency_level |
+---------------------------------------+
| REGIONAL_RAW                          |
+---------------------------------------+
1 row in set (0.03 sec)
```

#### Checking DB Instance Read\-Write Mode<a name="checking-node-readwrite-mode"></a>

 In multi\-master clusters, all nodes operate in read/write mode\. The `innodb_read_only` variable always returns zero\. The following example shows that when you connect to any DB instance in a multi\-master cluster, the DB instance reports that it has read/write capability\. 

```
$ mysql -h mysql -A -h multi-master-instance-1.example123.us-east-1.rds.amazonaws.com
mysql> select @@innodb_read_only;
+--------------------+
| @@innodb_read_only |
+--------------------+
|                  0 |
+--------------------+
mysql> quit;
Bye

$ mysql -h mysql -A -h multi-master-instance-2.example123.us-east-1.rds.amazonaws.com
mysql> select @@innodb_read_only;
+--------------------+
| @@innodb_read_only |
+--------------------+
|                  0 |
+--------------------+
```

#### Checking the Node Name and Role<a name="checking-node-name-and-role"></a>

 You can check the name of the DB instance you're currently connected to by using the `aurora_server_id` status variable\.  The following example shows how\. 

```
mysql> select @@aurora_server_id;
+----------------------+
| @@aurora_server_id   |
+----------------------+
| mmr-demo-test-mm-3-1 |
+----------------------+
1 row in set (0.00 sec)
```

 To find this information for all the DB instances in a multi\-master cluster, see [Describing Cluster Topology](#describing-cluster-topology)\. 

#### Describing Cluster Topology<a name="describing-cluster-topology"></a>

 You can describe multi\-master cluster topology by selecting from the `information_schema.replica_host_status` table\. Multi\-master clusters have the following differences from single\-master clusters: 
+  The `has_primary` column identifies the role of the node\. For multi\-master clusters, this value is true for the DB instance that handles all DDL and DCL statements\. Aurora forwards such requests to one of the DB instances in a multi\-master cluster\. 
+  The `replica_lag_in_milliseconds` column reports replication lag on all DB instances\. 
+  The `last_reported_status` column reports the status of the DB instance\. It can be `Online`, `Recovery`, or `Offline`\. 

 An example follows\. 

```
mysql> select server_id, has_primary, replica_lag_in_milliseconds, last_reported_status
    -> from information_schema.replica_host_status;
+----------------------+-------------+-----------------------------+----------------------+
| server_id            | has_primary | replica_lag_in_milliseconds | last_reported_status |
+----------------------+------------------+------------------------+----------------------+
| mmr-demo-test-mm-3-1 | true        |                      37.302 | Online               |
| mmr-demo-test-mm-3-2 | false       |                      39.907 | Online               |
+----------------------+-------------+-----------------------------+----------------------+
```

#### Using Instance Read\-Only Mode<a name="using-instance-read-only-mode"></a>

 In Aurora multi\-master clusters, you usually issue `SELECT` statements to the specific DB instance that performs write operations on the associated tables\. Doing so avoids consistency issues due to replication lag and maximizes reuse for table and index data from the buffer pool\. 

 If you need to run a query\-intensive workload across multiple tables, you might designate one of more DB instances within a multi\-master cluster as read\-only\. 

 To put an entire DB instance into read\-only mode at runtime, call the `mysql.rds_set_read_only` stored procedure\. 

```
mysql> select @@read_only;
+-------------+
| @@read_only |
+-------------+
|           0 |
+-------------+
1 row in set (0.00 sec)
mysql> call mysql.rds_set_read_only(1);
Query OK, 0 rows affected (0.00 sec)
mysql> select @@read_only;
+-------------+
| @@read_only |
+-------------+
|           1 |
+-------------+
1 row in set (0.00 sec)
mysql> call mysql.rds_set_read_only(0);
Query OK, 0 rows affected (0.00 sec)
mysql> select @@read_only;
+-------------+
| @@read_only |
+-------------+
|           0 |
+-------------+
1 row in set (0.00 sec)
```

 Calling the stored procedure is equivalent to running `SET GLOBAL read_only = 0|1`\. That setting is runtime only and doesn't survive an engine restart\. You can permanently set the DB instance to read\-only by setting the `read_only` parameter to `true` in the parameter group for your DB instance\. 

## Performance Considerations for Aurora Multi\-Master Clusters<a name="aurora-multi-master-performance"></a>

 For both single\-master and multi\-master clusters, the Aurora engine is optimized for OLTP workloads\. OLTP applications consist mostly of short\-lived transactions with highly selective, random\-access queries\. You get the most advantage from Aurora with workloads that run many such operations concurrently\. 

 Avoid running all the time at 100 percent utilization\. Doing so lets Aurora keep up with internal maintenance work\. To learn how to measure how busy a multi\-master cluster is and how much maintenance work is needed, see [Monitoring an Aurora Multi\-Master Cluster](#aurora-multi-master-monitoring)\. 

**Topics**
+ [Query Performance for Multi\-Master Clusters](#aurora-multi-master-query-perf)
+ [Conflict Resolution for Multi\-Master Clusters](#aurora-multi-master-conflicts)
+ [Optimizing Buffer Pool and Dictionary Cache Usage](#optimizing-buffer-pool-and-dictionary-cache-usage)

### Query Performance for Multi\-Master Clusters<a name="aurora-multi-master-query-perf"></a>

 Multi\-master clusters don't provide dedicated read\-only nodes or read\-only DNS endpoints, but it's possible to create groups of read\-only DB instances and use them for the intended purpose\. For more information, see [Using Instance Read\-Only Mode](#using-instance-read-only-mode)\. 

 You can use the following approaches to optimize query performance for a multi\-master cluster: 
+  Perform `SELECT` statements on the DB instance that handles the shard containing the associated table, database, or other schema objects involved in the query\. This technique maximizes reuse of data in the buffer pool\. It also avoids the same data being cached on more than one DB instance\. For more details about this technique, see [Optimizing Buffer Pool and Dictionary Cache Usage](#optimizing-buffer-pool-and-dictionary-cache-usage)\. 
+  If you need read/write workload isolation, designate one or more DB instances as read\-only, as described in [Using Instance Read\-Only Mode](#using-instance-read-only-mode)\. You can direct read\-only sessions to those DB instances by connecting to the corresponding instance endpoints, or by defining a custom endpoint that is associated with all the read\-only instances\. 
+  Spread read\-only queries across all DB instances\. This approach is the least efficient\. Use one of the other approaches where practical, especially as you move from the development and test phase towards production\. 

### Conflict Resolution for Multi\-Master Clusters<a name="aurora-multi-master-conflicts"></a>

 Many best practices for multi\-master clusters focus on reducing the chance of write conflicts\. Resolving write conflicts involves network overhead\. Your applications must also handle error conditions and retry transactions\. Wherever possible, try to minimize these unwanted consequences: 
+  Wherever practical, make all changes to a particular table and its associated indexes using the same DB instance\. If only one DB instance ever modifies a data page, changing that page cannot trigger any write conflicts\. This access pattern is common in sharded or multitenant database deployments\. Thus, it's relatively easy to switch such deployments to use multi\-master clusters\. 
+  A multi\-master cluster doesn't have a reader endpoint\. The reader endpoint load\-balances incoming connections, freeing you from knowing which DB instance is handling a particular connection\. In a multi\-master cluster, managing connections involves being aware which DB instance is used for each connection\. That way, modifications to a particular database or table can always be routed to the same DB instance\. 
+  A write conflict for a small amount of data \(one 16\-KB page\) can trigger a substantial amount of work to roll back the entire transaction\. Thus, ideally you keep the transactions for a multi\-master cluster relatively brief and small\. This best practice for OLTP applications is especially important for Aurora multi\-master clusters\. 

 Conflicts are detected at page level\. A conflict could occur because proposed changes from different DB instances modify different rows within the page\. All page changes introduced in the system are subject to conflict detection\. This rule applies regardless of whether the source is a user transaction or a server background process\. It also applies whether the data page is from a table, secondary index, undo space, and so on\. 

 You can divide the write operations so that each DB instance handles all write operations for a set of schema objects\. In this case, all the changes to each data page are made by one specific instance\. 

### Optimizing Buffer Pool and Dictionary Cache Usage<a name="optimizing-buffer-pool-and-dictionary-cache-usage"></a>

 Each DB instance in a multi\-master cluster maintains separate in\-memory buffers and caches such as the buffer pool, table handler cache, and table dictionary cache\. For each DB instance, the contents and amount of turnover for the buffers and caches depends on the SQL statements processed by that instance\. 

 Using memory efficiently can help the performance of multi\-master clusters and reduce I/O cost\. Use a sharded design to physically separate the data and write to each shard from a particular DB instance\. Doing so makes the most efficient use of the buffer cache on each DB instance\. Try to assign `SELECT` statements for a table to the same DB instance that performs write operations for that table\. Doing so helps those queries to reuse the cached data on that DB instance\. If you have a large number of tables or partitions, this technique also reduces the number of unique table handlers and dictionary objects held in memory by each DB instance\. 

## Approaches to Aurora Multi\-Master Clusters<a name="aurora-multi-master-tutorial"></a>

 In the following sections, you can find approaches to take for particular deployments that are suitable for multi\-master clusters\. These approaches involve ways to divide the workload so that the DB instances perform write operations for portions of the data that don't overlap\. Doing so minimizes the chances of write conflicts\. Write conflicts are the main focus of performance tuning and troubleshooting for a multi\-master cluster\. 

**Topics**
+ [Using a Multi\-Master Cluster for a Sharded Database](#aurora-multi-master-sharding)
+ [Using a Multi\-Master Cluster Without Sharding](#aurora-multi-master-ranges)
+ [Using a Multi\-Master Cluster as an Active Standby](#aurora-multi-master-active-standby)

### Using a Multi\-Master Cluster for a Sharded Database<a name="aurora-multi-master-sharding"></a>

 Sharding is a popular type of schema design that works well with Aurora multi\-master clusters\. In a sharded architecture, each DB instance is assigned to update a specific group of schema objects\. That way, multiple DB instances can write to the same shared storage volume without conflicts from concurrent changes\. Each DB instance can handle write operations for multiple shards\. You can change the mapping of DB instances to shards at any time by updating your application configuration\. You don't need to reorganize your database storage or reconfigure DB instances when you do so\. 

 Applications that use a sharded schema design are good candidates to use with Aurora multi\-master clusters\. The way the data is physically divided in a sharded system helps to avoid write conflicts\. You map each shard to a schema object such as a partition, a table, or a database\. Your application directs all write operations for a particular shard to the appropriate DB instance\. 

 Bring\-your\-own\-shard \(BYOS\) describes a use case where you already have a sharded/partitioned database and an application capable of accessing it\. The shards are already physically separated\. Thus, you can easily move the workload to Aurora multi\-master clusters without changing your schema design\. The same simple migration path applies to multitenant databases, where each tenant uses a dedicated table, a set of tables, or an entire database\. 

 You map shards or tenants to DB instances in a one\-to\-one or many\-to\-one fashion\. Each DB instance handles one or more shards\. The sharded design primarily applies to write operations\. You can issue `SELECT` queries for any shard from any DB instance with equivalent performance\. 

 Suppose that as time goes on, one of the shards becomes much more active\. To rebalance the workload, you can switch which DB instance is responsible for that shard\. In a non\-Aurora system, you might have to physically move the data to a different server\. With an Aurora multi\-master cluster, you can reshard like this by directing all write operations for the shard to some other DB instance that has unused compute capacity\. The Aurora shared storage model avoids the need to physically reorganize the data\. 

### Using a Multi\-Master Cluster Without Sharding<a name="aurora-multi-master-ranges"></a>

 If your schema design doesn't subdivide the data into physically separate containers such as databases, tables, or partitions, you can still use it with a multi\-master cluster\. 

 You might see some performance overhead, and your application might have to deal with occasional transaction rollbacks when write conflicts are treated as deadlock conditions\. Write conflicts are more likely during write operations for small tables\. If a table contains few data pages, rows from different parts of the primary key range might be in the same data page\. This overlap might lead to write conflicts if those rows are changed simultaneously by different DB instances\. 

 You should also minimize the number of secondary indexes in this case\. When you make a change to indexed columns in a table, Aurora makes corresponding changes in the associated secondary indexes\. A change to an index could cause a write conflict because the order and grouping of rows is different between a secondary index and the associated table\. 

 Because you might still experience some write conflicts when using this technique, Amazon recommends using a different approach if practical\. See if you can use an alternative database design that subdivides the data into different schema objects\. 

### Using a Multi\-Master Cluster as an Active Standby<a name="aurora-multi-master-active-standby"></a>

 An *active standby* is a DB instance that is kept synchronized with another DB instance, and is ready to take over for it very quickly\. This configuration helps with high availability in situations where a single DB instance can handle the full workload\. 

 You can use multi\-master clusters in an active standby configuration by directing all traffic, both read/write and read\-only, to a single DB instance\. If that DB instance becomes unavailable, your application must detect the problem and switch all connections to a different DB instance\. In this case, Aurora doesn't perform any failover because the other DB instance is already available to accept read/write connections\. By only writing to a single DB instance at any one time, you avoid write conflicts\. Thus, you don't need to have a sharded database schema to use multi\-master clusters in this way\. 

**Tip**  
 If your application can tolerate a brief pause, you can wait several seconds after a DB instance becomes unavailable before redirecting write traffic to another instance\. When an instance becomes unavailable because of a restart, it becomes available again after approximately 10–20 seconds\. If the instance can't restart quickly, Aurora might initiate recovery for that instance\. When an instance is shut down, it performs some additional cleanup activities as part of the shutdown\. If you begin writing to a different instance while the instance is restarting, undergoing recovery, or being shut down, you can encounter write conflicts\. The conflicts can occur between SQL statements on the new instance, and recovery operations such as rollback and purge on the instance that was restarted or shut down\. 