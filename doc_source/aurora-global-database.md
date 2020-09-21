# Using Amazon Aurora Global Databases<a name="aurora-global-database"></a>

Following, you can find a description of the Amazon Aurora Global Database feature, used to create Aurora global databases\. Each Aurora global database spans multiple AWS Regions, enabling low latency global reads and disaster recovery from outages across an AWS Region\. 

**Topics**
+ [Overview of Aurora Global Databases](#aurora-global-database-overview)
+ [Advantages of Aurora Global Databases](#aurora-global-database.advantages)
+ [Limitations of Aurora Global Databases](#aurora-global-database.limitations)
+ [Getting Started with Aurora Global Databases](aurora-global-database-getting-started.md)
+ [Managing an Aurora Global Database](aurora-global-database-managing.md)
+ [Connecting to an Aurora Global Database](aurora-global-database-connecting.md)
+ [Write Forwarding for Secondary AWS Regions with an Aurora Global Database](aurora-global-database-write-forwarding.md)
+ [Using Aurora Global Databases with Other AWS Services](aurora-global-database-interop.md)

## Overview of Aurora Global Databases<a name="aurora-global-database-overview"></a>

 An Aurora global database consists of one primary AWS Region where your data is maintained, and up to five read\-only, secondary AWS Regions\. Aurora replicates data to the secondary AWS Regions with typical latency of under a second\. You issue write operations directly to the primary DB instance in the primary AWS Region\. 

An Aurora global database uses dedicated infrastructure to replicate your data, leaving database resources available entirely to serve application workloads\. Applications with a worldwide footprint can use reader instances in the secondary AWS Regions for low\-latency reads\. In some unlikely cases, your database might become degraded or isolated in an AWS Region\. If this happens, with a global database you can promote one of the secondary AWS Regions to take full read/write workloads in under a minute\. 

 The Aurora cluster in the primary AWS Region where your data is mastered performs both read and write operations\. The clusters in the secondary Regions enable low\-latency reads\. You can scale up the secondary clusters independently by adding one of more DB instances \(Aurora Replicas\) to serve read\-only workloads\. For disaster recovery, you can remove a secondary cluster from the Aurora global database and promote it to allow full read and write operations\. 

 Only the primary cluster performs write operations\. Clients that perform write operations connect to the DB cluster endpoint of the primary cluster\. 

## Advantages of Aurora Global Databases<a name="aurora-global-database.advantages"></a>

With Aurora global databases, you can find the following advantages: 
+ Secondary clusters provide scaling of reads far beyond the capacity of a single Aurora cluster\. 

  A secondary cluster doesn't have a writer primary DB instance\. This functionality means that it can have up to 16 read\-only Aurora Replica instances, instead of the limit of 15 for a single Aurora cluster\.
+  The replication performed by an Aurora global database has little performance impact on the primary DB cluster\. The resources of the DB instances are fully devoted to serve application read and write workloads\. 
+  Changes are replicated between AWS Regions with minimal lag time, typically less than 1 second\. 
+  The secondary clusters enable fast failover for disaster recovery\. You can typically promote a secondary cluster and make it available for writes in under a minute\. 
+  Applications in remote AWS Regions experience lower query latency when they read from a secondary cluster\. 

## Limitations of Aurora Global Databases<a name="aurora-global-database.limitations"></a>

 The following limitations currently apply to Aurora global databases:
+  You can use Aurora global databases with the following Aurora versions:
  + Aurora MySQL\. For Aurora MySQL with MySQL 5\.6 compatibility, you can use version 5\.6\.10a or version 1\.22 or later\. For Aurora MySQL with MySQL 5\.7 compatibility, you can use version 2\.07 or later\.
  + Aurora PostgreSQL with PostgreSQL compatibility for versions 10\.11, 10\.12, 11\.7 and later\.
+  You have a choice of using `db.r4` or `db.r5` instance classes for an Aurora global database\. You can't use `db.t2` or `db.t3` instance classes\.
+ Aurora global databases are available in the following AWS Regions:
  + US East \(N\. Virginia\)
  + US East \(Ohio\)
  + US West \(N\. California\)
  + US West \(Oregon\)
  + Europe \(Ireland\)
  + Europe \(London\)
  + Europe \(Paris\)
  + Europe \(Frankfurt\)
  + Asia Pacific \(Mumbai\)
  + Asia Pacific \(Singapore\)
  + Asia Pacific \(Sydney\)
  + Asia Pacific \(Tokyo\)
  + Asia Pacific \(Seoul\)
  + Canada \(Central\)
+  A secondary cluster must be in a different AWS Region than the primary cluster\. 
+ To upgrade your global database clusters, make sure to upgrade the secondary clusters before the primary cluster\. For more information about upgrading, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching) or [Upgrading the PostgreSQL DB Engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 
+  You can start a Database Activity Stream only on the primary cluster, not on any secondary clusters\. For information about Database Activity Streams, see [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)\.

 The following features aren't supported for Aurora global databases: 
+  Cloning\. For information about cloning, see [Cloning an Aurora DB Cluster Volume](Aurora.Managing.Clone.md)\. 
+  Backtrack\. For information about backtracking, see [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md)\. 
+  Parallel query\. For information about parallel query, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. 
+ Aurora Serverless\. For information about Aurora Serverless, see [Using Amazon Aurora Serverless](aurora-serverless.md)\. 
+ Importing an RDS database into an Aurora PostgreSQL cluster using an Aurora Read Replica\. You can't make the Aurora PostgreSQL cluster a global database until after the replication is complete\. For more information, see [Migrating Data from an RDS PostgreSQL DB Instance to an Aurora PostgreSQL DB Cluster by Using an Aurora Read Replica](AuroraPostgreSQL.Migrating.md#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica)\.
+  Stopping and starting the DB clusters within the global database\. For information about stopping and starting Aurora clusters, see [Stopping and Starting an Amazon Aurora DB Cluster](aurora-cluster-stop-start.md)\. 