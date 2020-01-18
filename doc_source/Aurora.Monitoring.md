# Monitoring Amazon Aurora DB Cluster Metrics<a name="Aurora.Monitoring"></a>

Amazon Aurora provides a variety of Amazon CloudWatch metrics that you can monitor to determine the health and performance of your Aurora DB cluster\. You can use various tools, such as the Amazon RDS Management Console, AWS CLI, and CloudWatch API, to view Aurora metrics\. For more information, see [Monitoring an Amazon Aurora DB Cluster](MonitoringAurora.md)\.

**Note**  
If you are using Amazon RDS Performance Insights, additional metrics are available\. For more information, see [Performance Insights Metrics Published to Amazon CloudWatch](USER_PerfInsights.Cloudwatch.md)\.

## Amazon Aurora Metrics<a name="Aurora.AuroraMySQL.Monitoring.Metrics"></a>

The following metrics are available from Amazon Aurora\.

### Amazon Aurora Metrics<a name="aurora-metrics"></a>

The `AWS/RDS` namespace includes the following metrics that apply to database entities running on Amazon Aurora\.


| Metric | Description | Applies to | 
| --- | --- | --- | 
|  `ActiveTransactions`  |   The average number of current transactions executing on an Aurora database instance per second\.   By default, Aurora doesn't enable this metric\. To begin measuring this value, set `innodb_monitor_enable='all'` in the DB parameter group for a specific DB instance\.   | Aurora MySQL | 
|  `AuroraBinlogReplicaLag`  |  The amount of time a replica DB cluster running on Aurora with MySQL compatibility lags behind the source DB cluster\. This metric reports the value of the `Seconds_Behind_Master` field of the MySQL `SHOW SLAVE STATUS` command\. This metric is useful for monitoring replica lag between Aurora DB clusters that are replicating across different AWS Regions\. For more information, see [Aurora MySQL Replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Replication.CrossRegion.html)\.  | Aurora MySQL | 
| AuroraGlobalDBReplicatedWriteIO |  In an Aurora Global Database, the number of write I/O operations replicated from the primary AWS Region to the cluster volume in a secondary AWS Region\. The billing calculations for the secondary AWS Regions in a global database use `VolumeWriteIOPS` to account for writes performed within the cluster\. The billing calculations for the primary AWS Region in a global database use `VolumeWriteIOPS` to account for the write activity within that cluster, and `AuroraGlobalDBReplicatedWriteIO` to account for cross\-region replication within the global database\. Units: Bytes  | Aurora MySQL | 
| AuroraGlobalDBDataTransferBytes |  In an Aurora Global Database, the amount of redo log data transferred from the master AWS Region to a secondary AWS Region\. Units: Bytes  | Aurora MySQL | 
| AuroraGlobalDBReplicationLag |  For an Aurora Global Database, the amount of lag when replicating updates from the primary AWS Region, in milliseconds\. Units: Milliseconds  | Aurora MySQL | 
|  `AuroraReplicaLag`  |  For an Aurora Replica, the amount of lag when replicating updates from the primary instance, in milliseconds\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `AuroraReplicaLagMaximum`  |  The maximum amount of lag between the primary instance and each Aurora DB instance in the DB cluster, in milliseconds\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `AuroraReplicaLagMinimum`  |  The minimum amount of lag between the primary instance and each Aurora DB instance in the DB cluster, in milliseconds\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `BacktrackChangeRecordsCreationRate`  |  The number of backtrack change records created over five minutes for your DB cluster\.  |  Aurora MySQL  | 
|  `BacktrackChangeRecordsStored`  |  The actual number of backtrack change records used by your DB cluster\.  |  Aurora MySQL  | 
|  `BacktrackWindowActual`  |  The difference between the target backtrack window and the actual backtrack window\.  |  Aurora MySQL  | 
|  `BacktrackWindowAlert`  |  The number of times that the actual backtrack window is smaller than the target backtrack window for a given period of time\.  |  Aurora MySQL  | 
| BackupRetentionPeriodStorageUsed |   The total amount of backup storage in bytes used to support the point\-in\-time restore feature within the Aurora DB cluster's backup retention window\. Included in the total reported by the `TotalBackupStorageBilled` metric\. Computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora Backup Storage Usage](aurora-storage-backup.md)\.   | Aurora MySQL and Aurora PostgreSQL | 
|  `BinLogDiskUsage`  |  The amount of disk space occupied by binary logs on the master, in bytes\.  |  Aurora MySQL  | 
|  `BlockedTransactions`  | The average number of transactions in the database that are blocked per second\.  | Aurora MySQL | 
|  `BufferCacheHitRatio`  |  The percentage of requests that are served by the buffer cache\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `CommitLatency`  | The amount of latency for commit operations, in milliseconds\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `CommitThroughput`  | The average number of commit operations per second\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `CPUCreditBalance`  |  The number of CPU credits that an instance has accumulated\. This metric applies only to `db.t2.small ` and `db.t2.medium ` instances\. It is used to determine how long an Aurora MySQL DB instance can burst beyond its baseline performance level at a given rate\.  CPU credit metrics are reported at 5\-minute intervals\.   | Aurora MySQL | 
|  `CPUCreditUsage`  |  The number of CPU credits consumed during the specified period\. This metric applies only to `db.t2.small` and `db.t2.medium` instances\. It identifies the amount of time during which physical CPUs have been used for processing instructions by virtual CPUs allocated to the Aurora MySQL DB instance\.   CPU credit metrics are reported at 5\-minute intervals\.   | Aurora MySQL | 
|  `CPUUtilization`  |  The percentage of CPU used by an Aurora DB instance\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `DatabaseConnections`  |  The number of connections to an Aurora DB instance\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `DDLLatency`  |  The amount of latency for data definition language \(DDL\) requests, in milliseconds—for example, create, alter, and drop requests\.  | Aurora MySQL | 
|  `DDLThroughput`  |  The average number of DDL requests per second\.  | Aurora MySQL | 
|  `Deadlocks`  | The average number of deadlocks in the database per second\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `DeleteLatency`  |  The amount of latency for delete queries, in milliseconds\.  | Aurora MySQL | 
|  `DeleteThroughput`  |  The average number of delete queries per second\.  | Aurora MySQL | 
|  `DiskQueueDepth`  |  The number of outstanding read/write requests waiting to access the disk\.  | Aurora PostgreSQL | 
|  `DMLLatency`  |  The amount of latency for inserts, updates, and deletes, in milliseconds\.  | Aurora MySQL | 
|  `DMLThroughput`  | The average number of inserts, updates, and deletes per second\.  | Aurora MySQL | 
|  `EngineUptime`  |  The amount of time that the instance has been running, in seconds\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `FreeableMemory`  |  The amount of available random access memory, in bytes\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `FreeLocalStorage`  |  The amount of local storage available, in bytes\. Unlike for other DB engines, for Aurora DB instances this metric reports the amount of storage available to each DB instance\. This value depends on the DB instance class \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\. You can increase the amount of free storage space for an instance by choosing a larger DB instance class for your instance\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `InsertLatency`  |  The amount of latency for insert queries, in milliseconds\.  | Aurora MySQL | 
|  `InsertThroughput`  |  The average number of insert queries per second\.  | Aurora MySQL | 
|  `LoginFailures`  | The average number of failed login attempts per second\.  | Aurora MySQL | 
|  `MaximumUsedTransactionIDs`  | The age of the oldest unvacuumed transaction ID, in transactions\. If this value reaches 2,146,483,648 \(2^31 \- 1,000,000\), the database is forced into read\-only mode, to avoid transaction ID wraparound\. For more information, see [Preventing Transaction ID Wraparound Failures](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND) in the PostgreSQL documentation\.  | Aurora PostgreSQL | 
|  `NetworkReceiveThroughput`  |  The amount of network throughput received from clients by each instance in the Aurora MySQL DB cluster, in bytes per second\. This throughput doesn't include network traffic between instances in the Aurora DB cluster and the cluster volume\.  | Aurora MySQL and Aurora PostgreSQL  | 
|  `NetworkThroughput`  |  The amount of network throughput both received from and transmitted to clients by each instance in the Aurora MySQL DB cluster, in bytes per second\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `NetworkTransmitThroughput`  | The amount of network throughput sent to clients by each instance in the Aurora DB cluster, in bytes per second\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `Queries`  | The average number of queries executed per second\. | Aurora MySQL | 
|  `RDSToAuroraPostgreSQLReplicaLag`  | The amount of lag in seconds when replicating updates from the primary RDS PostgreSQL instance to other nodes in the cluster\.  | Aurora PostgreSQL | 
|  `ReadIOPS`  |  The average number of disk I/O operations per second\. Aurora with PostgreSQL compatibility reports read and write IOPS separately, in 1\-minute intervals\.  | Aurora PostgreSQL | 
|  `ReadLatency`  |  The average amount of time taken per disk I/O operation\.  | Aurora PostgreSQL | 
|  `ReadThroughput`  |  The average number of bytes read from disk per second\.  | Aurora PostgreSQL | 
|  `ResultSetCacheHitRatio`  |  The percentage of requests that are served by the Resultset cache\.  | Aurora MySQL | 
|  `SelectLatency`  |  The amount of latency for select queries, in milliseconds\.  | Aurora MySQL | 
|  `SelectThroughput`  |  The average number of select queries per second\.  | Aurora MySQL | 
| SnapshotStorageUsed |   The total amount of backup storage in bytes consumed by all Aurora snapshots for an Aurora DB cluster outside its backup retention window\. Included in the total reported by the `TotalBackupStorageBilled` metric\. Computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora Backup Storage Usage](aurora-storage-backup.md)\.   | Aurora MySQL and Aurora PostgreSQL | 
|  `SwapUsage`  |  The amount of swap space used on the Aurora PostgreSQL DB instance\.  | Aurora PostgreSQL | 
| TotalBackupStorageBilled |   The total amount of backup storage in bytes for which you are billed for a given Aurora DB cluster\. Includes the backup storage measured by the `BackupRetentionPeriodStorageUsed` and `SnapshotStorageUsed` metrics\. Computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora Backup Storage Usage](aurora-storage-backup.md)\.   | Aurora MySQL and Aurora PostgreSQL | 
|  `TransactionLogsDiskUsage`  |  The amount of disk space occupied by transaction logs on the Aurora PostgreSQL DB instance\.  | Aurora PostgreSQL | 
|  `UpdateLatency`  |  The amount of latency for update queries, in milliseconds\.  | Aurora MySQL | 
|  `UpdateThroughput`  |  The average number of update queries per second\.  | Aurora MySQL | 
|  `AuroraVolumeBytesLeftTotal`  |  The remaining available space for the cluster volume, measured in bytes\. As the cluster volume grows, this value decreases\. If it reaches zero, the cluster reports an out\-of\-space error\. This value is simpler and more reliable to monitor than `VolumeBytesUsed` when you want to detect whether your Aurora cluster is approaching the 64 TiB size limit\. `AuroraVolumeBytesLeftTotal` takes into account storage used for internal housekeeping and other allocations that don't affect your storage billing\.   | Aurora MySQL | 
| `VolumeBytesUsed` |  The amount of storage used by your Aurora DB instance, in bytes\. This value affects the cost of the Aurora DB cluster \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\.  This value doesn't reflect some internal storage allocations that don't affect storage billing\. Thus, you can anticipate out\-of\-space issues more accurately by testing whether `AuroraVolumeBytesLeftTotal` is approaching zero instead of comparing `VolumeBytesUsed` against the 64 TiB storage limit\.   | Aurora MySQL and Aurora PostgreSQL | 
|  `VolumeReadIOPs`  |  The number of billed read I/O operations from a cluster volume, reported at 5\-minute intervals\. Billed read operations are calculated at the cluster volume level, aggregated from all instances in the Aurora DB cluster, and then reported at 5\-minute intervals\. The value is calculated by taking the value of the **Read operations** metric over a 5\-minute period\. You can determine the amount of billed read operations per second by taking the value of the **Billed read operations** metric and dividing by 300 seconds\. For example, if the **Billed read operations** returns 13,686, then the billed read operations per second is 45 \(13,686 / 300 = 45\.62\)\.  You accrue billed read operations for queries that request database pages that aren't in the buffer cache and therefore must be loaded from storage\. You might see spikes in billed read operations as query results are read from storage and then loaded into the buffer cache\.   | Aurora MySQL and Aurora PostgreSQL | 
|  `VolumeWriteIOPs`  |  The number of write disk I/O operations to the cluster volume, reported at 5\-minute intervals\. See the description of VolumeReadIOPS above for a detailed description of how billed write operations are calculated\.  | Aurora MySQL and Aurora PostgreSQL | 
|  `WriteIOPS`  |  The average number of disk I/O operations per second\.  Aurora PostgreSQL reports read and write IOPS separately, on 1\-minute intervals\.  | Aurora PostgreSQL | 
|  `WriteLatency`  |  The average amount of time taken per disk I/O operation\.  | Aurora PostgreSQL | 
|  `WriteThroughput`  |  The average number of bytes written to disk per second\.  | Aurora PostgreSQL | 

## Viewing Aurora Metrics in the Amazon RDS Console<a name="Aurora.Monitoring.Metrics.RDS"></a>

To monitor the health and performance of your Aurora DB cluster, you can view some, but not all, of the metrics provided by Amazon Aurora in the Amazon RDS console\. For a detailed list of Aurora metrics available to the Amazon RDS console, see [Aurora Metrics Available in the Amazon RDS Console](#Aurora.Monitoring.Metrics.RDSAvailability)\.

**To view Aurora metrics in the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the name of the DB instance you want to monitor to see its details\. 

1. In the Cloudwatch section, choose one of the following options from **Monitoring** for how you want to view your metrics:
   + **Cloudwatch** – Shows a summary of CloudWatch metrics\. Each metric includes a graph showing the metric monitored over a specific time span\. For more information, see [Monitoring with Amazon CloudWatch](MonitoringOverview.md#monitoring-cloudwatch)\.
   + **Enhanced monitoring** – Shows a summary of OS metrics available to an Aurora DB instance with Enhanced Monitoring enabled\. Each metric includes a graph showing the metric monitored over a specific time span\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.
   + **OS process list** – Shows the processes running on the DB instance or DB cluster and their related metrics including CPU percentage, memory usage, and so on\.   
![\[RDS metrics viewing options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraMetrics01.png)

1. The following image shows the metrics view with **Enhanced monitoring** selected\.  
![\[Latest Metrics View\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraMetrics02.png)

## Aurora Metrics Available in the Amazon RDS Console<a name="Aurora.Monitoring.Metrics.RDSAvailability"></a>

Not all of the metrics provided by Amazon Aurora are available to you in the Amazon RDS console\. You can view them using other tools, however, such as the AWS CLI and CloudWatch API\. In addition, some of the metrics that are available in the Amazon RDS console are either shown only for specific instance classes, or with different names and different units of measurement\. 

The following Aurora metrics are not available in the Amazon RDS console:
+ `AuroraBinlogReplicaLag`
+ `DeleteLatency`
+ `DeleteThroughput`
+ `EngineUptime`
+ `InsertLatency`
+ `InsertThroughput`
+ `NetworkThroughput`
+ `Queries`
+ `UpdateLatency`
+ `UpdateThroughput`

In addition, some Aurora metrics are either shown only for specific instance classes, or only for DB instances, or with different names and different units of measurement:
+ The `CPUCreditBalance` and `CPUCreditUsage` metrics are displayed only for `db.t2.small` and `db.t2.medium` instances
+ The following metrics that are displayed with different names, as listed:    
<a name="aurora_cloudwatch_metrics_renamed"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Monitoring.html)
+ The following metrics apply to an entire Aurora DB cluster, but are displayed only when viewing DB instances for an Aurora DB cluster in the Amazon RDS console:
  + `VolumeBytesUsed`
  + `VolumeReadIOPs`
  + `VolumeWriteIOPs`
+ The following metrics are displayed in megabytes, instead of bytes, in the Amazon RDS console:
  + `FreeableMemory`
  + `FreeLocalStorage`
  + `NetworkReceiveThroughput`
  + `NetworkTransmitThroughput`

### Latest Metrics View<a name="Aurora.Monitoring.Metrics.RDSAvailability.LMV"></a>

You can view a subset of categorized Aurora metrics in the Latest Metrics view of the Amazon RDS console\. The following table lists the categories and associated metrics displayed in the Amazon RDS console for an Aurora instance\.


| Category | Metrics | 
| --- | --- | 
| SQL |  `ActiveTransactions` `BlockedTransactions` `BufferCacheHitRatio` `CommitLatency` `CommitThroughput` `DatabaseConnections` `DDLLatency` `DDLThroughput` `Deadlocks` `DMLLatency` `DMLThroughput` `LoginFailures` `ResultSetCacheHitRatio` `SelectLatency` `SelectThroughput`  | 
| System |  `AuroraReplicaLag` `AuroraReplicaLagMaximum` `AuroraReplicaLagMinimum` `CPUCreditBalance` `CPUCreditUsage` `CPUUtilization` `FreeableMemory` `FreeLocalStorage` `NetworkReceiveThroughput`  | 
| Deployment |  `AuroraReplicaLag` `BufferCacheHitRatio` `ResultSetCacheHitRatio` `SelectThroughput`  | 

**Note**  
The **Failed SQL Statements** metric, displayed under the **SQL** category of the Latest Metrics view in the Amazon RDS console, does not apply to Amazon Aurora\.