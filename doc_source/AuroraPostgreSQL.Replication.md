# Replication with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Replication"></a>

Following, you can find a description of replication with Amazon Aurora PostgreSQL, including how to monitor replication\.

**Topics**
+ [Using Aurora Replicas](#AuroraPostgreSQL.Replication.Replicas)
+ [Monitoring Aurora PostgreSQL Replication](#AuroraPostgreSQL.Replication.Monitoring)
+ [Using PostgreSQL Logical Replication with Aurora](AuroraPostgreSQL.Replication.Logical.md)

## Using Aurora Replicas<a name="AuroraPostgreSQL.Replication.Replicas"></a>

*Aurora Replicas *are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. The DB cluster volume is made up of multiple copies of the data for the DB cluster\. However, the data in the cluster volume is represented as a single, logical volume to the primary writer DB instance and to Aurora Replicas in the DB cluster\. For more information about Aurora Replicas, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)\.

Aurora Replicas work well for read scaling because they're fully dedicated to read operations on your cluster volume\. The writer DB instance manages write operations\. The cluster volume is shared among all instances in your Aurora PostgreSQL DB cluster\. Thus, no additional work is required to replicate a copy of the data for each Aurora Replica\. In contrast, PostgreSQL read replicas must apply, on a single thread, all write operations from the primary DB instance to their local data store\. This limitation can affect the ability of PostgreSQL read replicas to support large volumes of write traffic\.

**Note**  
Rebooting the writer DB instance of an Amazon Aurora DB cluster also automatically reboots the Aurora Replicas for that DB cluster\. The automatic reboot re\-establishes an entry point that guarantees read/write consistency across the DB cluster\.

## Monitoring Aurora PostgreSQL Replication<a name="AuroraPostgreSQL.Replication.Monitoring"></a>

Read scaling and high availability depend on minimal lag time\. You can monitor how far an Aurora Replica is lagging behind the writer DB instance of your Aurora PostgreSQL DB cluster by monitoring the Amazon CloudWatch `ReplicaLag` metric\. Because Aurora Replicas read from the same cluster volume as the writer DB instance, the `ReplicaLag` metric has a different meaning for an Aurora PostgreSQL DB cluster\. The `ReplicaLag` metric for an Aurora Replica indicates the lag for the page cache of the Aurora Replica compared to that of the writer DB instance\.

For more information on monitoring RDS instances and CloudWatch metrics, see [Monitoring an Amazon Aurora DB Cluster](MonitoringAurora.md)\.