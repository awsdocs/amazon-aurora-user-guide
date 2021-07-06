# Amazon Aurora metrics<a name="Aurora.AuroraMySQL.Monitoring.Metrics"></a>

The `AWS/RDS` namespace includes the following metrics that apply to database entities running on Amazon Aurora\. Some metrics apply to either Aurora MySQL, Aurora PostgreSQL, or both\. Furthermore, some metrics are specific to a cluster, primary instance, replica instance, or all instances\.

For Aurora global database metrics, see [Amazon CloudWatch metrics for write forwarding](aurora-global-database-write-forwarding.md#aurora-global-database-write-forwarding-cloudwatch)\. For Aurora parallel query metrics, see [Monitoring parallel query](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-monitoring)\.

**Topics**
+ [Cluster\-level metrics for Amazon Aurora](#Aurora.AuroraMySQL.Monitoring.Metrics.clusters)
+ [Instance\-level metrics for Amazon Aurora](#Aurora.AuroraMySQL.Monitoring.Metrics.instances)

## Cluster\-level metrics for Amazon Aurora<a name="Aurora.AuroraMySQL.Monitoring.Metrics.clusters"></a>

The following table describes metrics that are specific to Aurora clusters\.


**Cluster\-specific metrics**  

| Metric | Description | Applies to | Units | 
| --- | --- | --- | --- | 
|  `AuroraGlobalDBReplicatedWriteIO`  |  In an Aurora Global Database, the number of write I/O operations replicated from the primary AWS Region to the cluster volume in a secondary AWS Region\. The billing calculations for the secondary AWS Regions in a global database use `VolumeWriteIOPS` to account for writes performed within the cluster\. The billing calculations for the primary AWS Region in a global database use `VolumeWriteIOPS` to account for the write activity within that cluster, and `AuroraGlobalDBReplicatedWriteIO` to account for cross\-Region replication within the global database\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `AuroraGlobalDBDataTransferBytes`  |  In an Aurora Global Database, the amount of redo log data transferred from the master AWS Region to a secondary AWS Region\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `AuroraGlobalDBReplicationLag`  |  For an Aurora Global Database, the amount of lag when replicating updates from the primary AWS Region\.  |  Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraVolumeBytesLeftTotal`  |  The remaining available space for the cluster volume\. As the cluster volume grows, this value decreases\. If it reaches zero, the cluster reports an out\-of\-space error\.  If you want to detect whether your Aurora cluster is approaching the size limit of 128 tebibytes \(TiB\), this value is simpler and more reliable to monitor than `VolumeBytesUsed`\. `AuroraVolumeBytesLeftTotal` takes into account storage used for internal housekeeping and other allocations that don't affect your storage billing\.  This parameter is available in more recent Aurora versions\. For Aurora MySQL with MySQL 5\.6 compatibility, use Aurora version 1\.19\.5 or higher\. For Aurora MySQL with MySQL 5\.7 compatibility, use Aurora version 2\.04\.5 or higher\.  |  Aurora MySQL  |  Bytes  | 
|  `BacktrackChangeRecordsCreationRate`  |  The number of backtrack change records created over 5 minutes for your DB cluster\.  |  Aurora MySQL  |  Count per 5 minutes  | 
|  `BacktrackChangeRecordsStored`  |  The number of backtrack change records used by your DB cluster\.  |  Aurora MySQL  |  Count  | 
|  `BackupRetentionPeriodStorageUsed`  |  The total amount of backup storage used to support the point\-in\-time restore feature within the Aurora DB cluster's backup retention window\. This amount is included in the total reported by the `TotalBackupStorageBilled` metric\. It is computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `ServerlessDatabaseCapacity`  |  The current capacity of an Aurora Serverless v1 DB cluster\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `SnapshotStorageUsed`  |  The total amount of backup storage consumed by all Aurora snapshots for an Aurora DB cluster outside its backup retention window\. This amount is included in the total reported by the `TotalBackupStorageBilled` metric\. It is computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `TotalBackupStorageBilled`  |  The total amount of backup storage in bytes for which you are billed for a given Aurora DB cluster\. The metric includes the backup storage measured by the `BackupRetentionPeriodStorageUsed` and `SnapshotStorageUsed` metrics\. This metric is computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `VolumeBytesUsed`  |  The amount of storage used by your Aurora DB instance\. This value affects the cost of the Aurora DB cluster \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\.  This value doesn't reflect some internal storage allocations that don't affect storage billing\. Thus, you can anticipate out\-of\-space issues more accurately by testing whether `AuroraVolumeBytesLeftTotal` is approaching zero instead of comparing `VolumeBytesUsed` against the storage limit of 128 TiB\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `VolumeReadIOPs`  |  The number of billed read I/O operations from a cluster volume within a 5\-minute interval\. Billed read operations are calculated at the cluster volume level, aggregated from all instances in the Aurora DB cluster, and then reported at 5\-minute intervals\. The value is calculated by taking the value of the **Read operations** metric over a 5\-minute period\. You can determine the amount of billed read operations per second by taking the value of the **Billed read operations** metric and dividing by 300 seconds\. For example, if the **Billed read operations** returns 13,686, then the billed read operations per second is 45 \(13,686 / 300 = 45\.62\)\.  You accrue billed read operations for queries that request database pages that aren't in the buffer cache and must be loaded from storage\. You might see spikes in billed read operations as query results are read from storage and then loaded into the buffer cache\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per 5 minutes  | 
|  `VolumeWriteIOPs`  |  The number of write disk I/O operations to the cluster volume, reported at 5\-minute intervals\. For a detailed description of how billed write operations are calculated, see `VolumeReadIOPs`\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per 5 minutes  | 

## Instance\-level metrics for Amazon Aurora<a name="Aurora.AuroraMySQL.Monitoring.Metrics.instances"></a>

The following instance\-specific CloudWatch metrics apply to all Aurora MySQL and Aurora PostgreSQL instances unless noted otherwise\.


**Instance\-level metrics**  

| Metric | Description | Applies to | Units | 
| --- | --- | --- | --- | 
|  `AbortedClients`  | The number of client connections that have not been closed properly\. |  Aurora MySQL  |  Count  | 
|  `ActiveTransactions`  |  The average number of current transactions executing on an Aurora database instance per second\.  By default, Aurora doesn't enable this metric\. To begin measuring this value, set `innodb_monitor_enable='all'` in the DB parameter group for a specific DB instance\.   |  Aurora MySQL  |  Count per second  | 
|  `AuroraBinlogReplicaLag`  |  The amount of time a replica DB cluster running on Aurora MySQLCompatible Edition lags behind the source DB cluster\. This metric reports the value of the `Seconds_Behind_Master` field of the MySQL `SHOW SLAVE STATUS` command\. This metric is useful for monitoring replica lag between Aurora DB clusters that are replicating across different AWS Regions\. For more information, see [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.  |  Primary for Aurora MySQL  |  Seconds  | 
|  `AuroraReplicaLag`  |  For an Aurora replica, the amount of lag when replicating updates from the primary instance\.  |  Replica for Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraReplicaLagMaximum`  |  The maximum amount of lag between the primary instance and each Aurora DB instance in the DB cluster\.  |  Primary for Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraReplicaLagMinimum`  |  The minimum amount of lag between the primary instance and each Aurora DB instance in the DB cluster\.  |  Primary for Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `BacktrackWindowActual`  |  The difference between the target backtrack window and the actual backtrack window\.  |  Primary for Aurora MySQL  |  Minutes  | 
|  `BacktrackWindowAlert`  |  The number of times that the actual backtrack window is smaller than the target backtrack window for a given period of time\.  |  Primary for Aurora MySQL  |  Count  | 
|  `BinLogDiskUsage`  |  The amount of disk space occupied by binary logs on the primary instance\.  |  Primary for Aurora MySQL  |  Bytes  | 
|  `BlockedTransactions`  |  The average number of transactions in the database that are blocked per second\.  |  Aurora MySQL  |  Count per second  | 
|  `BufferCacheHitRatio`  |  The percentage of requests that are served by the buffer cache\.  |  Aurora MySQL and Aurora PostgreSQL  |  Percentage  | 
|  `CommitLatency`  |  The average duration of commit operations\.  |  Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `CommitThroughput`  |  The average number of commit operations per second\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per second  | 
|  `CPUCreditBalance`  |  The number of CPU credits that an instance has accumulated, reported at 5\-minute intervals\. You can use this metric to determine how long a DB instance can burst beyond its baseline performance level at a given rate\. This metric applies only to `db.t2.small` and `db.t2.medium` instances for Aurora MySQL, and to `db.t3` instances for Aurora PostgreSQL\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `CPUCreditUsage`  |  The number of CPU credits consumed during the specified period, reported at 5\-minute intervals\. This metric measures the amount of time during which physical CPUs have been used for processing instructions by virtual CPUs allocated to the DB instance\.  This metric applies only to `db.t2.small` and `db.t2.medium` instances for Aurora MySQL, and to `db.t3` instances for Aurora PostgreSQL\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `CPUUtilization`  |  The percentage of CPU used by an Aurora DB instance\.  |  Aurora MySQL and Aurora PostgreSQL  |  Percentage  | 
|  `DatabaseConnections`  |  The current number of connections to an Aurora DB instance\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `DDLLatency`  |  The average duration of requests such as example, create, alter, and drop requests\.  |  Aurora MySQL  |  Milliseconds  | 
|  `DDLThroughput`  |  The average number of DDL requests per second\.  |  Aurora MySQL  |  Count per second  | 
|  `Deadlocks`  |  The average number of deadlocks in the database per second\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per second  | 
|  `DeleteLatency`  |  The average duration of delete operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `DeleteThroughput`  |  The average number of delete queries per second\.  |  Aurora MySQL  |  Count per second  | 
|  `DiskQueueDepth`  |  The number of outstanding read/write requests waiting to access the disk\.  |  Aurora PostgreSQL  |  Count  | 
|  `DMLLatency`  |  The average duration of inserts, updates, and deletes\.  |  Aurora MySQL  |  Milliseconds  | 
|  `DMLThroughput`  |  The average number of inserts, updates, and deletes per second\.  |  Aurora MySQL  |  Count per second  | 
|  `EngineUptime`  |  The amount of time that the instance has been running\.  |  Aurora MySQL and Aurora PostgreSQL  |  Seconds  | 
|  `FreeableMemory`  |  The amount of available random access memory\.  |  Aurora MySQL and Aurora PostgreSQL  |  Megabytes  | 
|  `FreeLocalStorage`  |  The amount of local storage available\. Unlike for other DB engines, for Aurora DB instances this metric reports the amount of storage available to each DB instance\. This value depends on the DB instance class \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\. You can increase the amount of free storage space for an instance by choosing a larger DB instance class for your instance\.  |  Aurora MySQL and Aurora PostgreSQL  |  Megabytes  | 
|  `InsertLatency`  |  The average duration of insert operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `InsertThroughput`  |  The average number of insert operations per second\.  |  Aurora MySQL  |  Count per second  | 
|  `LoginFailures`  |  The average number of failed login attempts per second\.  |  Aurora MySQL  |  Count per second  | 
|  `MaximumUsedTransactionIDs`  |  The age of the oldest unvacuumed transaction ID, in transactions\. If this value reaches 2,146,483,648 \(2^31 \- 1,000,000\), the database is forced into read\-only mode, to avoid transaction ID wraparound\. For more information, see [Preventing transaction ID wraparound failures](https://www.postgresql.org/docs/current/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND) in the PostgreSQL documentation\.  |  Aurora PostgreSQL  |  Count  | 
|  `NetworkReceiveThroughput`  |  The amount of network throughput received from clients by each instance in the Aurora MySQL DB cluster\. This throughput doesn't include network traffic between instances in the Aurora DB cluster and the cluster volume\.  |  Aurora MySQL and Aurora PostgreSQL   |  Bytes per second \(console shows Megabytes per second\)  | 
|  `NetworkThroughput`  |  The amount of network throughput both received from and transmitted to clients by each instance in the Aurora MySQL DB cluster\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second  | 
|  `NetworkTransmitThroughput`  |  The amount of network throughput sent to clients by each instance in the Aurora DB cluster\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second \(console shows Megabytes per second\)  | 
|  `NumBinaryLogFiles`  |  The number of binlog files generated\.  | Aurora MySQL |  Count  | 
|  `Queries`  |  The average number of queries executed per second\.  |  Aurora MySQL  |  Count per second  | 
|  `RDSToAuroraPostgreSQLReplicaLag`  |  The lag when replicating updates from the primary RDS PostgreSQL instance to other nodes in the cluster\.  |  Replica for Aurora PostgreSQL  |  Seconds  | 
|  `ReadIOPS`  |  The average number of disk I/O operations per second\. Aurora PostgreSQL\-Compatible Edition reports read and write IOPS separately, in 1\-minute intervals\.  |  Aurora PostgreSQL  |  Count per second  | 
|  `ReadLatency`  |  The average amount of time taken per disk I/O operation\.  |  Aurora MySQL and Aurora PostgreSQL  |  Seconds  | 
|  `ReadThroughput`  |  The average number of bytes read from disk per second\.  |  Aurora PostgreSQL  |  Bytes per second  | 
|  `ResultSetCacheHitRatio`  |  The percentage of requests that are served by the Resultset cache\.  |  Aurora MySQL  |  Percentage  | 
|  `RollbackSegmentHistoryListLength`  |  The undo logs that record committed transactions with delete\-marked records\. These records are scheduled to be processed by the InnoDB purge operation\.  |  Aurora MySQL  |    | 
|  `RowLockTime`  |  The total time spent acquiring row locks for InnoDB tables\.  |  Aurora MySQL  |  Milliseconds  | 
|  `SelectLatency`  |  The average amount of time for select operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `SelectThroughput`  |  The average number of select queries per second\.  |  Aurora MySQL  |  Count per second  | 
|  `SumBinaryLogSize`  |  The total size of the binlog files\.  |  Aurora MySQL  |  Bytes  | 
|  `SwapUsage`  |  The amount of swap space used\. This metric is available for the Aurora PostgreSQL instance classes `db.t3.medium`, `db.r5.large`, `db.r5.xlarge`, `db.r4.large`, and `db.r4.xlarge`\. For Aurora MySQL, this metric applies only to `db.t*` instances\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `TransactionLogsDiskUsage`  |  The amount of disk space consumed by transaction logs on the Aurora PostgreSQL DB instance\. This metric is only generated when Aurora PostgreSQL is using logical replication or AWS Database Migration Service\. By default, Aurora PostgreSQL uses log records, not transaction logs\. When transaction logs aren't in use, the value for this metric is `-1`\.  |  Primary for Aurora PostgreSQL  |  Bytes  | 
|  `UpdateLatency`  |  The average amount of taken taken for update operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `UpdateThroughput`  |  The average number of updates per second\.  |  Aurora MySQL  |  Count per second  | 
|  `WriteIOPS`  |  The average number of disk I/O operations per second\. Aurora PostgreSQL reports read and write IOPS separately, at 1\-minute intervals\.  |  Aurora PostgreSQL  |  Count per second  | 
|  `WriteLatency`  |  The average amount of time taken per disk I/O operation\.  |  Aurora MySQL and Aurora PostgreSQL  |  Seconds  | 
|  `WriteThroughput`  |  The average number of bytes written to persistent storage every second\.  |  Aurora PostgreSQL  |  Bytes per second  | 