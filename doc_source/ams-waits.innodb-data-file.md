# io/file/innodb/innodb\_data\_file<a name="ams-waits.innodb-data-file"></a>

The `io/file/innodb/innodb_data_file` event occurs when there are threads waiting on I/O operations from storage\. 

**Topics**
+ [Supported engine versions](#ams-waits.innodb-data-file.context.supported)
+ [Context](#ams-waits.innodb-data-file.context)
+ [Likely causes of increased waits](#ams-waits.innodb-data-file.causes)
+ [Actions](#ams-waits.innodb-data-file.actions)

## Supported engine versions<a name="ams-waits.innodb-data-file.context.supported"></a>

This wait event information is supported for the following engine versions:

Aurora MySQL version 1, up to 1\.23\.1

## Context<a name="ams-waits.innodb-data-file.context"></a>

The *InnoDB buffer pool* is the shared memory area where Aurora MySQL caches table and index data\. Queries can access frequently used data directly from memory without reading from disk\. The event `io/file/innodb/innodb_data_file` indicates that processing the query requires a storage I/O operation because the data isn't available in the buffer pool\.

RDS typically generates this event when it performs I/O operations such as reads, writes, or flushes\. RDS also generates this event when it runs data definition language \(DDL\) statements\. This happens because these statements involve creating, deleting, opening, closing, or renaming InnoDB data files\.

## Likely causes of increased waits<a name="ams-waits.innodb-data-file.causes"></a>

When this event appears more than normal, possibly indicating a performance problem, typical causes include the following:
+ A spike in an application workload that's I/O intensive can increase the occurrence of this wait event because more queries need to read from storage\.

  A significant increase in the number of pages being scanned causes least recently used \(LRU\) pages to be evicted from the buffer pool at a faster rate\. Inefficient query plans can contribute to the problem\. Query plans can be inefficient because of outdated states, missing indexes, or inefficiently written queries\.
+ Storage capacity is sufficient but network throughput exceeds the maximum bandwidth for the instance class, causing I/O throttling\. For information about network throughput capacity for different instance classes, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.
+ Operations involving DDL statements or transactions that read, insert, or modify a large number of rows\. For example, bulk inserts or update or delete statements can specify a wide range of values in the `WHERE` clause\.
+ `SELECT` queries that scan a large number of rows\. For example, queries that use `BETWEEN` or `IN` clauses can specify wide ranges of data\.
+ A low buffer pool hit ratio because the buffer pool is too small\. The smaller the buffer pool, the more frequently LRU pages are flushed out\. This increases the likelihood that the requested data is read from disk\.

## Actions<a name="ams-waits.innodb-data-file.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Identify and optimize problem queries](#ams-waits.innodb-data-file.actions.optimize)
+ [Scale up your instance](#ams-waits.innodb-data-file.actions.upgrade)
+ [Make your buffer scan resistant](#ams-waits.innodb-data-file.actions.scanresistant)

### Identify and optimize problem queries<a name="ams-waits.innodb-data-file.actions.optimize"></a>

Find the query digest responsible for this wait from Performance Insights\. Check the query's statement execution plan to see if the query can be optimized to read fewer pages into the InnoDB buffer pool\. Doing so reduces the number of least recently used pages that are evicted from the buffer pool\. This increases the cache hit efficiency of the buffer pool, which lessens the load on the I/O subsystem\.

To check a query's statement execution plan, run the `EXPLAIN` statement\. This command shows the individual steps involved in query execution\. For more information, see [Optimizing Queries with EXPLAIN](https://dev.mysql.com/doc/refman/5.7/en/using-explain.html) in the MySQL documentation\.

### Scale up your instance<a name="ams-waits.innodb-data-file.actions.upgrade"></a>

If your `io/file/innodb/innodb_data_file` wait events are caused by insufficient network or buffer pool capacity, consider scaling up your RDS instance to a higher instance class type\.
+ Network throughput – Check for an increase in the value of the Amazon CloudWatch metrics `network receive throughput` and `network transmit throughput`\. If your instance has reached the network bandwidth limit for your instance class, consider scaling up your RDS instance to a higher instance class type\. For more information, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.
+ Buffer pool size – Check for a low buffer pool hit ratio\. To monitor this value in Performance Insights, check the `db.Cache.innoDB_buffer_pool_hit_rate.avg` metric\. To add this metric, choose **Manage metrics**, and choose `innoDB_buffer_pool_hit_rate` under **Cache** on the **Database metrics** tab\.

  If the hit ratio is low, consider scaling up your RDS instance to a higher instance class type\.
**Note**  
The DB instance parameter that controls the buffer pool size is `innodb_buffer_pool_size`\. You can modify this parameter value, but we recommend that you scale up your instance class instead because the default value is optimized for each instance class\.

### Make your buffer scan resistant<a name="ams-waits.innodb-data-file.actions.scanresistant"></a>

If you have a mix of reporting and online transaction processing \(OLTP\) queries, consider making your buffer pool scan resistant\. To do this, tune the parameters `innodb_old_blocks_pct` and `innodb_old_blocks_time`\. The effects of these parameters can vary based on your instance class hardware, data, and workload type\. We highly recommend that you benchmark your system before you set these parameters in your production environment\. For more information, see [Making the Buffer Pool Scan Resistant](https://dev.mysql.com/doc/refman/5.7/en/innodb-performance-midpoint_insertion.html) in the MySQL documentation\.