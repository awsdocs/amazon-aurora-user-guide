# Database load<a name="USER_PerfInsights.Overview.ActiveSessions"></a>

*Database load \(DB load\)* measures the level of activity in your database\. The key metric in Performance Insights is `DBLoad`, which is collected every second\.

**Topics**
+ [Active sessions](#USER_PerfInsights.Overview.ActiveSessions.active-sessions)
+ [Average active sessions](#USER_PerfInsights.Overview.ActiveSessions.AAS)
+ [Average active executions](#USER_PerfInsights.Overview.ActiveSessions.AAE)
+ [Dimensions](#USER_PerfInsights.Overview.ActiveSessions.dimensions)

## Active sessions<a name="USER_PerfInsights.Overview.ActiveSessions.active-sessions"></a>

A *database session* represents an application's dialogue with a relational database\. An active session is a connection that has submitted work to the DB engine and is waiting for a response\. 

A session is active when it's either running on CPU or waiting for a resource to become available so that it can proceed\. For example, an active session might wait for a page to be read into memory, and then consume CPU while it reads data from the page\. 

## Average active sessions<a name="USER_PerfInsights.Overview.ActiveSessions.AAS"></a>

The *average active sessions \(AAS\)* is the unit for the `DBLoad` metric in Performance Insights\. To get the average active sessions, Performance Insights samples the number of sessions concurrently running a query\. The AAS is the total number of sessions divided by the total number of samples for a specific time period\. The following table shows 5 consecutive samples of a running query\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.Overview.ActiveSessions.html)

In the preceding example, the DB load for the time interval was 2 AAS\. This measurement means that, on average, 2 sessions were active at a time during the time period when the 5 samples were taken\.

An analogy for DB load is activity in a warehouse\. Suppose that the warehouse employs 100 workers\. If 1 order comes in, 1 worker fulfills the order while the other workers are idle\. If 100 orders come in, all 100 workers fulfill orders simultaneously\. If you periodically sample how many workers are active over a given time period, you can calculate the average number of active workers\. The calculation shows that, on average, *N* workers are busy fulfilling orders at any given time\. If the average was 50 workers yesterday and 75 workers today, the activity level in the warehouse increased\. In the same way, DB load increases as session activity increases\. 

## Average active executions<a name="USER_PerfInsights.Overview.ActiveSessions.AAE"></a>

The average active executions \(AAE\) per second is related to AAS\. To calculate the AAE, Performance Insights divides the total execution time of a query by the time interval\. The following table shows the AAE calculation for the same query in the preceding table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.Overview.ActiveSessions.html)

In most cases, the AAS and AAE for a query are approximately the same\. However, because the inputs to the calculations are different data sources, the calculations often vary slightly\.

## Dimensions<a name="USER_PerfInsights.Overview.ActiveSessions.dimensions"></a>

The `db.load` metric is different from the other time\-series metrics because you can break it into subcomponents called dimensions\. You can think of dimensions as "slice by" categories for the different characteristics of the `DBLoad` metric\.

When you are diagnosing performance issues, the following dimensions are often the most useful:

**Topics**
+ [Wait events](#USER_PerfInsights.Overview.ActiveSessions.waits)
+ [Top SQL](#USER_PerfInsights.Overview.ActiveSessions.top-sql)

For a complete list of dimensions for the Aurora engines, see [DB load sliced by dimensions](USER_PerfInsights.UsingDashboard.Components.md#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.dims)\.

### Wait events<a name="USER_PerfInsights.Overview.ActiveSessions.waits"></a>

A *wait event* causes a SQL statement to wait for a specific event to happen before it can continue running\. Wait events are an important dimension, or category, for DB load because they indicate where work is impeded\. 

Every active session is either running on the CPU or waiting\. For example, sessions consume CPU when they search memory for a buffer, perform a calculation, or run procedural code\. When sessions aren't consuming CPU, they might be waiting for a memory buffer to become free, a data file to be read, or a log to be written to\. The more time that a session waits for resources, the less time it runs on the CPU\. 

When you tune a database, you often try to find out the resources that sessions are waiting for\. For example, two or three wait events might account for 90 percent of DB load\. This measure means that, on average, active sessions are spending most of their time waiting for a small number of resources\. If you can find out the cause of these waits, you can attempt a solution\. 

Consider the analogy of a warehouse worker\. An order comes in for a book\. The worker might be delayed in fulfilling the order\. For example, a different worker might be currently restocking the shelves, a trolley might not be available\. Or the system used to enter the order status might be slow\. The longer the worker waits, the longer it takes to fulfill the order\. Waiting is a natural part of the warehouse workflow, but if wait time becomes excessive, productivity decreases\. In the same way, repeated or lengthy session waits can degrade database performance\. For more information, see [Tuning with wait events for Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Tuning.html) and [Tuning with wait events for Aurora MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Managing.Tuning.wait-events.html) in the *Amazon Aurora User Guide*\. 

Wait events vary by DB engine: 
+ For a list of the common wait events for Aurora MySQL, see [Aurora MySQL wait events](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Waitevents)\. To learn how to tune using these wait events, see [Tuning Aurora MySQL with wait events and thread states](AuroraMySQL.Managing.Tuning.md)\.
+ For information about all MySQL wait events, see [Wait Event Summary Tables](https://dev.mysql.com/doc/refman/5.7/en/wait-summary-tables.html) in the MySQL documentation\.
+ For a list of common wait events for Aurora PostgreSQL, see [Amazon Aurora PostgreSQL wait events](AuroraPostgreSQL.Reference.Waitevents.md)\. To learn how to tune using these wait events, see [Tuning with wait events for Aurora PostgreSQL](AuroraPostgreSQL.Tuning.md)\.
+ For information about all PostgreSQL wait events, see [The Statistics Collector > Wait Event tables](https://www.postgresql.org/docs/current/monitoring-stats.html#WAIT-EVENT-TABLE) in the PostgreSQL documentation\.

### Top SQL<a name="USER_PerfInsights.Overview.ActiveSessions.top-sql"></a>

Where wait events show bottlenecks, top SQL shows which queries are contributing the most to DB load\. For example, many queries might be currently running on the database, but a single query might consume 99 percent of the DB load\. In this case, the high load might indicate a problem with the query\.

By default, the Performance Insights console displays top SQL queries that are contributing to the database load\. The console also shows relevant statistics for each statement\. To diagnose performance problems for a specific statement, you can examine its execution plan\.