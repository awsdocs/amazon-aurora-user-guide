# synch/sxlock/innodb/hash\_table\_locks<a name="ams-waits.sx-lock-hash-table-locks"></a>

The `synch/sxlock/innodb/hash_table_locks` event occurs when pages not found in the buffer pool must be read from a file\.

**Topics**
+ [Supported engine versions](#ams-waits.sx-lock-hash-table-locks.context.supported)
+ [Context](#ams-waits.sx-lock-hash-table-locks.context)
+ [Likely causes of increased waits](#ams-waits.sx-lock-hash-table-locks.causes)
+ [Actions](#ams-waits.sx-lock-hash-table-locks.actions)

## Supported engine versions<a name="ams-waits.sx-lock-hash-table-locks.context.supported"></a>

This wait event information is supported for Aurora MySQL version 2, up to 2\.09\.2\.

## Context<a name="ams-waits.sx-lock-hash-table-locks.context"></a>

The event `synch/sxlock/innodb/hash_table_locks` indicates that a workload is frequently accessing data that isn't stored in the buffer pool\. This wait event is associated with new page additions and old data evictions from the buffer pool\. The data stored in the buffer pool aged and new data must be cached, so the aged pages are evicted to allow caching of the new pages\. MySQL uses a least recently used \(LRU\) algorithm to evict pages from the buffer pool\. The workload is trying to access data that hasn't been loaded into the buffer pool or data that has been evicted from the buffer pool\.

This wait event occurs when the workload must access the data in files on disk or when blocks are freed from or added to the buffer pool's LRU list\. These operations wait to obtain a shared excluded lock \(SX\-lock\)\. This SX\-lock is used for the synchronization over the *hash table*, which is a table in memory designed to improve buffer pool access performance\.

For more information, see [Buffer Pool](https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool.html) in the MySQL documentation\.

## Likely causes of increased waits<a name="ams-waits.sx-lock-hash-table-locks.causes"></a>

When the `synch/sxlock/innodb/hash_table_locks` wait event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**An undersized buffer pool**  
The size of the buffer pool is too small to keep all of the frequently accessed pages in memory\.

**Heavy workload**  
The workload is causing frequent evictions and data pages reloads in the buffer cache\.

**Errors reading the pages**  
There are errors reading pages in the buffer pool, which might indicate data corruption\.

## Actions<a name="ams-waits.sx-lock-hash-table-locks.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Increase the size of the buffer pool](#ams-waits.sx-lock-hash-table-locks.actions.increase-buffer-pool-size)
+ [Improve data access patterns](#ams-waits.sx-lock-hash-table-locks.actions.improve-data-access-patterns)
+ [Reduce or avoid full\-table scans](#ams-waits.sx-lock-hash-table-locks.actions.reduce-full-table-scans)
+ [Check the error logs for page corruption](#ams-waits.sx-lock-hash-table-locks.actions.check-error-logs)

### Increase the size of the buffer pool<a name="ams-waits.sx-lock-hash-table-locks.actions.increase-buffer-pool-size"></a>

Make sure that the buffer pool is appropriately sized for the workload\. To do so, you can check the buffer pool cache hit rate\. Typically, if the value drops below 95 percent, consider increasing the buffer pool size\. A larger buffer pool can keep frequently accessed pages in memory longer\. To increase the size of the buffer pool, modify the value of the `innodb_buffer_pool_size` parameter\. The default value of this parameter is based on the DB instance class size\. For more information, see [ Best practices for Amazon Aurora MySQL database configuration](http://aws.amazon.com/blogs/database/best-practices-for-amazon-aurora-mysql-database-configuration/)\.

### Improve data access patterns<a name="ams-waits.sx-lock-hash-table-locks.actions.improve-data-access-patterns"></a>

Check the queries affected by this wait and their execution plans\. Consider improving data access patterns\. For example, if you are using [mysqli\_result::fetch\_array](https://www.php.net/manual/en/mysqli-result.fetch-array.php), you can try increasing the array fetch size\.

You can use Performance Insights to show queries and sessions that might be causing the `synch/sxlock/innodb/hash_table_locks` wait event\.

**To find SQL queries that are responsible for high load**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the AWS Database Blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](https://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Reduce or avoid full\-table scans<a name="ams-waits.sx-lock-hash-table-locks.actions.reduce-full-table-scans"></a>

Monitor your workload to see if it's running full\-table scans, and, if it is, reduce or avoid them\. For example, you can monitor status variables such as `Handler_read_rnd_next`\. For more information, see [Server Status Variables](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html#statvar_Handler_read_rnd_next) in the MySQL documentation\.

### Check the error logs for page corruption<a name="ams-waits.sx-lock-hash-table-locks.actions.check-error-logs"></a>

You can check the mysql\-error\.log for corruption\-related messages that were detected near the time of the issue\. Messages that you can work with to resolve the issue are in the error log\. You might need to recreate objects that were reported as corrupted\.