# synch/rwlock/innodb/hash\_table\_locks<a name="ams-waits.rw-lock-hash-table-locks"></a>

The `synch/rwlock/innodb/hash_table_locks` event occurs when there is contention on modifying the hash table that maps the buffer cache\.

**Topics**
+ [Supported engine versions](#ams-waits.rw-lock-hash-table-locks.context.supported)
+ [Context](#ams-waits.rw-lock-hash-table-locks.context)
+ [Likely causes of increased waits](#ams-waits.rw-lock-hash-table-locks.causes)
+ [Actions](#ams-waits.rw-lock-hash-table-locks.actions)

## Supported engine versions<a name="ams-waits.rw-lock-hash-table-locks.context.supported"></a>

This wait event information is supported for Aurora MySQL version 1, up to 1\.23\.1\.

## Context<a name="ams-waits.rw-lock-hash-table-locks.context"></a>

The event `synch/rwlock/innodb/hash_table_locks` indicates that there is contention on modifying the hash table that maps the buffer cache\. A *hash table* is a table in memory designed to improve buffer pool access performance\. This wait event is invoked when the hash table needs to be modified\.

For more information, see [Buffer Pool](https://dev.mysql.com/doc/refman/5.6/en/innodb-buffer-pool.html) in the MySQL documentation\.

## Likely causes of increased waits<a name="ams-waits.rw-lock-hash-table-locks.causes"></a>

When the `synch/rwlock/innodb/hash_table_locks` event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**An undersized buffer pool**  
The size of the buffer pool is too small to keep all of the frequently accessed pages in memory\.

**Heavy workload**  
The workload is causing frequent evictions and data pages reloads in the buffer cache\.

## Actions<a name="ams-waits.rw-lock-hash-table-locks.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Increase the size of the buffer pool](#ams-waits.rw-lock-hash-table-locks.actions.increase-buffer-pool-size)
+ [Improve data access patterns](#ams-waits.rw-lock-hash-table-locks.actions.improve-data-access-patterns)
+ [Find SQL queries responsible for high load](#ams-waits.rw-lock-hash-table-locks.actions.high-load)
+ [Reduce or avoid full\-table scans](#ams-waits.rw-lock-hash-table-locks.actions.reduce-full-table-scans)

### Increase the size of the buffer pool<a name="ams-waits.rw-lock-hash-table-locks.actions.increase-buffer-pool-size"></a>

Make sure that the buffer pool is appropriately sized for the workload\. To do so, you can check the buffer pool cache hit rate\. Typically, if the value drops below 95 percent, consider increasing the buffer pool size\. A larger buffer pool can keep frequently accessed pages in memory longer\. 

To increase the size of the buffer pool, modify the value of the `innodb_buffer_pool_size` parameter\. The default value of this parameter is based on the DB instance class size\. For more information, see the AWS Database Blog post [Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](http://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/)\.

### Improve data access patterns<a name="ams-waits.rw-lock-hash-table-locks.actions.improve-data-access-patterns"></a>

Check the queries affected by this wait and their execution plans\. Consider improving data access patterns\. For example, if you are using [mysqli\_result::fetch\_array](https://www.php.net/manual/en/mysqli-result.fetch-array.php), you can try increasing the array fetch size\.

You can use Performance Insights to show queries and sessions that might be causing the `synch/rwlock/innodb/hash_table_locks` wait event\.

### Find SQL queries responsible for high load<a name="ams-waits.rw-lock-hash-table-locks.actions.high-load"></a>

Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance isn't optimal, then examine where the database is spending the most time\. Look at the wait events that contribute to the highest load, and find out whether you can optimize the database and application to reduce those events\.

**To find SQL queries that are responsible for high load**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the AWS Database Blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](http://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Reduce or avoid full\-table scans<a name="ams-waits.rw-lock-hash-table-locks.actions.reduce-full-table-scans"></a>

Monitor your workload to see if it's running full\-table scans, and, if it is, reduce or avoid them\. For example, you can monitor status variables such as `Handler_read_rnd_next`\. For more information, see [Server Status Variables](https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html#statvar_Handler_read_rnd_next) in the MySQL documentation\.