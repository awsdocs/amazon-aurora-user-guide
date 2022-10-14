# Using Amazon Aurora global databases<a name="aurora-global-database"></a><a name="gdb"></a><a name="globaldb"></a><a name="global_database"></a>

Amazon Aurora global databases span multiple AWS Regions, enabling low latency global reads and providing fast recovery from the rare outage that might affect an entire AWS Region\. An Aurora global database has a primary DB cluster in one Region, and up to five secondary DB clusters in different Regions\. 

**Topics**
+ [Overview of Amazon Aurora global databases](#aurora-global-database-overview)
+ [Advantages of Amazon Aurora global databases](#aurora-global-database.advantages)
+ [Region and version availability](#aurora-global-database.Availability)
+ [Limitations of Amazon Aurora global databases](#aurora-global-database.limitations)
+ [Getting started with Amazon Aurora global databases](aurora-global-database-getting-started.md)
+ [Managing an Amazon Aurora global database](aurora-global-database-managing.md)
+ [Connecting to an Amazon Aurora global database](aurora-global-database-connecting.md)
+ [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)
+ [Using failover in an Amazon Aurora global database](aurora-global-database-disaster-recovery.md)
+ [Monitoring an Amazon Aurora global database](aurora-global-database-monitoring.md)
+ [Using Amazon Aurora global databases with other AWS services](aurora-global-database-interop.md)
+ [Upgrading an Amazon Aurora global database](aurora-global-database-upgrade.md)

## Overview of Amazon Aurora global databases<a name="aurora-global-database-overview"></a>

By using an Amazon Aurora global database, you can run your globally distributed applications using a single Aurora database that spans multiple AWS Regions\.

An Aurora global database consists of one *primary* AWS Region where your data is written, and up to five read\-only *secondary* AWS Regions\. You issue write operations directly to the primary DB cluster in the primary AWS Region\. Aurora replicates data to the secondary AWS Regions using dedicated infrastructure, with latency typically under a second\. 

In the following diagram, you can find an example Aurora global database that spans two AWS Regions\.

![\[An Aurora global database has a single primary and at least one secondary Aurora DB clusters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-conceptual-illo.png)

You can scale up each secondary cluster independently, by adding one or more Aurora Replicas \(read\-only Aurora DB instances\) to serve read\-only workloads\. 

Only the primary cluster performs write operations\. Clients that perform write operations connect to the DB cluster endpoint of the primary DB cluster\. As shown in the diagram, Aurora global database uses the cluster storage volume and not the database engine for replication\. To learn more, see [Overview of Aurora storage](Aurora.Overview.StorageReliability.md#Aurora.Overview.Storage)\. 

Aurora global databases are designed for applications with a worldwide footprint\. The read\-only secondary DB clusters \(AWS Regions\) allow you to support read operations closer to application users\. By using features such as write forwarding, you can also configure an Aurora MySQL–based global database so that secondary clusters send data to the primary\. For more information, see [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)\. 

An Aurora global database supports two different approaches to failover\. To recover your Aurora global database after an outage in the primary Region, you use the *manual unplanned failover* process\. With this process, you fail over your primary to another Region \(cross\-Region failover\)\. For more information about this process, see [Recovering an Amazon Aurora global database from an unplanned outage](aurora-global-database-disaster-recovery.md#aurora-global-database-failover)\.

For planned operational procedures such as maintenance, you use *managed planned failover*\. With this feature, you can relocate the primary cluster of a healthy Aurora global database to one of its secondary Regions with no data loss\. To learn more, see [Performing managed planned failovers for Amazon Aurora global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-disaster-recovery.managed-failover)\. 

## Advantages of Amazon Aurora global databases<a name="aurora-global-database.advantages"></a>

By using Aurora global databases, you can get the following advantages: 
+ **Global reads with local latency** – If you have offices around the world, you can use an Aurora global database to keep your main sources of information updated in the primary AWS Region\. Offices in your other Regions can access the information in their own Region, with local latency\. 
+ **Scalable secondary Aurora DB clusters** – You can scale your secondary clusters by adding more read\-only instances \(Aurora Replicas\) to a secondary AWS Region\. The secondary cluster is read\-only, so it can support up to 16 read\-only Aurora Replica instances rather than the usual limit of 15 for a single Aurora cluster\.
+ **Fast replication from primary to secondary Aurora DB clusters** – The replication performed by an Aurora global database has little performance impact on the primary DB cluster\. The resources of the DB instances are fully devoted to serve application read and write workloads\.
+ **Recovery from Region\-wide outages** – The secondary clusters allow you to make an Aurora global database available in a new primary AWS Region more quickly \(lower RTO\) and with less data loss \(lower RPO\) than traditional replication solutions\. 

## Region and version availability<a name="aurora-global-database.Availability"></a>

Feature availability and support varies across specific versions of each Aurora database engine, and across AWS Regions\. For more information on version and Region availability with Aurora and global databases, see [Aurora global databases](Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.md)\. 

## Limitations of Amazon Aurora global databases<a name="aurora-global-database.limitations"></a>

The following limitations currently apply to Aurora global databases:
+ Aurora global databases are available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Aurora global databases](Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.md)\.
+ Aurora global databases have certain configuration requirements for supported Aurora DB instance classes, maximum number of AWS Regions, and so on\. For more information, see [Configuration requirements of an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database.configuration.requirements)\.
+ Managed planned failover for Aurora global databases requires one of the following Aurora database engines:
  + Aurora MySQL with MySQL 8\.0 compatibility, version 3\.01\.0 and higher
  + Aurora MySQL with MySQL 5\.7 compatibility, version 2\.09\.1 and higher
  + Aurora MySQL with MySQL 5\.6 compatibility, version 1\.23\.1 and higher
  +  Aurora PostgreSQL versions 13\.3 and higher, 12\.4 and higher, 11\.9 and higher, and 10\.14 and higher 
+ Aurora global databases can't fail over from a primary to a secondary DB cluster when the primary and secondary have different DB engine versions—whether major, minor, or patch versions\.
+ Aurora global databases currently don't support the following Aurora features: 
  + Aurora multi\-master clusters
  + Aurora Serverless v1
  + Backtracking in Aurora
  + Amazon RDS Proxy
+ Automatic minor version upgrade doesn't apply to Aurora MySQL and Aurora PostgreSQL clusters that are part of an Aurora global database\. Note that you can specify this setting for a DB instance that is part of a global database cluster, but the setting has no effect\.
+ Aurora global databases currently don't support Aurora Auto Scaling for secondary DB clusters\.
+ You can start database activity streams on Aurora global databases running the following Aurora MySQL and Aurora PostgreSQL versions only\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html)

  For information about database activity streams, see [Monitoring Amazon Aurora with Database Activity Streams](DBActivityStreams.md)\.
+ The following limitations currently apply to upgrading Aurora global databases:
  + You can't apply a custom parameter group to the global database cluster while you're performing a major version upgrade of that Aurora global database\. You create your custom parameter groups in each Region of the global cluster and you apply them manually to the Regional clusters after the upgrade\.
  + With an Aurora global database based on Aurora PostgreSQL, you can't perform a major version upgrade of the Aurora DB engine if the recovery point objective \(RPO\) feature is turned on\. For information about the RPO feature, see [Managing RPOs for Aurora PostgreSQL–based global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-manage-recovery)\.

  For information about upgrading an Aurora global database, see [Upgrading an Amazon Aurora global database](aurora-global-database-upgrade.md)\.
+ You can't stop or start the Aurora DB clusters in your Aurora global database individually\. To learn more, see [Stopping and starting an Amazon Aurora DB cluster](aurora-cluster-stop-start.md)\. 
+ Aurora Replicas attached to the secondary Aurora DB cluster can restart under certain circumstances\. If the primary AWS Region's writer DB instance restarts or fails over, Aurora Replicas in secondary Regions also restart\. The secondary cluster is then unavailable until all replicas are back in sync with the primary DB cluster's writer instance\. This behavior is expected, as documented in [Replication with Amazon Aurora](Aurora.Replication.md)\. Be sure that you understand the impacts to your Aurora global database before making changes to your primary DB cluster\. To learn more, see [Recovering an Amazon Aurora global database from an unplanned outage](aurora-global-database-disaster-recovery.md#aurora-global-database-failover)\. 
+ Aurora PostgreSQL–based DB clusters running in an Aurora global database have the following limitations:
  + Cluster cache management isn't supported for Aurora PostgreSQL DB clusters that are part of Aurora global databases\.
  + If the primary DB cluster of your Aurora global database is based on a replica of an Amazon RDS PostgreSQL instance, you can't create a secondary cluster\. Don't attempt to create a secondary from that cluster using the AWS Management Console, the AWS CLI, or the `CreateDBCluster` API operation\. Attempts to do so time out, and the secondary cluster isn't created\.

We recommend that you create secondary DB clusters for your Aurora global databases by using the same version of the Aurora DB engine as the primary\. For more information, see [Creating an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-creating)\.