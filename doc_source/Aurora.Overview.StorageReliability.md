# Amazon Aurora Storage and Reliability<a name="Aurora.Overview.StorageReliability"></a>

 Following, you can learn about the Aurora storage subsystem\. Aurora uses a distributed and shared storage architecture that is an important factor in performance, scalability, and reliability for Aurora clusters\. 

**Topics**
+ [Overview of Aurora Storage](#Aurora.Overview.Storage)
+ [What the Cluster Volume Contains](#aurora-storage-contents)
+ [How Aurora Storage Grows](#aurora-storage-growth)
+ [How Aurora Data Storage is Billed](#aurora-storage-data-billing)
+ [Amazon Aurora Reliability](#Aurora.Overview.Reliability)

## Overview of Aurora Storage<a name="Aurora.Overview.Storage"></a>

 Aurora data is stored in the *cluster volume*, which is a single, virtual volume that uses solid state drives \(SSDs\)\. A cluster volume consists of copies of the data across multiple Availability Zones in a single AWS Region\. Because the data is automatically replicated across Availability Zones, your data is highly durable with less possibility of data loss\. This replication also ensures that your database is more available during a failover\. It does so because the data copies already exist in the other Availability Zones and continue to serve data requests to the DB instances in your DB cluster\. The amount of replication is independent of the number of DB instances in your cluster\. 

## What the Cluster Volume Contains<a name="aurora-storage-contents"></a>

 The Aurora cluster volume contains all your user data, schema objects, and internal metadata such as the system tables and the binary log\. For example, Aurora stores all the tables, indexes, binary large objects \(BLOBs\), stored procedures, and so on for an Aurora cluster in the cluster volume\. 

 The Aurora shared storage architecture makes your data independent from the DB instances in the cluster\. For example, you can add a DB instance quickly because Aurora doesn't make a new copy of the table data\. Instead, the DB instance connects to the shared volume that already contains all your data\. You can remove a DB instance from a cluster without removing any of the underlying data from the cluster\. Only when you delete the entire cluster does Aurora remove the data\. 

## How Aurora Storage Grows<a name="aurora-storage-growth"></a>

 Aurora cluster volumes automatically grow as the amount of data in your database increases\. An Aurora cluster volume can grow to a maximum size of 128 tebibytes \(TiB\) for Aurora MySQL and 64 TiB for Aurora PostgreSQL\. 

 This automatic storage scaling, combined with the high\-performance and highly distributed storage subsystem, makes Aurora a good choice for your important enterprise data when your main objectives are reliability and high availability\. For ways to balance storage costs against these other priorities, see the following sections\. 

## How Aurora Data Storage is Billed<a name="aurora-storage-data-billing"></a>

 Even though an Aurora cluster volume can grow to up to 128 TiB for MySQL and 64 TiB for PostgreSQL, you are only charged for the space that you use in an Aurora cluster volume\. When Aurora data is removed, such as by dropping a table or partition, the overall allocated space remains the same\. The free space is reused automatically when data volume increases in the future\. 

**Note**  
 Because storage costs are based on the storage "high water mark" \(the maximum amount that was allocated for the Aurora cluster at any point in time\), you can manage costs by avoiding ETL practices that create large volumes of temporary information, or that load large volumes of new data prior to removing unneeded older data\. 

 If removing data from an Aurora cluster results in a substantial amount of allocated but unused space, resetting the high water mark requires doing a logical data dump and restore to a new cluster, using a tool such as `mysqldump`\. Creating and restoring a snapshot does *not* reduce the allocated storage because the physical layout of the underlying storage remains the same in the restored snapshot\. 

 For pricing information about Aurora data storage, see [Amazon RDS for Aurora Pricing](https://aws.amazon.com/rds/aurora/pricing)\. 

## Amazon Aurora Reliability<a name="Aurora.Overview.Reliability"></a>

 Aurora is designed to be reliable, durable, and fault tolerant\. You can architect your Aurora DB cluster to improve availability by doing things such as adding Aurora Replicas and placing them in different Availability Zones, and also Aurora includes several automatic features that make it a reliable database solution\. 

**Topics**
+ [Storage Auto\-Repair](#Aurora.Overview.AutoRepair)
+ [Survivable Cache Warming](#Aurora.Overview.CacheWarming)
+ [Crash Recovery](#Aurora.Overview.CrashRecovery)

### Storage Auto\-Repair<a name="Aurora.Overview.AutoRepair"></a>

 Because Aurora maintains multiple copies of your data in three Availability Zones, the chance of losing data as a result of a disk failure is greatly minimized\. Aurora automatically detects failures in the disk volumes that make up the cluster volume\. When a segment of a disk volume fails, Aurora immediately repairs the segment\. When Aurora repairs the disk segment, it uses the data in the other volumes that make up the cluster volume to ensure that the data in the repaired segment is current\. As a result, Aurora avoids data loss and reduces the need to perform a point\-in\-time restore to recover from a disk failure\. 

### Survivable Cache Warming<a name="Aurora.Overview.CacheWarming"></a>

 Aurora "warms" the buffer pool cache when a database starts up after it has been shut down or restarted after a failure\. That is, Aurora preloads the buffer pool with the pages for known common queries that are stored in an in\-memory page cache\. This provides a performance gain by bypassing the need for the buffer pool to "warm up" from normal database use\. 

 The Aurora page cache is managed in a separate process from the database, which allows the page cache to survive independently of the database\. In the unlikely event of a database failure, the page cache remains in memory, which ensures that the buffer pool is warmed with the most current state when the database restarts\. 

### Crash Recovery<a name="Aurora.Overview.CrashRecovery"></a>

 Aurora is designed to recover from a crash almost instantaneously and continue to serve your application data without the binary log\. Aurora performs crash recovery asynchronously on parallel threads, so that your database is open and available immediately after a crash\. 

 For more information about crash recovery, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\. 

 The following are considerations for binary logging and crash recovery on Aurora MySQL: 
+  Enabling binary logging on Aurora directly affects the recovery time after a crash, because it forces the DB instance to perform binary log recovery\. 
+  The type of binary logging used affects the size and efficiency of logging\. For the same amount of database activity, some formats log more information than others in the binary logs\. The following settings for the `binlog_format` parameter result in different amounts of log data: 
  +  `ROW` – The most log data 
  +  `STATEMENT` – The least log data 
  +  `MIXED` – A moderate amount of log data that usually provides the best combination of data integrity and performance 

   The amount of binary log data affects recovery time\. If there is more data logged in the binary logs, the DB instance must process more data during recovery, which increases recovery time\. 
+  Aurora does not need the binary logs to replicate data within a DB cluster or to perform point in time restore \(PITR\)\. 
+  If you don't need the binary log for external replication \(or an external binary log stream\), we recommend that you set the `binlog_format` parameter to `OFF` to disable binary logging\. Doing so reduces recovery time\. 

 For more information about Aurora binary logging and replication, see [Replication with Amazon Aurora](Aurora.Replication.md)\. For more information about the implications of different MySQL replication types, see [Advantages and Disadvantages of Statement\-Based and Row\-Based Replication](https://dev.mysql.com/doc/refman/5.6/en/replication-sbr-rbr.html) in the MySQL documentation\. 