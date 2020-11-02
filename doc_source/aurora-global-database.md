# Using Amazon Aurora global databases<a name="aurora-global-database"></a>

Following, you can find a description of the Amazon Aurora Global Database feature, used to create Aurora global databases\. Each Aurora global database spans multiple AWS Regions, enabling low latency global reads and disaster recovery from outages across an AWS Region\. 

**Topics**
+ [Overview of Aurora global databases](#aurora-global-database-overview)
+ [Advantages of Aurora global databases](#aurora-global-database.advantages)
+ [Limitations of Aurora global databases](#aurora-global-database.limitations)
+ [Getting started with Aurora global databases](aurora-global-database-getting-started.md)
+ [Managing an Aurora global database](aurora-global-database-managing.md)
+ [Connecting to an Aurora global database](aurora-global-database-connecting.md)
+ [Write forwarding for secondary AWS Regions with an Aurora global database](aurora-global-database-write-forwarding.md)
+ [Using Aurora global databases with other AWS services](aurora-global-database-interop.md)

## Overview of Aurora global databases<a name="aurora-global-database-overview"></a>

 An Aurora global database consists of one primary AWS Region where your data is maintained, and up to five read\-only, secondary AWS Regions\. Aurora replicates data to the secondary AWS Regions with typical latency of under a second\. You issue write operations directly to the primary DB instance in the primary AWS Region\. 

An Aurora global database uses dedicated infrastructure to replicate your data, leaving database resources available entirely to serve application workloads\. Applications with a worldwide footprint can use reader instances in the secondary AWS Regions for low\-latency reads\. In some unlikely cases, your database might become degraded or isolated in an AWS Region\. If this happens, with a global database you can promote one of the secondary AWS Regions to take full read/write workloads in under a minute\. 

 The Aurora cluster in the primary AWS Region where your data is mastered performs both read and write operations\. The clusters in the secondary Regions enable low\-latency reads\. You can scale up the secondary clusters independently by adding one of more DB instances \(Aurora Replicas\) to serve read\-only workloads\. For disaster recovery, you can remove a secondary cluster from the Aurora global database and promote it to allow full read and write operations\. 

 Only the primary cluster performs write operations\. Clients that perform write operations connect to the DB cluster endpoint of the primary cluster\. 

## Advantages of Aurora global databases<a name="aurora-global-database.advantages"></a>

Aurora global databases can provide you with the following advantages: 
+ Secondary clusters provide scaling of reads far beyond the capacity of a single Aurora cluster\. 

  A secondary cluster doesn't have a writer primary DB instance\. This functionality means that it can have up to 16 read\-only Aurora Replica instances, instead of the limit of 15 for a single Aurora cluster\.
+  The replication performed by an Aurora global database has little performance impact on the primary DB cluster\. The resources of the DB instances are fully devoted to serve application read and write workloads\. 
+  Changes are replicated between AWS Regions with minimal lag time, typically less than 1 second\. 
+  The secondary clusters enable fast failover for disaster recovery\. You can typically promote a secondary cluster and make it available for writes in under a minute\. 
+  Applications in remote AWS Regions experience lower query latency when they read from a secondary cluster\. 

## Limitations of Aurora global databases<a name="aurora-global-database.limitations"></a>

 The following limitations currently apply to Aurora global databases:
+ Aurora global databases are available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Aurora global databases](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase)\. 
+  You have a choice of using `db.r4` or `db.r5` instance classes for an Aurora global database\. You can't use `db.t2` or `db.t3` instance classes\.
+  A secondary cluster must be in a different AWS Region than the primary cluster\. 
+ To upgrade your global database clusters, make sure to upgrade the secondary clusters before the primary cluster\. For more information about upgrading, see [Upgrading and patching Amazon Aurora MySQL DB clusters](AuroraMySQL.Updates.Patching.md) or [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 
+ If the primary AWS Region's DB instance restarts or fails over, any Aurora Replicas in that Region also restart\. The cluster is then unavailable until all replicas are back in sync with the writer of the primary DB cluster\. This behavior is documented more fully in [Replication with Amazon Aurora](Aurora.Replication.md) and [Replication with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Replication.md)\. Be sure you understand the possible impacts discussed in those sections before making changes to your primary DB cluster\. For more information about Aurora global database failover in general and how to plan for and recover from outages, see [Managing an Aurora global database](aurora-global-database-managing.md)\. 
+  You can start a database activity stream only on the primary cluster, not on any secondary clusters\.  Currently, database activity stream is available for global database clusters using Aurora PostgreSQL only\. For information about database activity streams, see [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)\.

 The following features aren't supported for Aurora global databases: 
+  Backtrack\. For information about backtracking, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\. 
+ Aurora Serverless\. For information about Aurora Serverless, see [Using Amazon Aurora Serverless](aurora-serverless.md)\. 
+ Importing an RDS database into an Aurora PostgreSQL cluster using an Aurora Read Replica\. You can't make the Aurora PostgreSQL cluster a global database until after the replication is complete\. For more information, see [Migrating data from an RDS PostgreSQL DB instance to an Aurora PostgreSQL DB cluster by using an Aurora read replica](AuroraPostgreSQL.Migrating.md#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica)\.
+  When using Aurora with PostgreSQL compatibility with Aurora global clusters, you can't create a secondary cluster for your global database if its primary is based on a replica of an Amazon RDS PostgreSQL instance\. That is, if the primary instance in your primary cluster is populated from an Amazon RDS PostgreSQL DB instance, don't attempt to create a secondary from that cluster using the AWS Management Console, the AWS CLI, or the `CreateDBCluster` API\. These will time out and the secondary won't get created\. It's best to create secondary DB clusters using the same version as the primary\. For more information, see [Creating an Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-creating)\. 
+  Stopping and starting the DB clusters within the global database\. For information about stopping and starting Aurora clusters, see [Stopping and starting an Amazon Aurora DB cluster](aurora-cluster-stop-start.md)\. 