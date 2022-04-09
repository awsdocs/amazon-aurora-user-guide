# sending data<a name="ams-states.sending-data"></a>

The `sending data` thread state indicates that a thread is reading and filtering rows for a query to determine the correct result set\. The name is misleading because it implies the state is transferring data, not collecting and preparing data to be sent later\.

**Topics**
+ [Supported engine versions](#ams-states.sending-data.context.supported)
+ [Context](#ams-states.sending-data.context)
+ [Likely causes of increased waits](#ams-states.sending-data.causes)
+ [Actions](#ams-states.sending-data.actions)

## Supported engine versions<a name="ams-states.sending-data.context.supported"></a>

This thread state information is supported for the following versions:
+ Aurora MySQL version 2 up to 2\.09\.2
+ Aurora MySQL version 1 up to 1\.23\.1

## Context<a name="ams-states.sending-data.context"></a>

Many thread states are short\-lasting\. Operations occurring during `sending data` tend to perform large numbers of disk or cache reads\. Therefore, `sending data` is often the longest\-running state over the lifetime of a given query\. This state appears when Aurora MySQL is doing the following:
+ Reading and processing rows for a `SELECT` statement
+ Performing a large number of reads from either disk or memory
+ Completing a full read of all data from a specific query
+ Reading data from a table, an index, or the work of a stored procedure
+ Sorting, grouping, or ordering data

After the `sending data` state finishes preparing the data, the thread state `writing to net` indicates the return of data to the client\. Typically, `writing to net` is captured only when the result set is very large or severe network latency is slowing the transfer\.

## Likely causes of increased waits<a name="ams-states.sending-data.causes"></a>

The appearance of `sending data` doesn't by itself indicate a problem\. If performance is poor, and you see frequent instances of `sending data`, the most likely causes are as follows\.

**Topics**
+ [Inefficient query](#ams-states.sending-data.causes.structure)
+ [Suboptimal server configuration](#ams-states.sending-data.causes.server)

### Inefficient query<a name="ams-states.sending-data.causes.structure"></a>

In most cases, what's responsible for this state is a query that isn't using an appropriate index to find the result set of a specific query\. For example, consider a query reading a 10 million record table for all orders placed in California, where the state column isn't indexed or is poorly indexed\. In the latter case, the index might exist, but the optimizer ignores it because of low cardinality\.

### Suboptimal server configuration<a name="ams-states.sending-data.causes.server"></a>

If several queries appear in the `sending data` state, the database server might be configured poorly\. Specifically, the server might have the following issues:
+ The database server doesn't have enough computing capacity: disk I/O, disk type and speed, CPU, or number of CPUs\.
+ The server is starved for allocated resources, such as the InnoDB buffer pool for InnoDB tables or the key buffer for MyIsam tables\.
+ Per\-thread memory settings such as `sort_buffer`, `read_buffer`, and `join_buffer` consume more RAM than required, starving the physical server for memory resources\.

## Actions<a name="ams-states.sending-data.actions"></a>

The general guideline is to find queries that return large numbers of rows by checking the Performance Schema\. If logging queries that don't use indexes is turned on, you can also examine the results from the slow logs\.

**Topics**
+ [Turn on the Performance Schema if it isn't turned on](#ams-states.sending-data.actions.enable-pfs)
+ [Examine memory settings](#ams-states.sending-data.actions.memory)
+ [Examine the explain plans for index usage](#ams-states.sending-data.actions.plans)
+ [Check the volume of data returned](#ams-states.sending-data.actions.maintenance)
+ [Check for concurrency issues](#ams-states.sending-data.actions.concurrent-queries)
+ [Check the structure of your queries](#ams-states.sending-data.actions.subqueries)

### Turn on the Performance Schema if it isn't turned on<a name="ams-states.sending-data.actions.enable-pfs"></a>

Performance Insights reports thread states only if Performance Schema instruments aren't turned on\. When Performance Schema instruments are turned on, Performance Insights reports wait events instead\. Performance Schema instruments provide additional insights and better tools when you investigate potential performance problems\. Therefore, we recommend that you turn on the Performance Schema\. For more information, see [Turning on the Performance Schema for Performance Insights on Aurora MySQL](USER_PerfInsights.EnableMySQL.md)\.

### Examine memory settings<a name="ams-states.sending-data.actions.memory"></a>

Examine the memory settings for the primary buffer pools\. Make sure that these pools are appropriately sized for the workload\. If your database uses multiple buffer pool instances, make sure that they aren't divided into many small buffer pools\. Threads can only use one buffer pool at a time\.

Make sure that the following memory settings used for each thread are properly sized:
+ read\_buffer
+ read\_rnd\_buffer
+ sort\_buffer
+ join\_buffer
+ binlog\_cache

Unless you have a specific reason to modify the settings, use the default values\.

### Examine the explain plans for index usage<a name="ams-states.sending-data.actions.plans"></a>

For queries in the `sending data` thread state, examine the plan to determine whether appropriate indexes are used\. If a query isn't using a useful index, consider adding hints like `USE INDEX` or `FORCE INDEX`\. Hints can greatly increase or decrease the time it takes to run a query, so use care before adding them\.

### Check the volume of data returned<a name="ams-states.sending-data.actions.maintenance"></a>

Check the tables that are being queried and the amount of data that they contain\. Can any of this data be archived? In many cases, the cause of poor query execution times isn't the result of the query plan, but the volume of data to be processed\. Many developers are very efficient in adding data to a database but seldom consider dataset life cycle in the design and development phases\.

Look for queries that perform well in low\-volume databases but perform poorly in your current system\. Sometimes developers who design specific queries might not realize that these queries are returning 350,000 rows\. The developers might have developed the queries in a lower\-volume environment with smaller datasets than production environments have\.

### Check for concurrency issues<a name="ams-states.sending-data.actions.concurrent-queries"></a>

Check whether multiple queries of the same type are running at the same time\. Some forms of queries run efficiently when they run alone\. However, if similar forms of query run together, or in high volume, they can cause concurrency issues\. Often, these issues are caused when the database uses temp tables to render results\. A restrictive transaction isolation level can also cause concurrency issues\.

If tables are read and written to concurrently, the database might be using locks\. To help identify periods of poor performance, examine the use of databases through large\-scale batch processes\. To see recent locks and rollbacks, examine the output of the `SHOW ENGINE INNODB STATUS` command\.

### Check the structure of your queries<a name="ams-states.sending-data.actions.subqueries"></a>

Check whether captured queries from these states use subqueries\. This type of query often leads to poor performance because the database compiles the results internally and then substitutes them back into the query to render data\. This process is an extra step for the database\. In many cases, this step can cause poor performance in a highly concurrent loading condition\.

Also check whether your queries use large numbers of `ORDER BY` and `GROUP BY` clauses\. In such operations, often the database must first form the entire dataset in memory\. Then it must order or group it in a specific manner before returning it to the client\.