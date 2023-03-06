# io/socket/sql/client\_connection<a name="ams-waits.client-connection"></a>

The `io/socket/sql/client_connection` event occurs when a thread is in the process of handling a new connection\.

**Topics**
+ [Supported engine versions](#ams-waits.client-connection.context.supported)
+ [Context](#ams-waits.client-connection.context)
+ [Likely causes of increased waits](#ams-waits.client-connection.causes)
+ [Actions](#ams-waits.client-connection.actions)

## Supported engine versions<a name="ams-waits.client-connection.context.supported"></a>

This wait event information is supported for the following engine versions:
+ Aurora MySQL version 2, up to 2\.09\.2

## Context<a name="ams-waits.client-connection.context"></a>

The event `io/socket/sql/client_connection` indicates that mysqld is busy creating threads to handle incoming new client connections\. In this scenario, the processing of servicing new client connection requests slows down while connections wait for the thread to be assigned\. For more information, see [MySQL server \(mysqld\)](AuroraMySQL.Managing.Tuning.concepts.md#AuroraMySQL.Managing.Tuning.concepts.processes.mysqld)\.

## Likely causes of increased waits<a name="ams-waits.client-connection.causes"></a>

When this event appears more than normal, possibly indicating a performance problem, typical causes include the following:
+ There is a sudden increase in new user connections from the application to your Amazon RDS instance\.
+ Your DB instance can't process new connections because the network, CPU, or memory is being throttled\.

## Actions<a name="ams-waits.client-connection.actions"></a>

If `io/socket/sql/client_connection` dominates database activity, it doesn't necessarily indicate a performance problem\. In a database that isn't idle, a wait event is always on top\. Act only when performance degrades\. We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Identify the problematic sessions and queries](#ams-waits.client-connection.actions.identify-queries)
+ [Follow best practices for connection management](#ams-waits.client-connection.actions.manage-connections)
+ [Scale up your instance if resources are being throttled](#ams-waits.client-connection.upgrade)
+ [Check the top hosts and top users](#ams-waits.client-connection.top-hosts)
+ [Query the performance\_schema tables](#ams-waits.client-connection.perf-schema)
+ [Check the thread states of your queries](#ams-waits.client-connection.thread-states)
+ [Audit your requests and queries](#ams-waits.client-connection.auditing)
+ [Pool your database connections](#ams-waits.client-connection.pooling)

### Identify the problematic sessions and queries<a name="ams-waits.client-connection.actions.identify-queries"></a>

If your DB instance is experiencing a bottleneck, your first task is to find the sessions and queries that cause it\. For a useful blog post, see [Analyze Amazon Aurora MySQL Workloads with Performance Insights](http://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

**To identify sessions and queries causing a bottleneck**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose your DB instance\.

1. In **Database load**, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The queries at the top of the list are causing the highest load on the database\.

### Follow best practices for connection management<a name="ams-waits.client-connection.actions.manage-connections"></a>

To manage your connections, consider the following strategies:
+ Use connection pooling\.

  You can gradually increase the number of connections as required\. For more information, see the whitepaper [Amazon Aurora MySQL Database Administrator’s Handbook](https://d1.awsstatic.com/whitepapers/RDS/amazon-aurora-mysql-database-administrator-handbook.pdf)\.
+ Use a reader node to redistribute read\-only traffic\.

  For more information, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas) and [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\.

### Scale up your instance if resources are being throttled<a name="ams-waits.client-connection.upgrade"></a>

Look for examples of throttling in the following resources:
+ CPU

  Check your Amazon CloudWatch metrics for high CPU usage\.
+ Network

  Check for an increase in the value of the CloudWatch metrics `network receive throughput` and `network transmit throughput`\. If your instance has reached the network bandwidth limit for your instance class, consider scaling up your RDS instance to a higher instance class type\. For more information, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.
+ Freeable memory 

  Check for a drop in the CloudWatch metric `FreeableMemory`\. Also, consider turning on Enhanced Monitoring\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.

### Check the top hosts and top users<a name="ams-waits.client-connection.top-hosts"></a>

Use Performance Insights to check the top hosts and top users\. For more information, see [Analyzing metrics with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)\.

### Query the performance\_schema tables<a name="ams-waits.client-connection.perf-schema"></a>

To get an accurate count of the current and total connections, query the `performance_schema` tables\. With this technique, you identify the source user or host that is responsible for creating a high number of connections\. For example, query the `performance_schema` tables as follows\.

```
SELECT * FROM performance_schema.accounts;
SELECT * FROM performance_schema.users;
SELECT * FROM performance_schema.hosts;
```

### Check the thread states of your queries<a name="ams-waits.client-connection.thread-states"></a>

If your performance issue is ongoing, check the thread states of your queries\. In the `mysql` client, issue the following command\.

```
show processlist;
```

### Audit your requests and queries<a name="ams-waits.client-connection.auditing"></a>

To check the nature of the requests and queries from user accounts, use AuroraAurora MySQL Advanced Auditing\. To learn how to turn on auditing, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\.

### Pool your database connections<a name="ams-waits.client-connection.pooling"></a>

Consider using Amazon RDS Proxy for connection management\. By using RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. For more information, see [Using Amazon RDS Proxy for Aurora](rds-proxy.md)\.