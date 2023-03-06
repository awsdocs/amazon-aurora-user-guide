# synch/mutex/innodb/trx\_sys\_mutex<a name="ams-waits.trxsysmutex"></a>

The `synch/mutex/innodb/trx_sys_mutex` event occurs when there is high database activity with a large number of transactions\.

**Topics**
+ [Relevant engine versions](#ams-waits.trxsysmutex.context.supported)
+ [Context](#ams-waits.trxsysmutex.context)
+ [Likely causes of increased waits](#ams-waits.trxsysmutex.causes)
+ [Actions](#ams-waits.trxsysmutex.actions)

## Relevant engine versions<a name="ams-waits.trxsysmutex.context.supported"></a>

This wait event information is supported for the following engine versions:
+ Aurora MySQL version 2, up to 2\.09\.2

## Context<a name="ams-waits.trxsysmutex.context"></a>

Internally, the InnoDB database engine uses the repeatable read isolation level with snapshots to provide read consistency\. This gives you a point\-in\-time view of the database at the time the snapshot was created\.

In InnoDB, all changes are applied to the database as soon as they arrive, regardless of whether they're committed\. This approach means that without multiversion concurrency control \(MVCC\), all users connected to the database see all of the changes and the latest rows\. Therefore, InnoDB requires a way to track the changes to understand what to roll back when necessary\.

To do this, InnoDB uses a transaction system \(`trx_sys`\) to track snapshots\. The transaction system does the following:
+ Tracks the transaction ID for each row in the undo logs\.
+ Uses an internal InnoDB structure called `ReadView` that helps to identify which transaction IDs are visible for a snapshot\.

## Likely causes of increased waits<a name="ams-waits.trxsysmutex.causes"></a>

Any database operation that requires the consistent and controlled handling \(creating, reading, updating, and deleting\) of transactions IDs generates a call from `trx_sys` to the mutex\.

These calls happen inside three functions: 
+ `trx_sys_mutex_enter` – Creates the mutex\.
+ `trx_sys_mutex_exit` – Releases the mutex\.
+ `trx_sys_mutex_own` – Tests whether the mutex is owned\.

The InnoDB Performance Schema instrumentation tracks all `trx_sys` mutex calls\. Tracking includes, but isn't limited to, management of `trx_sys` on database startup or shutdown, rollback operations, undo cleanups, row read access, and buffer pool loads\. High database activity with a large number of transactions results in `synch/mutex/innodb/trx_sys_mutex` appearing among the top wait events\.

For more information, see [Monitoring InnoDB Mutex Waits Using Performance Schema](https://dev.mysql.com/doc/refman/5.7/en/monitor-innodb-mutex-waits-performance-schema.html) in the MySQL documentation\.

## Actions<a name="ams-waits.trxsysmutex.actions"></a>

We recommend different actions depending on the causes of your wait event\.

### Identify the sessions and queries causing the events<a name="ams-waits.trxsysmutex.actions.identify"></a>

Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance isn't optimal, then examine where the database is spending the most time\. Look at the wait events that contribute to the highest load\. Find out whether you can optimize the database and application to reduce those events\.

**To view the Top SQL chart in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. Under the **Database load** chart, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](https://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Examine other wait events<a name="ams-waits.trxsysmutex.actions.action1"></a>

Examine the other wait events associated with the `synch/mutex/innodb/trx_sys_mutex` wait event\. Doing this can provide more information about the nature of the workload\. A large number of transactions might reduce throughput, but the workload might also make this necessary\.

For more information on how to optimize transactions, see [Optimizing InnoDB Transaction Management](https://dev.mysql.com/doc/refman/5.7/en/optimizing-innodb-transaction-management.html) in the MySQL documentation\.