# sort index<a name="ams-states.sort-index"></a>

The `sort index` thread state indicates that a thread is processing a `SELECT` statement that requires the use of an internal temporary table to sort the data\.

**Topics**
+ [Supported engine versions](#ams-states.sort-index.context.supported)
+ [Context](#ams-states.sort-index.context)
+ [Likely causes of increased waits](#ams-states.sort-index.causes)
+ [Actions](#ams-states.sort-index.actions)

## Supported engine versions<a name="ams-states.sort-index.context.supported"></a>

This thread state information is supported for the following versions:
+ Aurora MySQL version 2 up to 2\.09\.2
+ Aurora MySQL version 1 up to 1\.23\.1

## Context<a name="ams-states.sort-index.context"></a>

The `create sort index` state appears when a query with an `ORDER BY` or `GROUP BY` clause can't use an existing index to perform the operation\. In this case, MySQL needs to perform a more expensive `filesort` operation\. This operation is typically performed in memory if the result set isn't too large\. Otherwise, it involves creating a file on disk\.

## Likely causes of increased waits<a name="ams-states.sort-index.causes"></a>

The appearance of `sort index` doesn't by itself indicate a problem\. If performance is poor, and you see frequent instances of `sort index`, the most likely cause is slow queries with `ORDER BY` or `GROUP BY` operators\.

## Actions<a name="ams-states.sort-index.actions"></a>

The general guideline is to find queries with `ORDER BY` or `GROUP BY` clauses that are associated with the increases in the `create sort index` state\. Then see whether adding an index or increasing the sort buffer size solves the problem\.

**Topics**
+ [Turn on the Performance Schema if it isn't turned on](#ams-states.sort-index.actions.enable-pfs)
+ [Identify the problem queries](#ams-states.sort-index.actions.identify)
+ [Examine the explain plans for filesort usage](#ams-states.sort-index.actions.plan)
+ [Increase the sort buffer size](#ams-states.sort-index.actions.increasebuffersize)

### Turn on the Performance Schema if it isn't turned on<a name="ams-states.sort-index.actions.enable-pfs"></a>

Performance Insights reports thread states only if Performance Schema instruments aren't turned on\. When Performance Schema instruments are turned on, Performance Insights reports wait events instead\. Performance Schema instruments provide additional insights and better tools when you investigate potential performance problems\. Therefore, we recommend that you turn on the Performance Schema\. For more information, see [Enabling the Performance Schema for Performance Insights on Aurora MySQL](USER_PerfInsights.EnableMySQL.md)\.

### Identify the problem queries<a name="ams-states.sort-index.actions.identify"></a>

To identify current queries that are causing increases in the `create sort index` state, run `show processlist` and see if any of the queries have `ORDER BY` or `GROUP BY`\. Optionally, run `explain for connection N`, where `N` is the process list ID of the query with `filesort`\.

To identify past queries that are causing these increases, turn on the slow query log and find the queries with `ORDER BY`\. Run `EXPLAIN` on the slow queries and look for "using filesort\." For more information, see [Examine the explain plans for filesort usage](#ams-states.sort-index.actions.plan)\.

### Examine the explain plans for filesort usage<a name="ams-states.sort-index.actions.plan"></a>

Identify the statements with `ORDER BY` or `GROUP BY` clauses that result in the `create sort index` state\. 

The following example shows how to run `explain` on a query\. The `Extra` column shows that this query uses `filesort`\.

```
mysql> explain select * from mytable order by c1 limit 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: mytable
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 2064548
     filtered: 100.00
        Extra: Using filesort
1 row in set, 1 warning (0.01 sec)
```

The following example shows the result of running `explain` on the same query after an index is created on column `c1`\.

```
mysql> alter table mytable add index (c1);
```

```
mysql> explain select * from mytable order by c1 limit 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: mytable
   partitions: NULL
         type: index
possible_keys: NULL
          key: c1
      key_len: 1023
          ref: NULL
         rows: 10
     filtered: 100.00
        Extra: Using index
1 row in set, 1 warning (0.01 sec)
```

For information on using indexes for sort order optimization, see [ORDER BY Optimization](https://dev.mysql.com/doc/refman/5.7/en/order-by-optimization.html) in the MySQL documentation\.

### Increase the sort buffer size<a name="ams-states.sort-index.actions.increasebuffersize"></a>

To see whether a specific query required a `filesort` process that created a file on disk, check the `sort_merge_passes` variable value after running the query\. The following shows an example\.

```
mysql> show session status like 'sort_merge_passes';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Sort_merge_passes | 0     |
+-------------------+-------+
1 row in set (0.01 sec)

--- run query
mysql> select * from mytable order by u limit 10; 
--- run status again:

mysql> show session status like 'sort_merge_passes';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Sort_merge_passes | 0     |
+-------------------+-------+
1 row in set (0.01 sec)
```

If the value of `sort_merge_passes` is high, consider increasing the sort buffer size\. Apply the increase at the session level, because increasing it globally can significantly increase the amount of RAM MySQL uses\. The following example shows how to change the sort buffer size before running a query\. 

```
mysql> set session sort_buffer_size=10*1024*1024;
Query OK, 0 rows affected (0.00 sec)
-- run query
```