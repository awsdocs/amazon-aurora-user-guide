# aurora\_stat\_dml\_activity<a name="aurora_stat_dml_activity"></a>

Reports cumulative activity for each type of data manipulation language \(DML\) operation on a database in an Aurora PostgreSQL cluster\.

## Syntax<a name="aurora_stat_dml_activity-syntax"></a>

 

```
aurora_stat_dml_activity(database_oid)
```

## Return type<a name="aurora_stat_dml_activity-return-type"></a>

SETOF record

## Arguments<a name="aurora_stat_dml_activity-arguments"></a>

 *database\_oid*   
The object ID \(OID\) of the database in the Aurora PostgreSQL cluster\.

## Usage notes<a name="aurora_stat_dml_activity-usage-notes"></a>

The `aurora_stat_dml_activity` function is only available with Aurora PostgreSQL release 3\.1 compatible with PostgreSQL engine 11\.6 and later\.

Use this function on Aurora PostgreSQL clusters with a large number of databases to identify which databases have more or slower DML activity, or both\.

The `aurora_stat_dml_activity` function returns the number of times the operations ran and the cumulative latency in microseconds for SELECT, INSERT, UPDATE, and DELETE operations\. The report includes only successful DML operations\.

You can reset this statistic by using the PostgreSQL statistics access function `pg_stat_reset`\. You can check the last time this statistic was reset by using the `pg_stat_get_db_stat_reset_time` function\. For more information about PostgreSQL statistics access functions, see [The Statistics Collector](https://www.postgresql.org/docs/9.1/monitoring-stats.html) in the PostgreSQL documentation\.

## Examples<a name="aurora_stat_dml_activity-examples"></a>

The following example shows how to report DML activity statistics for the connected database\.

```
-- Define the oid variable from connected database by using \gset
=> SELECT oid, 
          datname 
     FROM pg_database 
    WHERE datname=(select current_database()) \gset

=> SELECT * 
     FROM aurora_stat_dml_activity(:oid);
     
 select_count | select_latency_microsecs | insert_count | insert_latency_microsecs | update_count | update_latency_microsecs | delete_count | delete_latency_microsecs
--------------+--------------------------+--------------+--------------------------+--------------+--------------------------+--------------+--------------------------
       178957 |                 66684115 |       171065 |                 28876649 |       519538 |            1454579206167 |            1 |                    53027


-- Showing the same results with expanded display on
=> SELECT * 
     FROM aurora_stat_dml_activity(:oid);
-[ RECORD 1 ]------------+--------------
select_count             | 178957
select_latency_microsecs | 66684115
insert_count             | 171065
insert_latency_microsecs | 28876649
update_count             | 519538
update_latency_microsecs | 1454579206167
delete_count             | 1
delete_latency_microsecs | 53027
```

The following example shows DML activity statistics for all databases in the Aurora PostgreSQL cluster\. This cluster has two databases, `postgres` and `mydb`\. The comma\-separated list corresponds with the `select_count`, `select_latency_microsecs`, `insert_count`, `insert_latency_microsecs`, `update_count`, `update_latency_microsecs`, `delete_count`, and `delete_latency_microsecs` fields\.

Aurora PostgreSQL creates and uses a system database named `rdsadmin` to support administrative operations such as backups, restores, health checks, replication, and so on\. These DML operations have no impact on your Aurora PostgreSQL cluster\.

```
=> SELECT oid, 
          datname, 
          aurora_stat_dml_activity(oid) 
     FROM pg_database;
  oid  |    datname     |                    aurora_stat_dml_activity
-------+----------------+-----------------------------------------------------------------
 14006 | template0      | (,,,,,,,)
 16384 | rdsadmin       | (2346623,1211703821,4297518,817184554,0,0,0,0)
     1 | template1      | (,,,,,,,)
 14007 | postgres       | (178961,66716329,171065,28876649,519538,1454579206167,1,53027)
 16401 | mydb           | (200246,64302436,200036,107101855,600000,83659417514,0,0)
```

The following example shows DML activity statistics for all databases, organized in columns for better readability\.

```
SELECT db.datname,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 1), '()') AS select_count,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 2), '()') AS select_latency_microsecs,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 3), '()') AS insert_count,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 4), '()') AS insert_latency_microsecs,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 5), '()') AS update_count,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 6), '()') AS update_latency_microsecs,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 7), '()') AS delete_count,
       BTRIM(SPLIT_PART(db.asdmla::TEXT, ',', 8), '()') AS delete_latency_microsecs 
FROM  (SELECT datname,
              aurora_stat_dml_activity(oid) AS asdmla 
         FROM pg_database
      ) AS db;
      
    datname     | select_count | select_latency_microsecs | insert_count | insert_latency_microsecs | update_count | update_latency_microsecs | delete_count | delete_latency_microsecs
----------------+--------------+--------------------------+--------------+--------------------------+--------------+--------------------------+--------------+--------------------------
 template0      |              |                          |              |                          |              |                          |              |
 rdsadmin       | 4206523      | 2478812333               | 7009414      | 1338482258               | 0            | 0                        | 0            | 0
 template1      |              |                          |              |                          |              |                          |              |
 fault_test     | 66           | 452099                   | 0            | 0                        | 0            | 0                        | 0            | 0
 db_access_test | 1            | 5982                     | 0            | 0                        | 0            | 0                        | 0            | 0
 postgres       | 42035        | 95179203                 | 5752         | 2678832898               | 21157        | 441883182488             | 2            | 1520
 mydb           | 71           | 453514                   | 0            | 0                        | 1            | 190                      | 1            | 152
```

The following example shows the average cumulative latency \(cumulative latency divided by count\) for each DML operation for the database with the OID `16401`\.

```
=> SELECT select_count, 
          select_latency_microsecs, 
          select_latency_microsecs/NULLIF(select_count,0) select_latency_per_exec,
          insert_count, 
          insert_latency_microsecs, 
          insert_latency_microsecs/NULLIF(insert_count,0) insert_latency_per_exec,
          update_count, 
          update_latency_microsecs, 
          update_latency_microsecs/NULLIF(update_count,0) update_latency_per_exec,
          delete_count, 
          delete_latency_microsecs, 
          delete_latency_microsecs/NULLIF(delete_count,0) delete_latency_per_exec
     FROM aurora_stat_dml_activity(16401);

-[ RECORD 1 ]------------+-------------
select_count             | 451312
select_latency_microsecs | 80205857
select_latency_per_exec  | 177
insert_count             | 451001
insert_latency_microsecs | 123667646
insert_latency_per_exec  | 274
update_count             | 1353067
update_latency_microsecs | 200900695615
update_latency_per_exec  | 148478
delete_count             | 12
delete_latency_microsecs | 448
delete_latency_per_exec  | 37
```