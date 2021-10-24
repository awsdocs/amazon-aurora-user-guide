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
+ Aurora MySQL version 1, up to 1\.23\.1

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

  Check for a drop in the CloudWatch metric `FreeableMemory`\. Also, consider turning on Enhanced Monitoring\. For more information, see [Monitoring the OS by using Enhanced Monitoring](USER_Monitoring.OS.md)\.