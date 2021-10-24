# io/table/sql/handler<a name="ams-waits.waitio"></a>

The `io/table/sql/handler` event occurs when work has been delegated to a storage engine\. 

**Topics**
+ [Supported engine versions](#ams-waits.waitio.context.supported)
+ [Context](#ams-waits.waitio.context)
+ [Likely causes of increased waits](#ams-waits.waitio.causes)
+ [Actions](#ams-waits.waitio.actions)

## Supported engine versions<a name="ams-waits.waitio.context.supported"></a>

This wait event information is supported for the following versions:
+ Aurora MySQL version 2, up to 2\.09\.2
+ Aurora MySQL version 1, up to 1\.23\.1

## Context<a name="ams-waits.waitio.context"></a>

The event `io/table` indicates a wait for access to a table\. This event occurs regardless of whether the data is cached in the buffer pool or accessed on disk\. The `io/table/sql/handler` event indicates an increase in workload activity\. 

A *handler* is a routine specialized in a certain type of data or focused on certain special tasks\. For example, an event handler receives and digests events and signals from the operating system or from a user interface\. A memory handler performs tasks related to memory\. A file input handler is a function that receives file input and performs special tasks on the data, according to context\.

Views such as `performance_schema.events_waits_current` often show `io/table/sql/handler` when the actual wait is a nested wait event such as a lock\. When the actual wait isn't `io/table/sql/handler`, Performance Insights reports the nested wait event\. When Performance Insights reports `io/table/sql/handler`, it represents the actual I/O wait and not a hidden nested wait event\. For more information, see [Performance Schema Atom and Molecule Events](https://dev.mysql.com/doc/refman/5.7/en/performance-schema-atom-molecule-events.html) in the *MySQL Reference Manual*\.

The `io/table/sql/handler` event often appears in top wait events with I/O waits such as `io/aurora_redo_log_flush` and `io/file/innodb/innodb_data_file`\.

## Likely causes of increased waits<a name="ams-waits.waitio.causes"></a>

In Performance Insights, sudden spikes in the `io/table/sql/handler` event indicate an increase in workload activity\. Increased activity means increased I/O\. 

Performance Insights filters the nesting event IDs and doesn't report a `io/table/sql/handler` wait when the underlying nested event is a lock wait\. For example, if the root cause event is `synch/mutex/innodb/aurora_lock_thread_slot_futex`, Performance Insights displays this wait in top wait events and not `io/table/sql/handler`\.

In views such as `performance_schema.events_waits_current`, waits for `io/table/sql/handler` often appear when the actual wait is a nested wait event such as a lock\. When the actual wait differs from `io/table/sql/handler`, Performance Insights looks up the nested wait and reports the actual wait instead of `io/table/sql/handler`\. When Performance Insights reports `io/table/sql/handler`, the real wait is `io/table/sql/handler` and not a hidden nested wait event\. For more information, see [Performance Schema Atom and Molecule Events](https://dev.mysql.com/doc/refman/5.7/en/performance-schema-atom-molecule-events.html) in the *MySQL 5\.7 Reference Manual*\.

## Actions<a name="ams-waits.waitio.actions"></a>

If this wait event dominates database activity, it doesn't necessarily indicate a performance problem\. A wait event is always on top when the database is active\. You need to act only when performance degrades\.

We recommend different actions depending on the other wait events that you see\.

**Topics**
+ [Identify the sessions and queries causing the events](#ams-waits.waitio.actions.identify)
+ [Check for a correlation with Performance Insights counter metrics](#ams-waits.waitio.actions.filters)
+ [Check for other correlated wait events](#ams-waits.waitio.actions.maintenance)
+ [Check the top hosts and top users](#ams-waits.waitio.actions.pi)
+ [Query the performance\_schema tables](#ams-waits.waitio.actions.performance-schema)
+ [Check the thread states of your queries](#ams-waits.waitio.actions.thread-states)
+ [Audit your requests and queries](#ams-waits.waitio.actions.auditing)
+ [Pool your database connections](#ams-waits.waitio.actions.pooling)

### Identify the sessions and queries causing the events<a name="ams-waits.waitio.actions.identify"></a>

Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance is isn't optimal, then examine where the database is spending the most time\. Look at the wait events that contribute to the highest load, and find out whether you can optimize the database and application to reduce those events\.

**To find SQL queries that are responsible for high load**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](https://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Check for a correlation with Performance Insights counter metrics<a name="ams-waits.waitio.actions.filters"></a>

Check for Performance Insights counter metrics such as `Innodb_rows_changed`\. If counter metrics are correlated with `io/table/sql/handler`, follow these steps:

1. In Performance Insights, look for the SQL statements accounting for the `io/table/sql/handler` top wait event\. If possible, optimize this statement so that it returns fewer rows\.

1. Retrieve the top tables from the `schema_table_statistics` and `x$schema_table_statistics` views\. These views show the amount of time spent per table\. For more information, see [The schema\_table\_statistics and x$schema\_table\_statistics Views](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-table-statistics.html) in the *MySQL Reference Manual*\.

   By default, rows are sorted by descending total wait time\. Tables with the most contention appear first\. The output indicates whether time is spent on reads, writes, fetches, inserts, updates, or deletes\. The following example was run on an Aurora MySQL 2\.09\.1 instance\.

   ```
   mysql> select * from sys.schema_table_statistics limit 1\G
   *************************** 1. row ***************************
        table_schema: read_only_db
          table_name: sbtest41
       total_latency: 54.11 m
        rows_fetched: 6001557
       fetch_latency: 39.14 m
       rows_inserted: 14833
      insert_latency: 5.78 m
        rows_updated: 30470
      update_latency: 5.39 m
        rows_deleted: 14833
      delete_latency: 3.81 m
    io_read_requests: NULL
             io_read: NULL
     io_read_latency: NULL
   io_write_requests: NULL
            io_write: NULL
    io_write_latency: NULL
    io_misc_requests: NULL
     io_misc_latency: NULL
   1 row in set (0.11 sec)
   ```

### Check for other correlated wait events<a name="ams-waits.waitio.actions.maintenance"></a>

If `synch/sxlock/innodb/btr_search_latch` and `io/table/sql/handler` contribute most to the DB load anomaly together, check whether the `innodb_adaptive_hash_index` variable is turned on\. If it is, consider increasing the `innodb_adaptive_hash_index_parts` parameter value\. 

If the Adaptive Hash Index is turned off, and the situation warrants it, consider turning it on\. To learn more about the MySQL Adaptive Hash Index, see the following resources:
+ The article [Is Adaptive Hash Index in InnoDB right for my workload?](https://www.percona.com/blog/2016/04/12/is-adaptive-hash-index-in-innodb-right-for-my-workload) on the Percona website
+ [Adaptive Hash Index](https://dev.mysql.com/doc/refman/5.7/en/innodb-adaptive-hash.html) in the *MySQL Reference Manual*
+ The article [Contention in MySQL InnoDB: Useful Info From the Semaphores Section](https://www.percona.com/blog/2019/12/20/contention-in-mysql-innodb-useful-info-from-the-semaphores-section/) on the Percona website

The Adaptive Hash Index isn't a viable option for Aurora reader nodes\. In some cases, performance might be poor on a reader node when `synch/sxlock/innodb/btr_search_latch` and `io/table/sql/handler` are dominant\. If so, consider redirecting the workload temporarily to the writer note and turning on the Adaptive Hash Index\.

### Check the top hosts and top users<a name="ams-waits.waitio.actions.pi"></a>

Use Performance Insights to check the top hosts and top users\. For more information, see [Analyzing metrics with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)\.

### Query the performance\_schema tables<a name="ams-waits.waitio.actions.performance-schema"></a>

To get an accurate count of the current and total connections, query the `performance_schema` tables\. With this technique, you identify the source user or host that is responsible for creating a high number of connections\. For example, query the `performance_schema` tables as follows\.

```
SELECT * FROM performance_schema.accounts;
SELECT * FROM performance_schema.users;
SELECT * FROM performance_schema.hosts;
```

### Check the thread states of your queries<a name="ams-waits.waitio.actions.thread-states"></a>

If your performance issue is ongoing, check the thread states of your queries\. In the `mysql` client, issue the following command\.

```
show processlist;
```

### Audit your requests and queries<a name="ams-waits.waitio.actions.auditing"></a>

To check the nature of the requests and queries from user accounts, use AuroraAurora MySQL Advanced Auditing\. To learn how to turn on auditing, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\.

### Pool your database connections<a name="ams-waits.waitio.actions.pooling"></a>

Consider using Amazon RDS Proxy for connection management\. By using RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. For more information, see [Using Amazon RDS Proxy](rds-proxy.md)\.