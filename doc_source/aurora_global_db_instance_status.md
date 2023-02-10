# aurora\_global\_db\_instance\_status<a name="aurora_global_db_instance_status"></a>

Displays the status of all Aurora instances, including replicas in an Aurora global DB cluster\. 

## Syntax<a name="aurora_global_db_instance_status-syntax"></a>

 

```
aurora_global_db_instance_status()
```

## Arguments<a name="aurora_global_db_instance_status-arguments"></a>

None

## Return type<a name="aurora_global_db_instance_status-return-type"></a>

SETOF record with following columns:
+ `server_id` – The identifier of the DB instance\. 
+ `session_id` – A unique identifier for the current session\. A value of `MASTER_SESSION_ID` identifies the Writer \(primary\) DB instance\. 
+ `aws_region` – The AWS Region in which this global DB instance runs\. For a list of Regions, see [Region availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\. 
+ `durable_lsn` – The log sequence number \(LSN\) made durable in storage\. A log sequence number \(LSN\) is a unique sequential number that identifies a record in the database transaction log\. LSNs are ordered such that a larger LSN represents a later transaction\.
+ `highest_lsn_rcvd` – The highest LSN received by the DB instance from the writer DB instance\. 
+ `feedback_epoch` – The epoch that the DB instance uses when it generates hot standby information\. A *hot standby* is a DB instance that supports connections and queries while the primary DB is in recovery or standby mode\. The hot standby information includes the epoch \(point in time\) and other details about the DB instance that's being used as a hot standby\. For more information, see [Hot Standby](https://www.postgresql.org/docs/current/hot-standby.html) in the PostgreSQL documentation\. 
+ `feedback_xmin` – The minimum \(oldest\) active transaction ID used by the DB instance\. 
+ `oldest_read_view_lsn` – The oldest LSN used by the DB instance to read from storage\. 
+ `visibility_lag_in_msec` – How far this DB instance is lagging behind the writer DB instance in milliseconds\.

## Usage notes<a name="aurora_global_db_instance_status-usage-notes"></a>

This function shows replication statistics for an Aurora DB cluster\. For each Aurora PostgreSQL DB instance in the cluster, the function shows a row of data that includes any cross\-Region replicas in a global database configuration\.

You can run this function from any instance in an Aurora PostgreSQL DB cluster or an Aurora PostgreSQL global database\. The function returns details about lag for all replica instances\.

To learn more about monitoring lag using this function \(`aurora_global_db_instance_status`\) or by using using `aurora_global_db_status`, see [Monitoring Aurora PostgreSQL\-based Aurora global databases](aurora-global-database-monitoring.md#aurora-global-database-monitoring.postgres)\.

For more information about Aurora global databases, see [Overview of Amazon Aurora global databases](aurora-global-database.md#aurora-global-database-overview)\. 

To get started with Aurora global databases, see [Getting started with Amazon Aurora global databases](aurora-global-database-getting-started.md) or see [Amazon Aurora FAQs](https://aws.amazon.com/rds/aurora/faqs/)\. 

## Examples<a name="aurora_global_db_instance_status-examples"></a>

This example shows cross\-Region instance stats\.

```
=> SELECT *
  FROM aurora_global_db_instance_status();
                server_id                 |              session_id              |  aws_region  | durable_lsn | highest_lsn_rcvd | feedback_epoch | feedback_xmin | oldest_read_view_lsn | visibility_lag_in_msec
------------------------------------------+--------------------------------------+--------------+-------------+------------------+----------------+---------------+----------------------+------------------------
 db-119-001-instance-01                   | MASTER_SESSION_ID                    | eu-west-1    |  2534560273 |           [NULL] |         [NULL] |        [NULL] |               [NULL] |                 [NULL]
 db-119-001-instance-02                   | 4ecff34d-d57c-409c-ba28-278b31d6fc40 | eu-west-1    |  2534560266 |       2534560273 |              0 |      19669196 |           2534560266 |                      6
 db-119-001-instance-03                   | 3e8a20fc-be86-43d5-95e5-bdf19d27ad6b | eu-west-1    |  2534560266 |       2534560273 |              0 |      19669196 |           2534560266 |                      6
 db-119-001-instance-04                   | fc1b0023-e8b4-4361-bede-2a7e926cead6 | eu-west-1    |  2534560266 |       2534560273 |              0 |      19669196 |           2534560254 |                     23
 db-119-001-instance-05                   | 30319b74-3f08-4e13-9728-e02aa1aa8649 | eu-west-1    |  2534560266 |       2534560273 |              0 |      19669196 |           2534560254 |                     23
 db-119-001-global-instance-1             | a331ffbb-d982-49ba-8973-527c96329c60 | eu-central-1 |  2534560254 |       2534560266 |              0 |      19669196 |           2534560247 |                    996
 db-119-001-global-instance-1             | e0955367-7082-43c4-b4db-70674064a9da | eu-west-2    |  2534560254 |       2534560266 |              0 |      19669196 |           2534560247 |                     14
 db-119-001-global-instance-1-eu-west-2a  | 1248dc12-d3a4-46f5-a9e2-85850491a897 | eu-west-2    |  2534560254 |       2534560266 |              0 |      19669196 |           2534560247 |                      0
```

This example shows how to check global replica lag in milliseconds\. 

```
=> SELECT CASE
          WHEN 'MASTER_SESSION_ID' = session_id THEN 'Primary'
         ELSE 'Secondary'
      END AS global_role,
      aws_region,
      server_id,
      visibility_lag_in_msec
  FROM aurora_global_db_instance_status()
  ORDER BY 1, 2, 3;
   global_role |  aws_region  |                server_id                | visibility_lag_in_msec
-------------+--------------+-----------------------------------------+------------------------
 Primary     | eu-west-1    | db-119-001-instance-01                  |                 [NULL]
 Secondary   | eu-central-1 | db-119-001-global-instance-1            |                     13
 Secondary   | eu-west-1    | db-119-001-instance-02                  |                     10
 Secondary   | eu-west-1    | db-119-001-instance-03                  |                      9
 Secondary   | eu-west-1    | db-119-001-instance-04                  |                      2
 Secondary   | eu-west-1    | db-119-001-instance-05                  |                     18
 Secondary   | eu-west-2    | db-119-001-global-instance-1            |                     14
 Secondary   | eu-west-2    | db-119-001-global-instance-1-eu-west-2a |                     13
```

This example shows how to check min, max and average lag per AWS Region from the global database configuration\.

```
=> SELECT 'Secondary' global_role,
       aws_region,
       min(visibility_lag_in_msec) min_lag_in_msec,
       max(visibility_lag_in_msec) max_lag_in_msec,
       round(avg(visibility_lag_in_msec),0) avg_lag_in_msec
  FROM aurora_global_db_instance_status()
 WHERE aws_region NOT IN (SELECT   aws_region
                              FROM aurora_global_db_instance_status()
                             WHERE session_id='MASTER_SESSION_ID')
                          GROUP BY aws_region
UNION ALL
SELECT  'Primary' global_role,
        aws_region,
        NULL,
        NULL,
        NULL
    FROM aurora_global_db_instance_status()
   WHERE session_id='MASTER_SESSION_ID'
ORDER BY 1, 5;
 global_role |  aws_region  | min_lag_in_msec | max_lag_in_msec | avg_lag_in_msec
------------+--------------+-----------------+-----------------+-----------------
 Primary    | eu-west-1    |          [NULL] |          [NULL] |          [NULL]
 Secondary  | eu-central-1 |             133 |             133 |             133
 Secondary  | eu-west-2    |               0 |             495 |             248
```