# Viewing metrics in the Amazon RDS console<a name="USER_Monitoring"></a>

Amazon RDS integrates with Amazon CloudWatch to display a variety of Aurora DB cluster metrics in the RDS console\. Some metrics are apply at the cluster level, whereas others apply at the instance level\. For descriptions of the instance\-level and cluster\-level metrics, see [Metrics reference for Amazon Aurora](metrics-reference.md)\.

The **Monitoring** tab for your Aurora DB cluster shows the following categories of metrics:
+ **CloudWatch** – Shows the Amazon CloudWatch metrics for Aurora that you can access in the RDS console\. You can also access these metrics in the CloudWatch console\. Each metric includes a graph that shows the metric monitored over a specific time span\. For a list of CloudWatch metrics, see [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md)\.
+ **Enhanced monitoring** – Shows a summary of operating\-system metrics when your Aurora DB cluster has turned on Enhanced Monitoring\. RDS delivers the metrics from Enhanced Monitoring to your Amazon CloudWatch Logs account\. Each OS metric includes a graph showing the metric monitored over a specific time span\. For an overview, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\. For a list of Enhanced Monitoring metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.
+ **OS Process list** – Shows details for each process running in your DB cluster\.
+ **Performance Insights** – Opens the Amazon RDS Performance Insights dashboard for a DB instance in your Aurora DB cluster\. Performance Insights isn't supported at the cluster level\. For an overview of Performance Insights, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\. For a list of Performance Insights metrics, see [ Amazon CloudWatch metrics for Performance InsightsCloudWatch metrics for Performance Insights  Performance Insights automatically publishes metrics to Amazon CloudWatch\. The same data can be queried from Performance Insights, but having the metrics in CloudWatch makes it easy to add CloudWatch alarms\. It also makes it easy to add the metrics to existing CloudWatch Dashboards\. 


| Metric | Description | 
| --- | --- | 
|  DBLoad  |  The number of active sessions for the DB engine\. Typically, you want the data for the average number of active sessions\. In Performance Insights, this data is queried as `db.load.avg`\.  | 
|  DBLoadCPU  |  The number of active sessions where the wait event type is CPU\. In Performance Insights, this data is queried as `db.load.avg`, filtered by the wait event type `CPU`\.  | 
|  DBLoadNonCPU  |  The number of active sessions where the wait event type is not CPU\.  |   These metrics are published to CloudWatch only if there is load on the DB instance\.  You can examine these metrics using the CloudWatch console, the AWS CLI, or the CloudWatch API\. For example, you can get the statistics for the `DBLoad` metric by running the [get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command\. 

```
aws cloudwatch get-metric-statistics \
    --region us-west-2 \
    --namespace AWS/RDS \
    --metric-name DBLoad  \
    --period 60 \
    --statistics Average \
    --start-time 1532035185 \
    --end-time 1532036185 \
    --dimensions Name=DBInstanceIdentifier,Value=db-loadtest-0
``` This example generates output similar to the following\. 

```
{
		"Datapoints": [
		{
		"Timestamp": "2021-07-19T21:30:00Z",
		"Unit": "None",
		"Average": 2.1
		},
		{
		"Timestamp": "2021-07-19T21:34:00Z",
		"Unit": "None",
		"Average": 1.7
		},
		{
		"Timestamp": "2021-07-19T21:35:00Z",
		"Unit": "None",
		"Average": 2.8
		},
		{
		"Timestamp": "2021-07-19T21:31:00Z",
		"Unit": "None",
		"Average": 1.5
		},
		{
		"Timestamp": "2021-07-19T21:32:00Z",
		"Unit": "None",
		"Average": 1.8
		},
		{
		"Timestamp": "2021-07-19T21:29:00Z",
		"Unit": "None",
		"Average": 3.0
		},
		{
		"Timestamp": "2021-07-19T21:33:00Z",
		"Unit": "None",
		"Average": 2.4
		}
		],
		"Label": "DBLoad"
		}
``` For more information about CloudWatch, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.  ](USER_PerfInsights.Cloudwatch.md)\.

**To view metrics for your Aurora DB cluster in the RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the Aurora DB cluster that you want to monitor\.

   The database page appears\. The following example shows an Amazon Aurora PostgreSQL database named `apga`\.  
![\[Database page with monitoring tab shown\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/cluster-with-monitoring-tab.png)

1. Scroll down and choose **Monitoring**\.

   The monitoring section appears\. By default, CloudWatch metrics are shown\. For descriptions of these metrics, see [ Amazon CloudWatch metrics for Amazon AuroraCloudWatch metrics for Aurora  The `AWS/RDS` namespace includes the following metrics that apply to database entities running on Amazon Aurora\. Some metrics apply to either Aurora MySQL, Aurora PostgreSQL, or both\. Furthermore, some metrics are specific to a DB cluster, primary DB instance, replica DB instance, or all DB instances\. For Aurora global database metrics, see [Amazon CloudWatch metrics for write forwarding](aurora-global-database-write-forwarding.md#aurora-global-database-write-forwarding-cloudwatch)\. For Aurora parallel query metrics, see [Monitoring parallel query](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-monitoring)\.    Cluster\-level metrics for Amazon Aurora  The following table describes metrics that are specific to Aurora clusters\. 


**Amazon Aurora cluster\-level metrics**  

| Metric | Console name | Description | Applies to | Units | 
| --- | --- | --- | --- | --- | 
|  `AuroraGlobalDBDataTransferBytes`  |  **Aurora Global DB Data Transfer Bytes \(Bytes\)**  |  In an Aurora Global Database, the amount of redo log data transferred from the master AWS Region to a secondary AWS Region\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `AuroraGlobalDBProgressLag`  |    |  In an Aurora Global Database, the measure of how far the secondary cluster is behind the primary cluster for both user transactions and system transactions\.  |  Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraGlobalDBReplicatedWriteIO`  |  **Aurora Global DB Replicated Write IO**  |  In an Aurora Global Database, the number of write I/O operations replicated from the primary AWS Region to the cluster volume in a secondary AWS Region\. The billing calculations for the secondary AWS Regions in a global database use `VolumeWriteIOPS` to account for writes performed within the cluster\. The billing calculations for the primary AWS Region in a global database use `VolumeWriteIOPS` to account for the write activity within that cluster, and `AuroraGlobalDBReplicatedWriteIO` to account for cross\-Region replication within the global database\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `AuroraGlobalDBReplicationLag`  |  **Aurora Global DB Replication Lag \(Milliseconds\)**  |  For an Aurora Global Database, the amount of lag when replicating updates from the primary AWS Region\.  |  Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraGlobalDBRPOLag`  |    |  In an Aurora Global Database, the recovery point objective \(RPO\) lag time\. This metric measures how far the secondary cluster is behind the primary cluster for user transactions\.  |  Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraVolumeBytesLeftTotal`  |    |  The remaining available space for the cluster volume\. As the cluster volume grows, this value decreases\. If it reaches zero, the cluster reports an out\-of\-space error\.  If you want to detect whether your Aurora cluster is approaching the size limit of 128 tebibytes \(TiB\), this value is simpler and more reliable to monitor than `VolumeBytesUsed`\. `AuroraVolumeBytesLeftTotal` takes into account storage used for internal housekeeping and other allocations that don't affect your storage billing\.  This parameter is available in more recent Aurora versions\. For Aurora MySQL with MySQL 5\.6 compatibility, use Aurora version 1\.19\.5 or higher\. For Aurora MySQL with MySQL 5\.7 compatibility, use Aurora version 2\.04\.5 or higher\.  |  Aurora MySQL  |  Bytes  | 
|  `BacktrackChangeRecordsCreationRate`  |  **Backtrack Change Records Creation Rate \(Count\)**  |  The number of backtrack change records created over 5 minutes for your DB cluster\.  |  Aurora MySQL  |  Count per 5 minutes  | 
|  `BacktrackChangeRecordsStored`  |  **Backtrack Change Records Stored \(Count\)**  |  The number of backtrack change records used by your DB cluster\.  |  Aurora MySQL  |  Count  | 
|  `BackupRetentionPeriodStorageUsed`  |  **Backup Retention Period Storage Used \(GiB\)**  |  The total amount of backup storage used to support the point\-in\-time restore feature within the Aurora DB cluster's backup retention window\. This amount is included in the total reported by the `TotalBackupStorageBilled` metric\. It is computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `ServerlessDatabaseCapacity`  |    |  The current capacity of an Aurora Serverless v1 DB cluster\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `SnapshotStorageUsed`  |  **Snapshot Storage Used \(GiB\)**  |  The total amount of backup storage consumed by all Aurora snapshots for an Aurora DB cluster outside its backup retention window\. This amount is included in the total reported by the `TotalBackupStorageBilled` metric\. It is computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `TotalBackupStorageBilled`  |  **Total Backup Storage Used \(GiB\)**  |  The total amount of backup storage in bytes for which you are billed for a given Aurora DB cluster\. The metric includes the backup storage measured by the `BackupRetentionPeriodStorageUsed` and `SnapshotStorageUsed` metrics\. This metric is computed separately for each Aurora cluster\. For instructions, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `VolumeBytesUsed`  |  **Volume Bytes Used \(GiB\)**  |  The amount of storage used by your Aurora DB instance\. This value affects the cost of the Aurora DB cluster \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\.  This value doesn't reflect some internal storage allocations that don't affect storage billing\. Thus, you can anticipate out\-of\-space issues more accurately by testing whether `AuroraVolumeBytesLeftTotal` is approaching zero instead of comparing `VolumeBytesUsed` against the storage limit of 128 TiB\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `VolumeReadIOPs`  |  **Volume Read IOPS \(Count\)**  |  The number of billed read I/O operations from a cluster volume within a 5\-minute interval\. Billed read operations are calculated at the cluster volume level, aggregated from all instances in the Aurora DB cluster, and then reported at 5\-minute intervals\. The value is calculated by taking the value of the **Read operations** metric over a 5\-minute period\. You can determine the amount of billed read operations per second by taking the value of the **Billed read operations** metric and dividing by 300 seconds\. For example, if the **Billed read operations** returns 13,686, then the billed read operations per second is 45 \(13,686 / 300 = 45\.62\)\.  You accrue billed read operations for queries that request database pages that aren't in the buffer cache and must be loaded from storage\. You might see spikes in billed read operations as query results are read from storage and then loaded into the buffer cache\.   If your Aurora MySQL cluster uses parallel query, you might see an increase in `VolumeReadIOPS` values\. Parallel queries don't use the buffer pool\. Thus, although the queries are fast, this optimized processing can result in an increase in read operations and associated charges\.    |  Aurora MySQL and Aurora PostgreSQL  |  Count per 5 minutes  | 
|  `VolumeWriteIOPs`  |  **Volume Write IOPS \(Count\)**  |  The number of write disk I/O operations to the cluster volume, reported at 5\-minute intervals\. For a detailed description of how billed write operations are calculated, see `VolumeReadIOPs`\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per 5 minutes  |    Instance\-level metrics for Amazon Aurora  The following instance\-specific CloudWatch metrics apply to all Aurora MySQL and Aurora PostgreSQL instances unless noted otherwise\. 


**Amazon Aurora instance\-level metrics**  

| Metric | Console Name | Description | Applies to | Units | 
| --- | --- | --- | --- | --- | 
|  `AbortedClients`  |    | The number of client connections that have not been closed properly\. |  Aurora MySQL  |  Count  | 
|  `ActiveTransactions`  |  **Active Transactions \(Count\)**  |  The average number of current transactions executing on an Aurora database instance per second\.  By default, Aurora doesn't enable this metric\. To begin measuring this value, set `innodb_monitor_enable='all'` in the DB parameter group for a specific DB instance\.   |  Aurora MySQL  |  Count per second  | 
|  `AuroraBinlogReplicaLag`  |  **Aurora Binlog Replica Lag \(Seconds\)**  |  The amount of time that a binary log replica DB cluster running on Aurora MySQL\-Compatible Edition lags behind the binary log replication source\. A lag means that the source is generating records faster than the replica can apply them\. This metric reports different values depending on the engine version: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.AuroraMySQL.Monitoring.Metrics.html) You can use this metric to monitor errors and replica lag in a cluster that acts as a binary log replica\. The metric value indicates the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.AuroraMySQL.Monitoring.Metrics.html) Because binary log replication only occurs on the writer instance of the cluster, we recommend using the version of this metric associated with the WRITER role\. For more information about administering replication, see [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\. For more information about troubleshooting, see [Amazon Aurora MySQL replication issues](CHAP_Troubleshooting.md#CHAP_Troubleshooting.MySQL)\.  |  Primary for Aurora MySQL  |  Seconds  | 
|  `AuroraReplicaLag`  |  **Aurora Replica Lag \(Milliseconds\)**  |  For an Aurora replica, the amount of lag when replicating updates from the primary instance\.  |  Replica for Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraReplicaLagMaximum`  |  **Replica Lag Maximum \(Milliseconds\)**  |  The maximum amount of lag between the primary instance and each Aurora DB instance in the DB cluster\.  |  Primary for Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `AuroraReplicaLagMinimum`  |  **Replica Lag Minimum \(Milliseconds\)**  |  The minimum amount of lag between the primary instance and each Aurora DB instance in the DB cluster\.  |  Primary for Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `BacktrackWindowActual`  |  **Backtrack Window Actual \(Minutes\)**  |  The difference between the target backtrack window and the actual backtrack window\.  |  Primary for Aurora MySQL  |  Minutes  | 
|  `BacktrackWindowAlert`  |  **Backtrack Window Alert \(Count\)**  |  The number of times that the actual backtrack window is smaller than the target backtrack window for a given period of time\.  |  Primary for Aurora MySQL  |  Count  | 
|  `BlockedTransactions`  |  **Blocked Transactions \(Count\)**  |  The average number of transactions in the database that are blocked per second\.  |  Aurora MySQL  |  Count per second  | 
|  `BufferCacheHitRatio`  |  **Buffer Cache Hit Ratio \(Percent\)**  |  The percentage of requests that are served by the buffer cache\.  |  Aurora MySQL and Aurora PostgreSQL  |  Percentage  | 
|  `CommitLatency`  |  **Commit Latency \(Milliseconds\)**  |  The average duration of commit operations\.  |  Aurora MySQL and Aurora PostgreSQL  |  Milliseconds  | 
|  `CommitThroughput`  |  **Commit Throughput \(Count/Second\)**  |  The average number of commit operations per second\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per second  | 
|  `CPUCreditBalance`  |  **CPU Credit Balance \(Count\)**  |  The number of CPU credits that an instance has accumulated, reported at 5\-minute intervals\. You can use this metric to determine how long a DB instance can burst beyond its baseline performance level at a given rate\. This metric applies only to `db.t2.small` and `db.t2.medium` instances for Aurora MySQL, and to `db.t3` instances for Aurora PostgreSQL\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `CPUCreditUsage`  |  **CPU Credit Usage \(Count\)**  |  The number of CPU credits consumed during the specified period, reported at 5\-minute intervals\. This metric measures the amount of time during which physical CPUs have been used for processing instructions by virtual CPUs allocated to the DB instance\.  This metric applies only to `db.t2.small` and `db.t2.medium` instances for Aurora MySQL, and to `db.t3` instances for Aurora PostgreSQL\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `CPUUtilization`  |  **CPU Utilization \(Percent\)**  |  The percentage of CPU used by an Aurora DB instance\.  |  Aurora MySQL and Aurora PostgreSQL  |  Percentage  | 
|  `DatabaseConnections`  |  **DB Connections \(Count\)**  |  The number of client network connections to the database instance\. The number of database sessions can be higher than the metric value because the metric value doesn't include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.AuroraMySQL.Monitoring.Metrics.html) |  Aurora MySQL and Aurora PostgreSQL  |  Count  | 
|  `DDLLatency`  |  **DDL Latency \(Milliseconds\)**  |  The average duration of requests such as example, create, alter, and drop requests\.  |  Aurora MySQL  |  Milliseconds  | 
|  `DDLThroughput`  |  **DDL \(Count/Second\)**  |  The average number of DDL requests per second\.  |  Aurora MySQL  |  Count per second  | 
|  `Deadlocks`  |  **Deadlocks \(Count\)**  |  The average number of deadlocks in the database per second\.  |  Aurora MySQL and Aurora PostgreSQL  |  Count per second  | 
|  `DeleteLatency`  |  **Delete Latency \(Milliseconds\)**  |  The average duration of delete operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `DeleteThroughput`  |  **Delete Throughput \(Count/Second\)**  |  The average number of delete queries per second\.  |  Aurora MySQL  |  Count per second  | 
|  `DiskQueueDepth`  |  **Queue Depth \(Count\)**  |  The number of outstanding read/write requests waiting to access the disk\.  |  Aurora PostgreSQL  |  Count  | 
|  `DMLLatency`  |  **DML Latency \(Milliseconds\)**  |  The average duration of inserts, updates, and deletes\.  |  Aurora MySQL  |  Milliseconds  | 
|  `DMLThroughput`  |  **DML Throughput \(Count/Second\)**  |  The average number of inserts, updates, and deletes per second\.  |  Aurora MySQL  |  Count per second  | 
| EBSByteBalance% |  **EBS Byte Balance \(Percent\)**  |  The percentage of throughput credits remaining in the burst bucket of your RDS database\. This metric is available for basic monitoring only\. To find the instance sizes that support this metric, see the instance sizes with an asterisk \(\*\) in the [EBS optimized by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html#current) table in *Amazon EC2 User Guide for Linux Instances*\. The `Sum` statistic is not applicable to this metric\.  |  Aurora MySQL and Aurora PostgreSQL  |  Percent  | 
| EBSIOBalance% |  **EBS IO Balance \(Percent\)**  |  The percentage of I/O credits remaining in the burst bucket of your RDS database\. This metric is available for basic monitoring only\.  To find the instance sizes that support this metric, see the instance sizes with an asterisk \(\*\) in the [EBS optimized by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html#current) table in *Amazon EC2 User Guide for Linux Instances*\. The `Sum` statistic is not applicable to this metric\. This metric is different from `BurstBalance`\. To learn how to use this metric, see [Improving application performance and reducing costs with Amazon EBS\-Optimized Instance burst capability](http://aws.amazon.com/blogs/compute/improving-application-performance-and-reducing-costs-with-amazon-ebs-optimized-instance-burst-capability/)\.   |  Aurora MySQL and Aurora PostgreSQL  |  Percent  | 
|  `EngineUptime`  |  **Engine Uptime \(Seconds\)**  |  The amount of time that the instance has been running\.  |  Aurora MySQL and Aurora PostgreSQL  |  Seconds  | 
|  `FreeableMemory`  |  **Freeable Memory \(MB\)**  |  The amount of available random access memory\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `FreeLocalStorage`  |    |  The amount of local storage available\. Unlike for other DB engines, for Aurora DB instances this metric reports the amount of storage available to each DB instance\. This value depends on the DB instance class \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\. You can increase the amount of free storage space for an instance by choosing a larger DB instance class for your instance\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `InsertLatency`  |  **Insert Latency \(Milliseconds\)**  |  The average duration of insert operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `InsertThroughput`  |  **Insert Throughput \(Count/Second\)**  |  The average number of insert operations per second\.  |  Aurora MySQL  |  Count per second  | 
|  `LoginFailures`  |  **Login Failures \(Count\)**  |  The average number of failed login attempts per second\.  |  Aurora MySQL  |  Count per second  | 
|  `MaximumUsedTransactionIDs`  |  **MaximumUsedTransactionIDs**  |  The age of the oldest unvacuumed transaction ID, in transactions\. If this value reaches 2,146,483,648 \(2^31 \- 1,000,000\), the database is forced into read\-only mode, to avoid transaction ID wraparound\. For more information, see [Preventing transaction ID wraparound failures](https://www.postgresql.org/docs/current/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND) in the PostgreSQL documentation\.  |  Aurora PostgreSQL  |  Count  | 
|  `NetworkReceiveThroughput`  |  **Network Receive Throughput \(MB/Second\)**  |  The amount of network throughput received from clients by each instance in the Aurora MySQL DB cluster\. This throughput doesn't include network traffic between instances in the Aurora DB cluster and the cluster volume\.  |  Aurora MySQL and Aurora PostgreSQL   |  Bytes per second \(console shows Megabytes per second\)  | 
|  `NetworkThroughput`  |  **Network Throughput \(Byte/Second\)**  |  The amount of network throughput both received from and transmitted to clients by each instance in the Aurora MySQL DB cluster\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second  | 
|  `NetworkTransmitThroughput`  |  **Network Transmit Throughput \(MB/Second\)**  |  The amount of network throughput sent to clients by each instance in the Aurora DB cluster\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second \(console shows Megabytes per second\)  | 
|  `NumBinaryLogFiles`  |    |  The number of binlog files generated\.  | Aurora MySQL |  Count  | 
|  `Queries`  |  **Queries \(Count/Second\)**  |  The average number of queries executed per second\.  |  Aurora MySQL  |  Count per second  | 
|  `RDSToAuroraPostgreSQLReplicaLag`  |    |  The lag when replicating updates from the primary RDS PostgreSQL instance to other nodes in the cluster\.  |  Replica for Aurora PostgreSQL  |  Seconds  | 
|  `ReadIOPS`  |  **Read IOPS \(Count/Second\)**  |  The average number of disk I/O operations per second\. Aurora PostgreSQL\-Compatible Edition reports read and write IOPS separately, in 1\-minute intervals\.  |  Aurora PostgreSQL  |  Count per second  | 
|  `ReadLatency`  |  **Read Latency \(Milliseconds\)**  |  The average amount of time taken per disk I/O operation\.  |  Aurora MySQL and Aurora PostgreSQL  |  Seconds  | 
|  `ReadThroughput`  |  **Read Throughput \(MB/Second\)**  |  The average number of bytes read from disk per second\.  |  Aurora PostgreSQL  |  Bytes per second  | 
|  `ReplicationSlotDiskUsage`  |    |  The amount of disk space consumed by replication slot files\.   |  Aurora PostgreSQL  |  Bytes  | 
|  `ResultSetCacheHitRatio`  |  **Result Set Cache Hit Ratio \(Percent\)**  |  The percentage of requests that are served by the Resultset cache\.  |  Aurora MySQL  |  Percentage  | 
|  `RollbackSegmentHistoryListLength`  |    |  The undo logs that record committed transactions with delete\-marked records\. These records are scheduled to be processed by the InnoDB purge operation\.  |  Aurora MySQL  |  Count  | 
|  `RowLockTime`  |    |  The total time spent acquiring row locks for InnoDB tables\.  |  Aurora MySQL  |  Milliseconds  | 
|  `SelectLatency`  |  **Select Latency \(Milliseconds\)**  |  The average amount of time for select operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `SelectThroughput`  |  **Select Throughput \(Count/Second\)**  |  The average number of select queries per second\.  |  Aurora MySQL  |  Count per second  | 
|  `StorageNetworkReceiveThroughput`  |  **Network Receive Throughput \(MB/Second\)**  |  The amount of network throughput received from the Aurora storage subsystem by each instance in the DB cluster\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second   | 
|  `StorageNetworkThroughput`  |  **Network Throughput \(Byte/Second\)**  |  The amount of network throughput received from and sent to the Aurora storage subsystem by each instance in the Aurora MySQL DB cluster\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second   | 
|  `StorageNetworkTransmitThroughput`  |  **Network Transmit Throughput \(MB/Second\)**  |  The amount of network throughput sent to the Aurora storage subsystem by each instance in the Aurora MySQL DB cluster\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes per second   | 
|  `SumBinaryLogSize`  |    |  The total size of the binlog files\.  |  Aurora MySQL  |  Bytes  | 
|  `SwapUsage`  |  **Swap Usage \(MB\)**  |  The amount of swap space used\. This metric is available for the Aurora PostgreSQL DB instance classes `db.t3.medium`, `db.t3.large`, `db.r4.large`, `db.r4.xlarge`, `db.r5.large`, `db.r5.xlarge`, `db.r6g.large`, and `db.r6g.xlarge`\. For Aurora MySQL, this metric applies only to `db.t*` DB instance classes\.  |  Aurora MySQL and Aurora PostgreSQL  |  Bytes  | 
|  `TransactionLogsDiskUsage`  |  **Transaction Logs Disk Usage \(MB\)**  |  The amount of disk space consumed by transaction logs on the Aurora PostgreSQL DB instance\. This metric is generated only when Aurora PostgreSQL is using logical replication or AWS Database Migration Service\. By default, Aurora PostgreSQL uses log records, not transaction logs\. When transaction logs aren't in use, the value for this metric is `-1`\.  |  Primary for Aurora PostgreSQL  |  Bytes  | 
|  `UpdateLatency`  |  **Update Latency \(Milliseconds\)**  |  The average amount of taken taken for update operations\.  |  Aurora MySQL  |  Milliseconds  | 
|  `UpdateThroughput`  |  **Update Throughput \(Count/Second\)**  |  The average number of updates per second\.  |  Aurora MySQL  |  Count per second  | 
|  `WriteIOPS`  |  **Volume Write IOPS \(Count\)**  |  The number of Aurora storage write records generated per second\. This is more or less the number of log records generated by the database\. These do not correspond to 8K page writes, and do not correspond to network packets sent\.  |  Aurora PostgreSQL  |  Count per second  | 
|  `WriteLatency`  |  **Write Latency \(Milliseconds\)**  |  The average amount of time taken per disk I/O operation\.  |  Aurora MySQL and Aurora PostgreSQL  |  Seconds  | 
|  `WriteThroughput`  |  **Write Throughput \(MB/Second\)**  |  The average number of bytes written to persistent storage every second\.  |  Aurora PostgreSQL  |  Bytes per second  |   ](Aurora.AuroraMySQL.Monitoring.Metrics.md)\.  
![\[Database page with monitoring tab shown\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/cluster-monitoring-subpage.png)

1. Choose **Monitoring** to see the metric categories\.  
![\[Monitoring options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/monitoring-options.png)

1. Choose the category of metrics that you want to see\.

   The following example shows Enhanced Monitoring metrics\. For descriptions of these metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.  
![\[Enhanced Monitoring\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/cluster-em-metrics.png)
**Tip**  
To choose the time range of the metrics represented by the graphs, you can use the time range list\.  
To bring up a more detailed view, you can choose any graph\. You can also apply metric\-specific filters to the data\. 