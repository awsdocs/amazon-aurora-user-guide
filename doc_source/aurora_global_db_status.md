# aurora\_global\_db\_status<a name="aurora_global_db_status"></a>

Displays information about various aspects of Aurora global database lag, specifically, lag of the underlying Aurora storage \(so called durability lag\) and lag between the recovery point objective \(RPO\) \.

This function is available for the following Aurora PostgreSQL versions:
+ PostgreSQL 11\.7 \(Aurora PostgreSQL release 3\.2\) and higher versions 
+ PostgreSQL 10\.11 \(Aurora PostgreSQL release 2\.4\) and higher versions

When this function is used with Aurora PostgreSQL DB versions earlier than PostgreSQL 11\.7 \(Aurora PostgreSQL release 3\.2\), it returns less information than when used with PostgreSQL 11\.7 and later versions\. PostgreSQL 11\.7 \(Aurora PostgreSQL release 3\.2\) and higher versions returned enhanced statistics to this function\.

## Syntax<a name="aurora_global_db_status-syntax"></a>

 

```
aurora_global_db_status()
```

## Arguments<a name="aurora_global_db_status-arguments"></a>

None\.

## Return type<a name="aurora_global_db_status-return-type"></a>

SETOF record with following columns:
+ `aws_region` – The AWS Region that this DB cluster is in\. For a complete listing of AWS Regions by engine, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\. 
+ `highest_lsn_written` – The highest log sequence number \(LSN\) that currently exists on this DB cluster\. A log sequence number \(LSN\) is a unique sequential number that identifies a record in the database transaction log\. LSNs are ordered such that a larger LSN represents a later transaction\. 
+ `durability_lag_in_msec` – The difference in the timestamp values between the highest\_lsn\_written on a secondary DB cluster and the highest\_lsn\_written on the primary DB cluster\. A value of \-1 identifies the primary global database of an Aurora global database\. 
+ `rpo_lag_in_msec` – The recovery point objective \(RPO\) lag\. The RPO lag is the time it takes for the most recent user transaction COMMIT to be stored on a secondary DB cluster after it's been stored on the primary DB cluster of an Aurora global database\. A value of \-1 denotes the primary global database \(and thus, lag isn't relevant\)\. 

  In simple terms, this metric calculates the recovery point objective for each Aurora PostgreSQL DB cluster in an Aurora global database, that is, how much data might be lost if there were an outage\. As with lag, RPO is measured in time\.
+ `last_lag_calculation_time` – The timestamp that specifies when values were last calculated for `replication_lag_in_msec` and `rpo_lag_in_msec`\. A time value such as `1970-01-01 00:00:00+00` means this is the primary global database\. 
+ `feedback_epoch` – The epoch that the secondary DB cluster uses when it generates hot standby information\. A *hot standby* is a DB instance that supports connections and queries while the primary DB is in recovery or standby mode\. The hot standby information includes the epoch \(point in time\) and other details about the DB instance that's being used as a hot standby\. For more information, see [Hot Standby](https://www.postgresql.org/docs/current/hot-standby.html) in the PostgreSQL documentation\.
+ `feedback_xmin` – The minimum \(oldest\) active transaction ID used by the secondary DB cluster\.

## Usage notes<a name="aurora_global_db_status-usage-notes"></a>

This function shows replication statistics for an Aurora global database\. It shows one row for each DB cluster in an Aurora PostgreSQL global database\. You can run this function from any instance in your Aurora PostgreSQL global database\.

To evaluate Aurora global database replication lag, which is the visible data lag, see [aurora\_global\_db\_instance\_status](aurora_global_db_instance_status.md)\.

To learn more about using `aurora_global_db_status` and `aurora_global_db_instance_status` to monitor Aurora global database lag, see [Monitoring Aurora PostgreSQL\-based Aurora global databases](aurora-global-database-monitoring.md#aurora-global-database-monitoring.postgres)\. For more information about Aurora global databases, see [Overview of Amazon Aurora global databases](aurora-global-database.md#aurora-global-database-overview)\. 

## Examples<a name="aurora_global_db_status-examples"></a>

This example shows how to display cross\-region storage statistics\.

```
=> SELECT CASE 
          WHEN '-1' = durability_lag_in_msec THEN 'Primary'
          ELSE 'Secondary'
       END AS global_role,
       *
  FROM aurora_global_db_status();
 global_role | aws_region | highest_lsn_written | durability_lag_in_msec | rpo_lag_in_msec | last_lag_calculation_time  | feedback_epoch | feedback_xmin
-------------+------------+---------------------+------------------------+-----------------+----------------------------+----------------+---------------
 Primary     | eu-west-1  |           131031557 |                     -1 |              -1 | 1970-01-01 00:00:00+00     |              0 |             0
 Secondary   | eu-west-2  |           131031554 |                    410 |               0 | 2021-06-01 18:59:36.124+00 |              0 |         12640
 Secondary   | eu-west-3  |           131031554 |                    410 |               0 | 2021-06-01 18:59:36.124+00 |              0 |         12640
```