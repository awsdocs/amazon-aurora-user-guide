# aurora\_wait\_report<a name="aurora_wait_report"></a>

This function shows wait event activity over a period of time\. 

## Syntax<a name="aurora_wait_report-syntax"></a>

 

```
aurora_wait_report([time])
```

## Arguments<a name="aurora_wait_report-arguments"></a>

 *time \(optional\) *   
The time in seconds\. Default is 10 seconds\.

## Return type<a name="aurora_wait_report-return-type"></a>

SETOF record with following columns:
+ type\_name – Wait type name
+ event\_name – Wait event name
+ wait – Number of waits
+ wait\_time – Wait time in milliseconds 
+ ms\_per\_wait – Average milliseconds by the number of an wait
+ waits\_per\_xact – Average waits by the number of one transaction
+ ms\_per\_xact – Average milliseconds by the number of transactions

## Usage notes<a name="aurora_wait_report-usage-notes"></a>

This function is available as of Aurora PostgreSQL release 1\.1 compatible with PostgreSQL 9\.6\.6 and higher versions\.

To use this function, you need to first create the Aurora PostgreSQL `aurora_stat_utils` extension, as follows:

```
=> CREATE extension aurora_stat_utils;
CREATE EXTENSION
```

For more information about available Aurora PostgreSQL extension versions, see [Extension versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html) in *Release Notes for Aurora PostgreSQL*\.

This function calculates the instance\-level wait events by comparing two snapshots of statistics data from aurora\_stat\_system\_waits\(\) function and pg\_stat\_database PostgreSQL Statistics Views\. 

For more information about `aurora_stat_system_waits()` and `pg_stat_database`, see [The Statistics Collector](https://www.postgresql.org/docs/current/monitoring-stats.html#PG-STAT-DATABASE-VIEW) in the *PostgreSQL documentation*\.

When run, this function takes an initial snapshot, waits the number of seconds specified, and then takes a second snapshot\. The function compares the two snapshots and returns the difference\. This difference represents the instance's activity for that time interval\. 

On the writer instance, the function also displays the number of committed transactions and TPS \(transactions per second\)\. This function returns information at the instance level and includes all databases on the instance\. 

## Examples<a name="aurora_wait_report-examples"></a>

This example shows how to create aurora\_stat\_utils extension to be able to use aurora\_log\_report function\. 

```
=> CREATE extension aurora_stat_utils;
CREATE EXTENSION
```

This example shows how to check wait report for 10 seconds\. 

```
=> SELECT *
     FROM aurora_wait_report();
NOTICE:  committed 34 transactions in 10 seconds (tps 3)
 type_name |    event_name     | waits | wait_time | ms_per_wait | waits_per_xact | ms_per_xact
-----------+-------------------+-------+-----------+-------------+----------------+-------------
 Client    | ClientRead        |    26 |  30003.00 |    1153.961 |           0.76 |     882.441
 Activity  | WalWriterMain     |    50 |  10051.32 |     201.026 |           1.47 |     295.627
 Timeout   | PgSleep           |     1 |  10049.52 |   10049.516 |           0.03 |     295.574
 Activity  | BgWriterHibernate |     1 |  10048.15 |   10048.153 |           0.03 |     295.534
 Activity  | AutoVacuumMain    |    18 |   9941.66 |     552.314 |           0.53 |     292.402
 Activity  | BgWriterMain      |     1 |    201.09 |     201.085 |           0.03 |       5.914
 IO        | XactSync          |    15 |     25.34 |       1.690 |           0.44 |       0.745
 IO        | RelationMapRead   |    12 |      0.54 |       0.045 |           0.35 |       0.016
 IO        | WALWrite          |    84 |      0.21 |       0.002 |           2.47 |       0.006
 IO        | DataFileExtend    |     1 |      0.02 |       0.018 |           0.03 |       0.001
```

This example shows how to check wait report for 60 seconds\. 

```
=> SELECT *
     FROM aurora_wait_report(60);
NOTICE:  committed 1544 transactions in 60 seconds (tps 25)
 type_name |       event_name       |  waits  | wait_time | ms_per_wait | waits_per_xact | ms_per_xact
-----------+------------------------+---------+-----------+-------------+----------------+-------------
 Lock      | transactionid          |    6422 | 477000.53 |      74.276 |           4.16 |     308.938
 Client    | ClientRead             |    8265 | 270752.99 |      32.759 |           5.35 |     175.358
 Activity  | CheckpointerMain       |       1 |  60100.25 |   60100.246 |           0.00 |      38.925
 Timeout   | PgSleep                |       1 |  60098.49 |   60098.493 |           0.00 |      38.924
 Activity  | WalWriterMain          |     296 |  60010.99 |     202.740 |           0.19 |      38.867
 Activity  | AutoVacuumMain         |     107 |  59827.84 |     559.139 |           0.07 |      38.749
 Activity  | BgWriterMain           |     290 |  58821.83 |     202.834 |           0.19 |      38.097
 IO        | XactSync               |    1295 |  55220.13 |      42.641 |           0.84 |      35.764
 IO        | WALWrite               | 6602259 |  47810.94 |       0.007 |        4276.07 |      30.966
 Lock      | tuple                  |     473 |  29880.67 |      63.173 |           0.31 |      19.353
 LWLock    | buffer_mapping         |     142 |   3540.13 |      24.930 |           0.09 |       2.293
 Activity  | BgWriterHibernate      |     290 |   1124.15 |       3.876 |           0.19 |       0.728
 IO        | BufFileRead            |    7615 |    618.45 |       0.081 |           4.93 |       0.401
 LWLock    | buffer_content         |      73 |    345.93 |       4.739 |           0.05 |       0.224
 LWLock    | lock_manager           |      62 |    191.44 |       3.088 |           0.04 |       0.124
 IO        | RelationMapRead        |      72 |      5.16 |       0.072 |           0.05 |       0.003
 LWLock    | ProcArrayLock          |       1 |      2.01 |       2.008 |           0.00 |       0.001
 IO        | ControlFileWriteUpdate |       2 |      0.03 |       0.013 |           0.00 |       0.000
 IO        | DataFileExtend         |       1 |      0.02 |       0.018 |           0.00 |       0.000
 IO        | ControlFileSyncUpdate  |       1 |      0.00 |       0.000 |           0.00 |       0.000
```