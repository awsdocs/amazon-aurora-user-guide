# Amazon Aurora storage and reliability<a name="Aurora.Overview.StorageReliability"></a>

 Following, you can learn about the Aurora storage subsystem\. Aurora uses a distributed and shared storage architecture that is an important factor in performance, scalability, and reliability for Aurora clusters\. 

**Topics**
+ [Overview of Aurora storage](#Aurora.Overview.Storage)
+ [What the cluster volume contains](#aurora-storage-contents)
+ [How Aurora storage automatically resizes](#aurora-storage-growth)
+ [How Aurora data storage is billed](#aurora-storage-data-billing)
+ [Amazon Aurora reliability](#Aurora.Overview.Reliability)

## Overview of Aurora storage<a name="Aurora.Overview.Storage"></a>

Aurora data is stored in the *cluster volume*, which is a single, virtual volume that uses solid state drives \(SSDs\)\. A cluster volume consists of copies of the data across three Availability Zones in a single AWS Region\. Because the data is automatically replicated across Availability Zones, your data is highly durable with less possibility of data loss\. This replication also ensures that your database is more available during a failover\. It does so because the data copies already exist in the other Availability Zones and continue to serve data requests to the DB instances in your DB cluster\. The amount of replication is independent of the number of DB instances in your cluster\.

Aurora uses separate local storage for nonpersistent, temporary files\. This includes files that are used for such purposes as sorting large data sets during query processing, and building indexes\. For more information, see [Temporary storage limits for Aurora MySQLTemporary storage limits](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.TempStorage) and [Temporary storage limits for Aurora PostgreSQLTemporary storage limits](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.TempStorage)\.

## What the cluster volume contains<a name="aurora-storage-contents"></a>

 The Aurora cluster volume contains all your user data, schema objects, and internal metadata such as the system tables and the binary log\. For example, Aurora stores all the tables, indexes, binary large objects \(BLOBs\), stored procedures, and so on for an Aurora cluster in the cluster volume\. 

 The Aurora shared storage architecture makes your data independent from the DB instances in the cluster\. For example, you can add a DB instance quickly because Aurora doesn't make a new copy of the table data\. Instead, the DB instance connects to the shared volume that already contains all your data\. You can remove a DB instance from a cluster without removing any of the underlying data from the cluster\. Only when you delete the entire cluster does Aurora remove the data\. 

## How Aurora storage automatically resizes<a name="aurora-storage-growth"></a>

 Aurora cluster volumes automatically grow as the amount of data in your database increases\. The maximum size for an Aurora cluster volume is 128 tebibytes \(TiB\) or 64 TiB, depending on the DB engine version\. For details about the maximum size for a specific version, see [Amazon Aurora size limits](CHAP_Limits.md#RDS_Limits.FileSize.Aurora)\. This automatic storage scaling is combined with a high\-performance and highly distributed storage subsystem\. These make Aurora a good choice for your important enterprise data when your main objectives are reliability and high availability\. 

To display the volume status, see [Displaying volume status for an Aurora MySQL DB cluster](AuroraMySQL.Managing.VolumeStatus.md) or [Displaying volume status for an Aurora PostgreSQL DB cluster](AuroraPostgreSQL.Managing.VolumeStatus.md)\. For ways to balance storage costs against other priorities, [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling) describes how to monitor the Amazon Aurora metrics `AuroraVolumeBytesLeftTotal` and `VolumeBytesUsed` in CloudWatch\.

 When Aurora data is removed, the space allocated for that data is freed\. Examples of removing data include dropping or truncating a table\. This automatic reduction in storage usage helps you to minimize storage charges\. 

**Note**  
 The storage limits and dynamic resizing behavior discussed here applies to persistent tables and other data stored in the cluster volume\. Data for temporary tables is stored in the local DB instance and its maximum size depends on the instance class that you use\. 

 Some storage features, such as the maximum size of a cluster volume and automatic resizing when data is deleted, depend on the Aurora version of your cluster\. For more information, see [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling)\. You can also learn how to avoid storage issues and how to monitor the allocated storage and free space in your cluster\. 

## How Aurora data storage is billed<a name="aurora-storage-data-billing"></a>

 Even though an Aurora cluster volume can grow up to 128 tebibytes \(TiB\), you are only charged for the space that you use in an Aurora cluster volume\. In earlier Aurora versions, the cluster volume could reuse space that was freed up when you deleted data, but the allocated storage space would never decrease\. Starting in Aurora MySQL 2\.09\.0 and 1\.23\.0, and Aurora PostgreSQL 3\.3\.0 and 2\.6\.0, when Aurora data is removed, such as by dropping a table or database, the overall allocated space decreases by a comparable amount\. Thus, you can reduce storage charges by deleting tables, indexes, databases, and so on that you no longer need\. 

**Tip**  
 For earlier versions without the dynamic resizing feature, resetting the storage usage for a cluster involved doing a logical dump and restoring to a new cluster\. That operation can take a long time for a substantial volume of data\. If you encounter this situation, consider upgrading your cluster to a version that supports volume shrinking\. 

 For pricing information about Aurora data storage, see [Amazon RDS for Aurora Pricing](https://aws.amazon.com/rds/aurora/pricing)\. 

 For information about how to minimize storage charges by monitoring storage usage for your cluster, see [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling)\. For pricing information about Aurora data storage, see [Amazon RDS for Aurora pricing](https://aws.amazon.com/rds/aurora/pricing)\. 

## Amazon Aurora reliability<a name="Aurora.Overview.Reliability"></a>

 Aurora is designed to be reliable, durable, and fault tolerant\. You can architect your Aurora DB cluster to improve availability by doing things such as adding Aurora Replicas and placing them in different Availability Zones, and also Aurora includes several automatic features that make it a reliable database solution\. 

**Topics**
+ [Storage auto\-repair](#Aurora.Overview.AutoRepair)
+ [Survivable page cache](#Aurora.Overview.CacheWarming)
+ [Crash recovery](#Aurora.Overview.CrashRecovery)

### Storage auto\-repair<a name="Aurora.Overview.AutoRepair"></a>

 Because Aurora maintains multiple copies of your data in three Availability Zones, the chance of losing data as a result of a disk failure is greatly minimized\. Aurora automatically detects failures in the disk volumes that make up the cluster volume\. When a segment of a disk volume fails, Aurora immediately repairs the segment\. When Aurora repairs the disk segment, it uses the data in the other volumes that make up the cluster volume to ensure that the data in the repaired segment is current\. As a result, Aurora avoids data loss and reduces the need to perform a point\-in\-time restore to recover from a disk failure\. 

### Survivable page cache<a name="Aurora.Overview.CacheWarming"></a>

In Aurora, each DB instance's page cache is managed in a separate process from the database, which allows the page cache to survive independently of the database\. \(The page cache is also called the InnoDB *buffer pool* on Aurora MySQL and the *buffer cache* on Aurora PostgreSQL\.\)

In the unlikely event of a database failure, the page cache remains in memory, which keeps current data pages "warm" in the page cache when the database restarts\. This provides a performance gain by bypassing the need for the initial queries to execute read I/O operations to "warm up" the page cache\.

For Aurora MySQL, page cache behavior when rebooting and failing over is the following:
+ Versions earlier than 2\.10 – When the writer DB instance reboots, the page cache on the writer instance survives, but reader DB instances lose their page caches\.
+ Version 2\.10 and higher – You can reboot the writer instance without rebooting the reader instances\.
  + If the reader instances don't reboot when the writer instance reboots, they don't lose their page caches\.
  + If the reader instances reboot when the writer instance reboots, they do lose their page caches\.
+ When a reader instance reboots, the page caches on the writer and reader instances both survive\.
+ When the DB cluster fails over, the effect is similar to when a writer instance reboots\. On the new writer instance \(previously the reader instance\) the page cache survives, but on the reader instance \(previously the writer instance\), the page cache doesn't survive\.

For Aurora PostgreSQL, you can use cluster cache management to preserve the page cache of a designated reader instance that becomes the writer instance after failover\. For more information, see [Fast recovery after failover with cluster cache management for Aurora PostgreSQL](AuroraPostgreSQL.cluster-cache-mgmt.md)\.

### Crash recovery<a name="Aurora.Overview.CrashRecovery"></a>

 Aurora is designed to recover from a crash almost instantaneously and continue to serve your application data without the binary log\. Aurora performs crash recovery asynchronously on parallel threads, so that your database is open and available immediately after a crash\. 

 For more information about crash recovery, see [Fault tolerance for an Aurora DB cluster](Concepts.AuroraHighAvailability.md#Aurora.Managing.FaultTolerance)\. 

 The following are considerations for binary logging and crash recovery on Aurora MySQL: 
+  Enabling binary logging on Aurora directly affects the recovery time after a crash, because it forces the DB instance to perform binary log recovery\. 
+  The type of binary logging used affects the size and efficiency of logging\. For the same amount of database activity, some formats log more information than others in the binary logs\. The following settings for the `binlog_format` parameter result in different amounts of log data: 
  +  `ROW` – The most log data 
  +  `STATEMENT` – The least log data 
  +  `MIXED` – A moderate amount of log data that usually provides the best combination of data integrity and performance 

   The amount of binary log data affects recovery time\. If there is more data logged in the binary logs, the DB instance must process more data during recovery, which increases recovery time\. 
+  Aurora does not need the binary logs to replicate data within a DB cluster or to perform point in time restore \(PITR\)\. 
+  If you don't need the binary log for external replication \(or an external binary log stream\), we recommend that you set the `binlog_format` parameter to `OFF` to disable binary logging\. Doing so reduces recovery time\. 

 For more information about Aurora binary logging and replication, see [Replication with Amazon Aurora](Aurora.Replication.md)\. For more information about the implications of different MySQL replication types, see [Advantages and disadvantages of statement\-based and row\-based replication](https://dev.mysql.com/doc/refman/5.6/en/replication-sbr-rbr.html) in the MySQL documentation\. 