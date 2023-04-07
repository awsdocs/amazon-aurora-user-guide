# Diagnosing table and index bloat<a name="AuroraPostgreSQL.diag-table-ind-bloat"></a>

You can use PostgreSQL Multiversion Concurrency Control \(MVCC\) to help preserve data integrity\. PostgreSQL MVCC works by saving an internal copy of updated or deleted rows \(also called *tuples*\) until a transaction is either committed or rolled back\. This saved internal copy is invisible to users\. However, table bloat can occur when those invisible copies aren't cleaned up regularly by the VACUUM or AUTOVACUUM utilities\. Unchecked, table bloat can incur increased storage costs and slow your processing speed\. 

In many cases, the default settings for VACUUM or AUTOVACUUM on Aurora are sufficient for handling unwanted table bloat\. However, you may want to check for bloat if your application is experiencing the following conditions:
+ Processes a large number of transactions in a relatively short time between VACUUM processes\.
+ Performs poorly and runs out of storage\.

To get started, gather the most accurate information about how much space is used by dead tuples and how much you can recover by cleaning up the table and index bloat\. To do so, use the `pgstattuple` extension to gather statistics on your Aurora cluster\. For more information, see [pgstattuple](https://www.postgresql.org/docs/current/pgstattuple.html)\. Privileges to use the `pgstattuple` extension are limited to the `pg_stat_scan_tables` role and database superusers\.

To create the `pgstattuple` extension on Aurora, connect a client session to the cluster, for example, psql or pgAdmin, and use the following command:

```
CREATE EXTENSION pgstattuple;
```

Create the extension in each database that you want to profile\. After creating the extension, use the command line interface \(CLI\) to measure how much unusable space you can reclaim\. Before gathering statistics, modify the cluster parameter group by setting AUTOVACUUM to 0\. A setting of 0 prevents Aurora from automatically cleaning up any dead tuples left behind by your application, which can impact the accuracy of the results\. Enter the following command to create a simple table:

```
postgres=> CREATE TABLE lab AS SELECT generate_series (0,100000);
SELECT 100001
```

In the following example, we run the query with AUTOVACUUM turned on for the DB cluster\. The `dead_tuple_count` is 0, which indicates that the AUTOVACUUM has deleted the obsolete data or tuples from the PostgreSQL database\.

To use `pgstattuple` to gather information about the table, specify the name of a table or an object identifier \(OID\) in the query:

```
postgres=> SELECT * FROM pgstattuple('lab');
```

```
    
    
table_len | tuple_count | tuple_len | tuple_percent | dead_tuple_count | dead_tuple_len | dead_tuple_percent | free_space | free_percent
-----------+-------------+-----------+---------------+------------------+----------------+--------------------+------------+--------------
3629056 | 100001 | 2800028 | 77.16 | 0 | 0 | 0 | 16616 | 0.46
(1 row)
```

In the following query, we turn off AUTOVACUUM and enter a command that deletes 25,000 rows from the table\. As a result, the `dead_tuple_count` increases to 25000\.

```
postgres=> DELETE FROM lab WHERE generate_series < 25000;    

DELETE 25000
```

```
SELECT * FROM pgstattuple('lab');
```

```
        
table_len | tuple_count | tuple_len | tuple_percent | dead_tuple_count | dead_tuple_len | dead_tuple_percent | free_space | free_percent
-----------+-------------+-----------+---------------+------------------+----------------+--------------------+------------+--------------
3629056 | 75001 | 2100028 | 57.87 | 25000 | 700000 | 19.29 | 16616 | 0.46
(1 row)
```

To reclaim those dead tuples, start a VACUUM process\.

## Observing bloat without interrupting your application<a name="AuroraPostgreSQL.diag-table-ind-bloat.Observing"></a>

Settings on an Aurora cluster are optimized to provide the best practices for most workloads\. However, you might want to optimize a cluster to better suit your applications and use patterns\. In this case, you can use the `pgstattuple` extension without disrupting a busy application\. To do so, perform the following steps:

1. Clone your Aurora instance\.

1. Modify the parameter file to turn off AUTOVACUUM in the clone\.

1. Perform a `pgstattuple` query while testing the clone with a sample workload or with pgbench, which is a program for running benchmark tests on PostgreSQL\. For more information, see [pgbench](https://www.postgresql.org/docs/current/pgbench.html)\.

After running your applications and viewing the result, use pg\_repack or VACUUM FULL on the restored copy and compare the differences\. If you see a significant drop in the dead\_tuple\_count, dead\_tuple\_len, or dead\_tuple\_percent, then adjust the vacuum schedule on your production cluster to minimize the bloat\.

## Avoiding bloat in temporary tables<a name="AuroraPostgreSQL.diag-table-ind-bloat.AvoidinginTables"></a>

If your application creates temporary tables, make sure that your application removes those temporary tables when they’re no longer needed\. Autovacuum processes don’t locate temporary tables\. Left unchecked, temporary tables can quickly create database bloat\. Moreover, the bloat can extend into the system tables, which are the internal tables that track PostgreSQL objects and attributes, like pg\_attribute and pg\_depend\.

When a temporary table is no longer needed, you can use a TRUNCATE statement to empty the table and free up the space\. Then, manually vacuum the pg\_attribute and pg\_depend tables\. Vacuuming these tables ensures that creating and truncating/deleting temporary tables continually isn't adding tuples and contributing to system bloat\. 

You can avoid this problem while creating a temporary table by including the following syntax that deletes the new rows when content is committed: 

```
CREATE TEMP TABLE IF NOT EXISTS table_name(table_description) ON COMMIT DELETE ROWS;
```

The `ON COMMIT DELETE ROWS` clause truncates the temporary table when the transaction is committed\.

## Avoiding bloat in indexes<a name="AuroraPostgreSQL.diag-table-ind-bloat.AvoidinginIndexes"></a>

When you change an indexed field in a table, the index update results in one or more dead tuples in that index\. By default, the autovacuum process cleans up bloat in indexes, but that cleanup uses a significant amount of time and resources\. To specify index cleanup preferences when you create a table, include the vacuum\_index\_cleanup clause\. By default, at table creation time, the clause is set to AUTO, which means that the server decides if your index requires cleanup when it vacuums the table\. You can set the clause to ON to turn on index cleanup for a specific table, or OFF to turn off index cleanup for that table\. Remember, turning off index cleanup might save time, but can potentially lead to a bloated index\. 

You can manually control index cleanup when you VACUUM a table at the command line\. To vacuum a table and remove dead tuples from the indexes, include the INDEX\_CLEANUP clause with a value of ON and the table name:

```
acctg=> VACUUM (INDEX_CLEANUP ON) receivables;
        
INFO: aggressively vacuuming "public.receivables"
VACUUM
```

To vacuum a table without cleaning the indexes, specify a value of OFF:

```
acctg=> VACUUM (INDEX_CLEANUP OFF) receivables;
        
INFO: aggressively vacuuming "public.receivables"
VACUUM
```