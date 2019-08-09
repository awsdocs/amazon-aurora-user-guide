# Single\-Master Replication with Amazon Aurora MySQL<a name="AuroraMySQL.Replication"></a>

 The Aurora MySQL replication features are key to the high availability and performance of your cluster\. Aurora makes it easy to create or resize clusters with up to 15 Aurora Replicas\. 

 All the replicas work from the same underlying data\. If some database instances go offline, others remain available to continue processing queries or to take over as the writer if needed\. Aurora automatically spreads your read\-only connections across multiple database instances, helping an Aurora cluster to support query\-intensive workloads\. 

 Following, you can find information about how Aurora MySQL replication works and how to fine\-tune replication settings for best availability and performance\. 

**Note**  
 Following, you can learn about replication features for Aurora clusters using single\-master replication\. This kind of cluster is the default for Aurora\. For information about Aurora multi\-master clusters, see [Working with Aurora Multi\-Master Clusters](aurora-multi-master.md)\. 

**Topics**
+ [Using Aurora Replicas](#AuroraMySQL.Replication.Replicas)
+ [Replication Options for Amazon Aurora MySQL](#AuroraMySQL.Replication.Options)
+ [Performance Considerations for Amazon Aurora MySQL Replication](#AuroraMySQL.Replication.Performance)
+ [High Availability Considerations for Amazon Aurora MySQL Replication](#AuroraMySQL.Replication.Availability)
+ [Monitoring Amazon Aurora MySQL Replication](#AuroraMySQL.Replication.Monitoring)
+ [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)
+ [Replication Between Aurora and MySQL or Between Aurora and Another Aurora DB Cluster](AuroraMySQL.Replication.MySQL.md)
+ [Using GTID\-Based Replication for Aurora MySQL](mysql-replication-gtid.md)

## Using Aurora Replicas<a name="AuroraMySQL.Replication.Replicas"></a>

Aurora Replicas are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. Although the DB cluster volume is made up of multiple copies of the data for the DB cluster, the data in the cluster volume is represented as a single, logical volume to the primary instance and to Aurora Replicas in the DB cluster\. For more information about Aurora Replicas, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)\.

Aurora Replicas work well for read scaling because they are fully dedicated to read operations on your cluster volume\. Write operations are managed by the primary instance\. Because the cluster volume is shared among all instances in your Aurora MySQL DB cluster, no additional work is required to replicate a copy of the data for each Aurora Replica\. In contrast, MySQL Read Replicas must replay, on a single thread, all write operations from the master DB instance to their local data store\. This limitation can affect the ability of MySQL Read Replicas to support large volumes of read traffic\.

With Aurora MySQL, when an Aurora Replica is deleted, its instance endpoint is removed immediately, and the Aurora Replica is removed from the reader endpoint\. If there are statements executing on the Aurora Replica that is being deleted, there is a three minute grace period\. Existing statements can finish gracefully during the grace period\. When the grace period ends, the Aurora Replica is shut down and deleted\.

**Important**  
Aurora Replicas for Aurora MySQL always use the `REPEATABLE READ` default transaction isolation level for operations on InnoDB tables\. You can use the `SET TRANSACTION ISOLATION LEVEL` command to change the transaction level only for the primary instance of an Aurora MySQL DB cluster\. This restriction avoids user\-level locks on Aurora Replicas, and allows Aurora Replicas to scale to support thousands of active user connections while still keeping replica lag to a minimum\.

**Note**  
DDL statements executed on the primary instance might interrupt database connections on the associated Aurora Replicas\. If an Aurora Replica connection is actively using a database object, such as a table, and that object is modified on the primary instance using a DDL statement, the Aurora Replica connection is interrupted\.

**Note**  
The China \(Ningxia\) region does not support cross\-region read replicas\.

## Replication Options for Amazon Aurora MySQL<a name="AuroraMySQL.Replication.Options"></a>

You can set up replication between any of the following options:
+ Two Aurora MySQL DB clusters in different AWS regions, by creating an Aurora Read Replica of an Aurora MySQL DB cluster in a different AWS Region\.

  For more information, see [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.
+ Two Aurora MySQL DB clusters in the same AWS Region, by using MySQL binary log \(binlog\) replication\.

  For more information, see [Replication Between Aurora and MySQL or Between Aurora and Another Aurora DB Cluster](AuroraMySQL.Replication.MySQL.md)\.
+ An Amazon RDS MySQL DB instance as the master and an Aurora MySQL DB cluster, by creating an Aurora Read Replica of an Amazon RDS MySQL DB instance\.

   Typically, this approach is used for migration to Aurora MySQL, rather than for ongoing replication\. For more information, see [Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using a DB Snapshot](AuroraMySQL.Migrating.RDSMySQL.md)\.

**Note**  
Rebooting the primary instance of an Amazon Aurora DB cluster also automatically reboots the Aurora Replicas for that DB cluster, in order to re\-establish an entry point that guarantees read/write consistency across the DB cluster\.

## Performance Considerations for Amazon Aurora MySQL Replication<a name="AuroraMySQL.Replication.Performance"></a>

 Starting in Aurora MySQL 1\.17\.4, the following features help you to fine\-tune the performance of Aurora MySQL replication\. 

 The replica log compression feature automatically reduces network bandwidth for replication messages\. Because each message is transmitted to all Aurora Replicas, the benefits are greater for larger clusters\. This feature involves some CPU overhead on the writer node to perform the compression\. Thus, the feature is only available on the `8xlarge` and `16xlarge` instance classes, which have high CPU capacity\. It is enabled by default on these instance classes\. You can control this feature by turning off the `aurora_enable_replica_log_compression` parameter\. For example, you might turn off replica log compression for larger instance classes if your writer node is near its maximum CPU capacity\. 

 The binlog filtering feature automatically reduces network bandwidth for replication messages\. Because the Aurora Replicas don't use the binlog information that is included in the replication messages, that data is omitted from the messages sent to those nodes\. You control this feature by changing the `aurora_enable_repl_bin_log_filtering` parameter\. This parameter is on by default\. Because this optimization is intended to be transparent, you might turn off this setting only during diagnosis or troubleshooting for issues related to replication\. For example, you can do so to match the behavior of an older Aurora MySQL cluster where this feature was not available\. 

## High Availability Considerations for Amazon Aurora MySQL Replication<a name="AuroraMySQL.Replication.Availability"></a>

 Having more Aurora Replicas in your cluster helps to ensure high availability\. A database instance with a full copy of your data is always available for you to query, even if some database instances become unavailable\. 

 The tradeoff with having multiple Aurora Replicas is that replicas become unavailable for brief periods when the underlying database instances are restarted\. These restarts can happen during maintenance operations, or when a replica begins to lag too far behind the master\. Restarting a replica interrupts existing connections to the corresponding database instance\. Restarting an Aurora cluster causes all the replicas to become unavailable at the same time\. 

 Starting in Aurora MySQL 1\.17\.4, the following feature helps to ensure high availability even during these intervals when replicas are restarted\. 

 The "zero\-downtime restart" feature preserves existing connections when an Aurora MySQL Replica is restarted, for example if the replica falls too far behind the master\. Any open transaction is rolled back, and your application must retry it\. To enable this feature, turn on the `aurora_enable_zdr` parameter in the cluster parameter group\. This parameter is off by default\. 

## Monitoring Amazon Aurora MySQL Replication<a name="AuroraMySQL.Replication.Monitoring"></a>

Read scaling and high availability depend on minimal lag time\. You can monitor how far an Aurora Replica is lagging behind the primary instance of your Aurora MySQL DB cluster by monitoring the Amazon CloudWatch `ReplicaLag` metric\. Because Aurora Replicas read from the same cluster volume as the primary instance, the `ReplicaLag` metric has a different meaning for an Aurora MySQL DB cluster\. The `ReplicaLag` metric for an Aurora Replica indicates the lag for the page cache of the Aurora Replica compared to that of the primary instance\.

If you need the most current value for Aurora Replica lag, you can query the `mysql.ro_replica_status` table in your Aurora MySQL DB cluster and check the value in the `Replica_lag_in_msec` column\. This column value is provided to Amazon CloudWatch as the value for the `ReplicaLag` metric\. The values in the `mysql.ro_replica_status` are also provided in the `INFORMATION_SCHEMA.REPLICA_HOST_STATUS` table in your Aurora MySQL DB cluster\.

For more information on monitoring RDS instances and CloudWatch metrics, see [Monitoring an Amazon Aurora DB Cluster](MonitoringAurora.md)\.