# aurora\_stat\_get\_db\_commit\_latency<a name="aurora_stat_get_db_commit_latency"></a>

Gets the cumulative commit latency in microseconds for Aurora PostgreSQL databases\. *Commit latency* is measured as the time between when a client submits a commit request and when it receives the commit acknowledgement\.

## Syntax<a name="aurora_stat_get_db_commit_latency-syntax"></a>

 

```
aurora_stat_get_db_commit_latency(database_oid)
```

## Return type<a name="aurora_stat_get_db_commit_latency-return-type"></a>

SETOF record

## Arguments<a name="aurora_stat_get_db_commit_latency-arguments"></a>

 *database\_oid*   
The object ID \(OID\) of the Aurora PostgreSQL database\.

## Usage notes<a name="aurora_stat_get_db_commit_latency-usage-notes"></a>

Amazon CloudWatch uses this function to calculate the average commit latency\. For more information about Amazon CloudWatch metrics and how to troubleshoot high commit latency, see [Viewing metrics in the Amazon RDS console](USER_Monitoring.md) and [Making better decisions about Amazon RDS with Amazon CloudWatch metrics](https://aws.amazon.com/blogs/database/making-better-decisions-about-amazon-rds-with-amazon-cloudwatch-metrics/)\.

You can reset this statistic by using the PostgreSQL statistics access function `pg_stat_reset`\. You can check the last time this statistic was reset by using the `pg_stat_get_db_stat_reset_time` function\. For more information about PostgreSQL statistics access functions, see [The Statistics Collector](https://www.postgresql.org/docs/9.1/monitoring-stats.html) in the PostgreSQL documentation\.

## Examples<a name="aurora_stat_get_db_commit_latency-examples"></a>

The following example gets the cumulative commit latency for each database in the `pg_database` cluster\.

```
=> SELECT oid, 
          datname, 
          aurora_stat_get_db_commit_latency(oid) 
     FROM pg_database;
     
  oid  |    datname     | aurora_stat_get_db_commit_latency
-------+----------------+-----------------------------------
 14006 | template0      |                                 0
 16384 | rdsadmin       |                         654387789
     1 | template1      |                                 0
 16401 | mydb           |                            229556
 69768 | postgres       |                             22011
```

The following example gets the cumulative commit latency for the currently connected database\. Before calling the `aurora_stat_get_db_commit_latency` function, the example first uses `\gset` to define a variable for the `oid` argument and sets its value from the connected database\.

```
--Get the oid value from the connected database before calling aurora_stat_get_db_commit_latency

=> SELECT oid
     FROM pg_database
    WHERE datname=(SELECT current_database()) \gset

=> SELECT * 
     FROM aurora_stat_get_db_commit_latency(:oid);
     
 aurora_stat_get_db_commit_latency
-----------------------------------
                        1424279160
```

The following example gets the cumulative commit latency for the `mydb` database in the `pg_database` cluster\. Then, it resets this statistic by using the `pg_stat_reset` function and shows the results\. Finally, it uses the `pg_stat_get_db_stat_reset_time` function to check the last time this statistic was reset\.

```
=> SELECT oid,
          datname,
          aurora_stat_get_db_commit_latency(oid)
     FROM pg_database
    WHERE datname = 'mydb';
     
  oid  |  datname  | aurora_stat_get_db_commit_latency
-------+-----------+-----------------------------------
 16427 | mydb      |                           3320370


=> SELECT pg_stat_reset();

 pg_stat_reset
---------------


=> SELECT oid,
          datname,
          aurora_stat_get_db_commit_latency(oid)
     FROM pg_database
    WHERE datname = 'mydb';
     
  oid  |  datname  | aurora_stat_get_db_commit_latency
-------+-----------+-----------------------------------
 16427 | mydb      |                                 6
 
 
=> SELECT * 
     FROM pg_stat_get_db_stat_reset_time(16427);
     
 pg_stat_get_db_stat_reset_time
--------------------------------
 2021-04-29 21:36:15.707399+00
```