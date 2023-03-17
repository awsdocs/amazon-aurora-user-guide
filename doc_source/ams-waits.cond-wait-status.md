# synch/cond/sql/MDL\_context::COND\_wait\_status<a name="ams-waits.cond-wait-status"></a>

The `synch/cond/sql/MDL_context::COND_wait_status` event occurs when there are threads waiting on a table metadata lock\.

**Topics**
+ [Supported engine versions](#ams-waits.cond-wait-status.context.supported)
+ [Context](#ams-waits.cond-wait-status.context)
+ [Likely causes of increased waits](#ams-waits.cond-wait-status.causes)
+ [Actions](#ams-waits.cond-wait-status.actions)

## Supported engine versions<a name="ams-waits.cond-wait-status.context.supported"></a>

This wait event information is supported for the following engine versions:
+ Aurora MySQL version 2, up to 2\.09\.2

## Context<a name="ams-waits.cond-wait-status.context"></a>

The event `synch/cond/sql/MDL_context::COND_wait_status` indicates that there are threads waiting on a table metadata lock\. In some cases, one session holds a metadata lock on a table and another session tries to get the same lock on the same table\. In such a case, the second session waits on the `synch/cond/sql/MDL_context::COND_wait_status` wait event\.

MySQL uses metadata locking to manage concurrent access to database objects and to ensure data consistency\. Metadata locking applies to tables, schemas, scheduled events, tablespaces, and user locks acquired with the `get_lock` function, and stored programs\. Stored programs include procedures, functions, and triggers\. For more information, see [Metadata locking](https://dev.mysql.com/doc/refman/5.7/en/metadata-locking.html) in the MySQL documentation\.

The MySQL process list shows this session in the state `waiting for metadata lock`\. In Performance Insights, if `Performance_schema` is turned on, the event `synch/cond/sql/MDL_context::COND_wait_status` appears\.

The default timeout for a query waiting on a metadata lock is based on the value of the `lock_wait_timeout` parameter, which defaults to 31,536,000 seconds \(365 days\)\.

For more details on different InnoDB locks and the types of locks that can cause conflicts, see [InnoDB Locking](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html) in the MySQL documentation\.

## Likely causes of increased waits<a name="ams-waits.cond-wait-status.causes"></a>

When the `synch/cond/sql/MDL_context::COND_wait_status` event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**Long\-running transactions**  
One or more transactions are modifying a large amount of data and holding locks on tables for a very long time\.

**Idle transactions**  
One or more transactions remain open for a long time, without being committed or rolled back\.

**DDL statements on large tables**  
One or more data definition statements \(DDL\) statements, such as `ALTER TABLE` commands, were run on very large tables\.

**Explicit table locks**  
There are explicit locks on tables that aren't being released in a timely manner\. For example, an application might run `LOCK TABLE` statements improperly\.

## Actions<a name="ams-waits.cond-wait-status.actions"></a>

We recommend different actions depending on the causes of your wait event and on the version of the Aurora MySQL DB cluster\.

**Topics**
+ [Identify the sessions and queries causing the events](#ams-waits.cond-wait-status.actions.identify)
+ [Check for past events](#ams-waits.cond-wait-status.actions.past-events)
+ [Run queries on Aurora MySQL version 2](#ams-waits.cond-wait-status.actions.run-queries-aurora-mysql-57)
+ [Respond to the blocking session](#ams-waits.cond-wait-status.actions.blocker)

### Identify the sessions and queries causing the events<a name="ams-waits.cond-wait-status.actions.identify"></a>

You can use Performance Insights to show queries blocked by the `synch/cond/sql/MDL_context::COND_wait_status` wait event\. However, to identify the blocking session, query metadata tables from `performance_schema` and `information_schema` on the DB cluster\.

Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance isn't optimal, then examine where the database is spending the most time\. Look at the wait events that contribute to the highest load, and find out whether you can optimize the database and application to reduce those events\.

**To find SQL queries that are responsible for high load**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard for that DB instance appears\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the AWS Database Blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](https://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Check for past events<a name="ams-waits.cond-wait-status.actions.past-events"></a>

You can gain insight into this wait event to check for past occurrences of it\. To do so, complete the following actions:
+ Check the data manipulation language \(DML\) and DDL throughput and latency to see if there were any changes in workload\.

  You can use Performance Insights to find queries waiting on this event at the time of the issue\. Also, you can view the digest of the queries run near the time of issue\.
+ If audit logs or general logs are turned on for the DB cluster, you can check for all queries run on the objects \(schema\.table\) involved in the waiting transaction\. You can also check for the queries that completed running before the transaction\.

The information available to troubleshoot past events is limited\. Performing these checks doesn't show which object is waiting for information\. However, you can identify tables with heavy load at the time of the event and the set of frequently operated rows causing conflict at the time of issue\. You can then use this information to reproduce the issue in a test environment and provide insights about its cause\.

### Run queries on Aurora MySQL version 2<a name="ams-waits.cond-wait-status.actions.run-queries-aurora-mysql-57"></a>

In Aurora MySQL version 2, you can identify the blocked session directly by querying `performance_schema` tables or `sys` schema views\. An example can illustrate how to query tables to identify blocking queries and sessions\.

In the following process list output, the connection ID `89` is waiting on a metadata lock, and it's running a `TRUNCATE TABLE` command\. In a query on the `performance_schema` tables or `sys` schema views, the output shows that the blocking session is `76`\.

```
MySQL [(none)]> select @@version, @@aurora_version;
+-----------+------------------+
| @@version | @@aurora_version |
+-----------+------------------+
| 5.7.12    | 2.09.0           |
+-----------+------------------+
1 row in set (0.01 sec)

MySQL [(none)]> show processlist;
+----+-----------------+--------------------+-----------+---------+------+---------------------------------+-------------------------------+
| Id | User            | Host               | db        | Command | Time | State                           | Info                          |
+----+-----------------+--------------------+-----------+---------+------+---------------------------------+-------------------------------+
|  2 | rdsadmin        | localhost          | NULL      | Sleep   |    0 | NULL                            | NULL                          |
|  4 | rdsadmin        | localhost          | NULL      | Sleep   |    2 | NULL                            | NULL                          |
|  5 | rdsadmin        | localhost          | NULL      | Sleep   |    1 | NULL                            | NULL                          |
| 20 | rdsadmin        | localhost          | NULL      | Sleep   |    0 | NULL                            | NULL                          |
| 21 | rdsadmin        | localhost          | NULL      | Sleep   |  261 | NULL                            | NULL                          |
| 66 | auroramysql5712 | 172.31.21.51:52154 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 67 | auroramysql5712 | 172.31.21.51:52158 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 68 | auroramysql5712 | 172.31.21.51:52150 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 69 | auroramysql5712 | 172.31.21.51:52162 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 70 | auroramysql5712 | 172.31.21.51:52160 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 71 | auroramysql5712 | 172.31.21.51:52152 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 72 | auroramysql5712 | 172.31.21.51:52156 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 73 | auroramysql5712 | 172.31.21.51:52164 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 74 | auroramysql5712 | 172.31.21.51:52166 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 75 | auroramysql5712 | 172.31.21.51:52168 | sbtest123 | Sleep   |    0 | NULL                            | NULL                          |
| 76 | auroramysql5712 | 172.31.21.51:52170 | NULL      | Query   |    0 | starting                        | show processlist              |
| 88 | auroramysql5712 | 172.31.21.51:52194 | NULL      | Query   |   22 | User sleep                      | select sleep(10000)           |
| 89 | auroramysql5712 | 172.31.21.51:52196 | NULL      | Query   |    5 | Waiting for table metadata lock | truncate table sbtest.sbtest1 |
+----+-----------------+--------------------+-----------+---------+------+---------------------------------+-------------------------------+
18 rows in set (0.00 sec)
```

Next, a query on the `performance_schema` tables or `sys` schema views shows that the blocking session is `76`\.

```
MySQL [(none)]> select * from sys.schema_table_lock_waits;                                                                
+---------------+-------------+-------------------+-------------+------------------------------+-------------------+-----------------------+-------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+------------------------------+--------------------+------------------------+-------------------------+------------------------------+
| object_schema | object_name | waiting_thread_id | waiting_pid | waiting_account              | waiting_lock_type | waiting_lock_duration | waiting_query                 | waiting_query_secs | waiting_query_rows_affected | waiting_query_rows_examined | blocking_thread_id | blocking_pid | blocking_account             | blocking_lock_type | blocking_lock_duration | sql_kill_blocking_query | sql_kill_blocking_connection |
+---------------+-------------+-------------------+-------------+------------------------------+-------------------+-----------------------+-------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+------------------------------+--------------------+------------------------+-------------------------+------------------------------+
| sbtest        | sbtest1     |               121 |          89 | auroramysql5712@192.0.2.0    | EXCLUSIVE         | TRANSACTION           | truncate table sbtest.sbtest1 |                 10 |                           0 |                           0 |                108 |           76 | auroramysql5712@192.0.2.0    | SHARED_READ        | TRANSACTION            | KILL QUERY 76           | KILL 76                      |
+---------------+-------------+-------------------+-------------+------------------------------+-------------------+-----------------------+-------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+------------------------------+--------------------+------------------------+-------------------------+------------------------------+
1 row in set (0.00 sec)
```

### Respond to the blocking session<a name="ams-waits.cond-wait-status.actions.blocker"></a>

When you identify the session, your options include the following:
+ Contact the application owner or the user\.
+ If the blocking session is idle, consider ending the blocking session\. This action might trigger a long rollback\. To learn how to end a session, see [Ending a session or query](mysql-stored-proc-ending.md)\.

For more information about identifying blocking transactions, see [Using InnoDB Transaction and Locking Information](https://dev.mysql.com/doc/refman/5.7/en/innodb-information-schema-examples.html) in the MySQL documentation\.