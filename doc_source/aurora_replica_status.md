# aurora\_replica\_status<a name="aurora_replica_status"></a>

Displays the status of all Aurora PostgreSQL reader nodes\. 

## Syntax<a name="aurora_replica_status-syntax"></a>

 

```
aurora_replica_status()
```

## Arguments<a name="aurora_replica_status-arguments"></a>

None

## Return type<a name="aurora_replica_status-return-type"></a>

SETOF record with the following columns:
+ `server_id` – The DB instance ID \(identifier\)\. 
+ `session_id` – A unique identifier for the current session, returned for primary instance and reader instances as follows:
  + For the primary instance, `session_id` is always ``MASTER_SESSION_ID’`\.
  + For reader instances, `session_id` is always the `UUID` \(universally unique identifier\) of the reader instance\.
+ `durable_lsn` – The log sequence number \(LSN\) that's been saved in storage\.
  + For the primary volume, the primary volume durable LSN \(VDL\) that's currently in effect\.
  + For any secondary volumes, the VDL of the primary up to which the secondary has successfully been applied\.
**Note**  
A log sequence number \(LSN\) is a unique sequential number that identifies a record in the database transaction log\. LSNs are ordered such that a larger LSN represents a transaction that's occurred later in the sequence\.
+ `highest_lsn_rcvd` – The highest \(most recent\) LSN received by the DB instance from the writer DB instance\.
+ `current_read_lsn` – The LSN of the most recent snapshot that's been applied to all readers\. 
+ `cur_replay_latency_in_usec` – The number of microseconds that it's expected to take to replay the log on the secondary\. 
+ `active_txns` – The number of currently active transactions\.
+ `is_current` – Not used\.
+ `last_transport_error` – Last replication error code\.
+ `last_error_timestamp` – Timestamp of last replication error\.
+ `last_update_timestamp` – Timestamp of last update to replica status\.
+ `feedback_xmin` – The hot standby feedback\_xmin of the replica\. The minimum \(oldest\) active transaction ID used by the DB instance\.
+ `feedback_epoch` – The epoch the DB instance uses when it generates hot standby information\.
+ `replica_lag_in_msec` – Time that reader instance lags behind writer writer instance, in milliseconds\.
+ `log_stream_speed_in_kib_per_second` – The log stream throughput in kilobytes per second\.
+ `log_buffer_sequence_number` – The log buffer sequence number\.
+ `oldest_read_view_trx_id` – Not used\.
+ `oldest_read_view_lsn` – The oldest LSN used by the DB instance to read from storage\.
+ `pending_read_ios` – The outstanding page reads that are still pending on replica\. 
+ `read_ios` – The total number of page reads on replica\.
+ `iops` – Not used\.
+ `cpu` – CPU usage by the replica process\. Note that this isn't CPU usage by the instance but rather the process\. For information about CPU usage by the instance, see [Instance\-level metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md#Aurora.AuroraMySQL.Monitoring.Metrics.instances)\.

## Usage notes<a name="aurora_replica_status-usage-notes"></a>

The `aurora_replica_status` function returns values from an Aurora PostgreSQL DB cluster's replica status manager\. You can use this function to obtain information about the status of replication on your Aurora PostgreSQL DB cluster, including metrics for all DB instances in your Aurora DB cluster\. For example, you can do the following:
+ **Get information about the type of instance \(writer, reader\) in the Aurora PostgreSQL DB cluster** – You can obtain this information by checking the values of the following columns: 
  + `server_id` – Contains the name of the instance that you specified when you created the instance\. In some cases, such as for the primary \(writer\) instance, the name is typically created for you by appending *\-instance\-1* to the name that you create for your Aurora PostgreSQL DB cluster\.
  + `session_id` – The `session_id` field indicates whether the instance is a reader or a writer\. For a writer instance, `session_id` is always set to `"MASTER_SESSION_ID"`\. For a reader instance, `session_id` is set to the `UUID` of the specific reader\.
+ **Diagnose common replication issues, such as replica lag** – Replica lag is the time in milliseconds that the page cache of a reader instance is behind that of the writer instance\. This lag occurs because Aurora clusters use asynchronous replication, as described in [Replication with Amazon Aurora](Aurora.Replication.md)\. It's shown in the `replica_lag_in_msec` column in the results returned by this function\. Lag can also occur when a query is cancelled due to conflicts with recovery on a standby server\. You can check `pg_stat_database_conflicts()` to verify that such a conflict is causing the replica lag \(or not\)\. For more information, see [The Statistics Collector](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-CONFLICTS-VIEW) in the *PostgreSQL documentation*\. To learn more about high availability and replication, see [Amazon Aurora FAQs](https://aws.amazon.com/rds/aurora/faqs/#High_Availability_and_Replication)\. 

  Amazon CloudWatch stores `replica_lag_in_msec` results over time, as the `AuroraReplicaLag` metric\. For information about using CloudWatch metrics for Aurora, see [Monitoring Amazon Aurora metrics with Amazon CloudWatch](monitoring-cloudwatch.md) 

To learn more about troubleshooting Aurora read replicas and restarts, see [Why did my Amazon Aurora read replica fall behind and restart?](https://aws.amazon.com/premiumsupport/knowledge-center/aurora-read-replica-restart/) in the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\. 

## Examples<a name="aurora_replica_status-examples"></a>

The following example shows how to get the replication status of all instances in an Aurora PostgreSQL DB cluster:

```
=> SELECT * 
FROM aurora_replica_status();
```

The following example shows the writer instance in the `docs-lab-apg-main` Aurora PostgreSQL DB cluster: 

```
=> SELECT server_id, 
    CASE 
        WHEN 'MASTER_SESSION_ID' = session_id THEN 'writer'
        ELSE 'reader' 
    END AS instance_role
FROM aurora_replica_status() 
WHERE session_id = 'MASTER_SESSION_ID';
        server_id       | instance_role
------------------------+---------------
 db-119-001-instance-01 | writer
```

The following example example lists all reader instances in a cluster:

```
=> SELECT server_id, 
    CASE 
        WHEN 'MASTER_SESSION_ID' = session_id THEN 'writer'
        ELSE 'reader' 
    END AS instance_role
FROM aurora_replica_status() 
WHERE session_id <> 'MASTER_SESSION_ID';
        server_id       | instance_role
------------------------+---------------
db-119-001-instance-02  | reader
db-119-001-instance-03  | reader
db-119-001-instance-04  | reader
db-119-001-instance-05  | reader
(4 rows)
```

The following example lists all instances, how far each instance is lagging behind the writer, and how long since the last update: 

```
=> SELECT server_id, 
    CASE 
        WHEN 'MASTER_SESSION_ID' = session_id THEN 'writer'
        ELSE 'reader' 
    END AS instance_role,
    replica_lag_in_msec AS replica_lag_ms,
    round(extract (epoch FROM (SELECT age(clock_timestamp(), last_update_timestamp))) * 1000) AS last_update_age_ms
FROM aurora_replica_status()
ORDER BY replica_lag_in_msec NULLS FIRST;
       server_id        | instance_role | replica_lag_ms | last_update_age_ms
------------------------+---------------+----------------+--------------------
 db-124-001-instance-03 | writer        |         [NULL] |               1756
 db-124-001-instance-01 | reader        |             13 |               1756
 db-124-001-instance-02 | reader        |             13 |               1756
(3 rows)
```