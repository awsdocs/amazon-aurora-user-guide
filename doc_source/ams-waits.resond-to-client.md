# io/aurora\_respond\_to\_client<a name="ams-waits.resond-to-client"></a>

The `io/aurora_respond_to_client` event occurs when a thread is waiting to return a result set to a client\.

**Topics**
+ [Supported engine versions](#ams-waits.resond-to-client.context.supported)
+ [Context](#ams-waits.resond-to-client.context)
+ [Likely causes of increased waits](#ams-waits.resond-to-client.causes)
+ [Actions](#ams-waits.resond-to-client.actions)

## Supported engine versions<a name="ams-waits.resond-to-client.context.supported"></a>

This wait event information is supported for the following engine versions:
+ For Aurora MySQL version 2, version 2\.07\.7 and higher 2\.07 versions, 2\.09\.3 and higher 2\.09 versions, and 2\.10\.2 and higher 2\.10 versions
+ For Aurora MySQL version 1, version 1\.22\.6 and higher

In versions before version 1\.22\.6, 2\.07\.7, 2\.09\.3, and 2\.10\.2, this wait event erroneously includes idle time\.

## Context<a name="ams-waits.resond-to-client.context"></a>

The event `io/aurora_respond_to_client` indicates that a thread is waiting to return a result set to a client\.

The query processing is complete, and the results are being returned back to the application client\. However, because there isn't enough network bandwidth on the DB cluster, a thread is waiting to return the result set\.

## Likely causes of increased waits<a name="ams-waits.resond-to-client.causes"></a>

When the `io/aurora_respond_to_client` event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**DB instance class insufficient for the workload**  
The DB instance class used by the DB cluster doesn't have the necessary network bandwidth to process the workload efficiently\.

**Large result sets**  
There was an increase in size of the result set being returned, because the query returns higher numbers of rows\. The larger result set consumes more network bandwidth\.

**Increased load on the client**  
There might be CPU pressure, memory pressure, or network saturation on the client\. An increase in load on the client delays the reception of data from the Aurora MySQL DB cluster\.

**Increased network latency**  
There might be increased network latency between the Aurora MySQL DB cluster and client\. Higher network latency increases the time required for the client to receive the data\.

## Actions<a name="ams-waits.resond-to-client.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Identify the sessions and queries causing the events](#ams-waits.resond-to-client.actions.identify)
+ [Scale the DB instance class](#ams-waits.resond-to-client.actions.scale-db-instance-class)
+ [Check workload for unexpected results](#ams-waits.resond-to-client.actions.workload)
+ [Distribute workload with reader instances](#ams-waits.resond-to-client.actions.balance)
+ [Use the SQL\_BUFFER\_RESULT modifier](#ams-waits.resond-to-client.actions.sql-buffer-result)

### Identify the sessions and queries causing the events<a name="ams-waits.resond-to-client.actions.identify"></a>

You can use Performance Insights to show queries blocked by the `io/aurora_respond_to_client` wait event\. Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance isn't optimal, then examine where the database is spending the most time\. Look at the wait events that contribute to the highest load, and find out whether you can optimize the database and application to reduce those events\. 

**To find SQL queries that are responsible for high load**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the AWS Database Blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](http://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Scale the DB instance class<a name="ams-waits.resond-to-client.actions.scale-db-instance-class"></a>

Check for the increase in the value of the Amazon CloudWatch metrics related to network throughput, such as `NetworkReceiveThroughput` and `NetworkTransmitThroughput`\. If the DB instance class network bandwidth is being reached, you can scale the DB instance class used by the DB cluster by modifying the DB cluster\. A DB instance class with larger network bandwidth returns data to clients more efficiently\.

For information about monitoring Amazon CloudWatch metrics, see [Viewing metrics in the Amazon RDS console](USER_Monitoring.md)\. For information about DB instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

### Check workload for unexpected results<a name="ams-waits.resond-to-client.actions.workload"></a>

Check the workload on the DB cluster and make sure that it isn't producing unexpected results\. For example, there might be queries that are returning a higher number of rows than expected\. In this case, you can use Performance Insights counter metrics such as `Innodb_rows_read`\. For more information, see [Performance Insights counter metrics](USER_PerfInsights_Counters.md)\.

### Distribute workload with reader instances<a name="ams-waits.resond-to-client.actions.balance"></a>

You can distribute read\-only workload with Aurora replicas\. You can scale horizontally by adding more Aurora replicas\. Doing so can result in an increase in the throttling limits for network bandwidth\. For more information, see [Amazon Aurora DB clusters](Aurora.Overview.md)\.

### Use the SQL\_BUFFER\_RESULT modifier<a name="ams-waits.resond-to-client.actions.sql-buffer-result"></a>

You can add the `SQL_BUFFER_RESULT` modifier to `SELECT` statements to force the result into a temporary table before they are returned to the client\. This modifier can help with performance issues when InnoDB locks aren't being freed because queries are in the `io/aurora_respond_to_client` wait state\. For more information, see [SELECT Statement](https://dev.mysql.com/doc/refman/5.7/en/select.html) in the MySQL documentation\.