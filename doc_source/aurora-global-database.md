# Using Amazon Aurora global databases<a name="aurora-global-database"></a>

Amazon Aurora global databases span multiple AWS Regions, enabling low latency global reads and providing fast recovery from any outage that might affect any single AWS Region\. An Aurora global database has a primary DB cluster in one Region, and secondary DB clusters in one or more different Regions\. 

**Topics**
+ [Overview of Amazon Aurora global databases](#aurora-global-database-overview)
+ [Advantages of Amazon Aurora global databases](#aurora-global-database.advantages)
+ [Limitations of Amazon Aurora global databases](#aurora-global-database.limitations)
+ [Getting started with Amazon Aurora global databases](aurora-global-database-getting-started.md)
+ [Managing an Amazon Aurora global database](aurora-global-database-managing.md)
+ [Connecting to an Amazon Aurora global database](aurora-global-database-connecting.md)
+ [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)
+ [Disaster recovery for Amazon Aurora global databases](aurora-global-database-disaster-recovery.md)
+ [Monitoring an Amazon Aurora global database](aurora-global-database-monitoring.md)
+ [Using Amazon Aurora global databases with other AWS services](aurora-global-database-interop.md)

## Overview of Amazon Aurora global databases<a name="aurora-global-database-overview"></a>

By using an Amazon Aurora global database, you can have a single Aurora database that spans multiple AWS Regions to support your globally distributed applications\.

An Aurora global database consists of one *primary* AWS Region where your data is mastered, and up to five read\-only *secondary* AWS Regions\. You issue write operations directly to the primary DB cluster in the primary AWS Region\. Aurora replicates data to the secondary AWS Regions using dedicated infrastructure, with latency typically under a second\. 

You can also change the configuration of your Aurora global database while it's running to support various use cases\. For example, you might want the read/write capabilities to move from one Region to another, say, in different time zones, to 'follow the sun\.' Or, you might need to respond to an outage in one Region\. With Aurora global database, you can promote one of the secondary Regions to the primary role to take full read/write workloads in under a minute\.

The following diagram shows an example Aurora global database that spans two AWS Regions\.

![\[An Aurora global database has a single primary and at least one secondary Aurora DB clusters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-conceptual-illo.png)

You can scale up each secondary cluster independently, by adding one or more Aurora Replicas \(read\-only Aurora DB instances\) to serve read\-only workloads\. 

Only the primary cluster performs write operations\. Clients that perform write operations connect to the DB cluster endpoint of the primary DB cluster\. 

Aurora global database is designed for applications with a worldwide footprint\. The read\-only secondary DB clusters \(AWS Regions\) allow you to support read operations closer to application users\. With features such as *write forwarding*, you can also configure an Aurora MySQL–based global database so that secondary clusters send data to the primary\. For more information, see [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)\. 

## Advantages of Amazon Aurora global databases<a name="aurora-global-database.advantages"></a>

Aurora global databases can provide you with the following advantages: 
+ **Global reads with local latency** – Enterprises with offices around the world can use an Aurora global database to keep their main sources of information updated in the primary Region\. Offices in other Regions can access the information in their own AWS Region, with local latency\. 
+ **Scalable secondary Aurora DB clusters** – You can scale your secondary clusters by adding more read\-only instances \(Aurora Replicas\) to a secondary AWS Region\. The secondary cluster is read\-only, so it can support up to 16 read\-only Aurora Replica instances rather than the usual limit of 15 for a single Aurora cluster\.
+ **Fast replication from primary to secondary Aurora DB clusters** – The replication performed by an Aurora global database has little performance impact on the primary DB cluster\. The resources of the DB instances are fully devoted to serve application read and write workloads\. 
+ **Disaster recovery from Region–wide outages** – The secondary clusters enable fast failover for disaster recovery\. You can promote a secondary cluster and make it available for writes in under a minute\. 

## Limitations of Amazon Aurora global databases<a name="aurora-global-database.limitations"></a>

The following limitations currently apply to Aurora global databases:
+ Aurora global database is available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Aurora global databases](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase)\. 
+ Aurora global databases currently don't support the following Aurora features: 
  + Aurora multi\-master clusters
  + Aurora Serverless v1
  + Backtracking in Aurora 
+ You can start database activity streams on Aurora global databases running the following Aurora MySQL and Aurora PostgreSQL versions only:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html)

  For information about database activity streams, see [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)\.
+ To upgrade an Aurora global database, upgrade all secondary clusters before upgrading the primary cluster\. However, for an Aurora PostgreSQL–based Aurora global database, you can upgrade the Aurora DB engine to a new minor version only\. This limitation applies to Aurora DB clusters for Aurora PostgreSQL only, not to Aurora MySQL\. For more information about upgrading Aurora DB engines, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md) or [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 
+ You can't stop or start the Aurora DB clusters in your Aurora global database individually\. To learn more, see [Stopping and starting an Amazon Aurora DB cluster](aurora-cluster-stop-start.md)\. 
+ Aurora Replicas attached to the primary Aurora DB cluster can restart under certain circumstances\. If the primary AWS Region's DB instance restarts or fails over, Aurora Replicas in that Region also restart\. The cluster is then unavailable until all replicas are back in sync with the primary DB cluster's writer instance\. This behavior is expected, and is documented in [Replication with Amazon Aurora](Aurora.Replication.md)\. Be sure you understand the impacts to your Aurora global database before making changes to your primary DB cluster\. To learn more, see [Managing recovery for Aurora global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-manage-recovery)\. 
+ Aurora PostgreSQL–based DB clusters running in an Aurora global database have the following limitations: 
  + If the primary DB cluster of your Aurora global database is based on a Replica of an Amazon RDS PostgreSQL instance, you can't create a secondary cluster\. Don't attempt to create a secondary from that cluster using the AWS Management Console, the AWS CLI, or the `CreateDBCluster` API operation\. Attempts to do so time out, and the secondary cluster is not created\. 

We recommend that you create secondary DB clusters for your Aurora global databases by using the same version of the Aurora DB engine as the primary\. For more information, see [Creating an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-creating)\. 