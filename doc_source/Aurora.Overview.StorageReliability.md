# Amazon Aurora Storage and Reliability<a name="Aurora.Overview.StorageReliability"></a>

 Aurora data is stored in the cluster volume, which is designed for reliability\. 

## Amazon Aurora Storage<a name="Aurora.Overview.Storage"></a>

 Aurora data is stored in the *cluster volume*, which is a single, virtual volume that uses solid state drives \(SSDs\)\. A cluster volume consists of copies of the data across multiple Availability Zones in a single AWS Region\. Because the data is automatically replicated across Availability Zones, your data is highly durable with less possibility of data loss\. This replication also ensures that your database is more available during a failover\. It does so because the data copies already exist in the other Availability Zones and continue to serve data requests to the DB instances in your DB cluster\. The amount of replication is independent of the number of DB instances in your cluster\. 

 Aurora cluster volumes automatically grow as the amount of data in your database increases\. An Aurora cluster volume can grow to a maximum size of 64 tebibytes \(TiB\)\. Table size is limited to the size of the cluster volume\. That is, the maximum table size for a table in an Aurora DB cluster is 64 TiB\. Even though an Aurora cluster volume can grow to up to 64 TiB, you are only charged for the space that you use in an Aurora cluster volume\. For pricing information, see [Amazon RDS for Aurora Pricing](https://aws.amazon.com/rds/aurora/pricing)\. 

## Amazon Aurora Reliability<a name="Aurora.Overview.Reliability"></a>

 Aurora is designed to be reliable, durable, and fault tolerant\. You can architect your Aurora DB cluster to improve availability by doing things such as adding Aurora Replicas and placing them in different Availability Zones, and also Aurora includes several automatic features that make it a reliable database solution\. 

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