# synch/mutex/innodb/buf\_pool\_mutex<a name="ams-waits.bufpoolmutex"></a>

The `synch/mutex/innodb/buf_pool_mutex` event occurs when a thread has acquired a lock on the InnoDB buffer pool to access a page in memory\.

**Topics**
+ [Relevant engine versions](#ams-waits.bufpoolmutex.context.supported)
+ [Context](#ams-waits.bufpoolmutex.context)
+ [Likely causes of increased waits](#ams-waits.bufpoolmutex.causes)
+ [Actions](#ams-waits.bufpoolmutex.actions)

## Relevant engine versions<a name="ams-waits.bufpoolmutex.context.supported"></a>

This wait event information is supported for the following engine versions:
+ Aurora MySQL version 2, up to 2\.09\.2

## Context<a name="ams-waits.bufpoolmutex.context"></a>

The `buf_pool` mutex is a single mutex that protects the control data structures of the buffer pool\.

For more information, see [Monitoring InnoDB Mutex Waits Using Performance Schema](https://dev.mysql.com/doc/refman/5.7/en/monitor-innodb-mutex-waits-performance-schema.html) in the MySQL documentation\.

## Likely causes of increased waits<a name="ams-waits.bufpoolmutex.causes"></a>

This is a workload\-specific wait event\. Common causes for `synch/mutex/innodb/buf_pool_mutex` to appear among the top wait events include the following:
+ The buffer pool size isn't large enough to hold the working set of data\.
+ The workload is more specific to certain pages from a specific table in the database, leading to contention in the buffer pool\.

## Actions<a name="ams-waits.bufpoolmutex.actions"></a>

We recommend different actions depending on the causes of your wait event\.

### Identify the sessions and queries causing the events<a name="ams-waits.bufpoolmutex.actions.identify"></a>

Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance isn't optimal, then examine where the database is spending the most time\. Look at the wait events that contribute to the highest load, and find out whether you can optimize the database and application to reduce those events\.

**To view the Top SQL chart in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. Underneath the **Database load** chart, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](https://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

### Use Performance Insights<a name="ams-waits.bufpoolmutex.actions.action1"></a>

This event is related to workload\. You can use Performance Insights to do the following:
+ Identify when wait events start, and whether there's any change in the workload around that time from the application logs or related sources\.
+ Identify the SQL statements responsible for this wait event\. Examine the execution plan of the queries to make sure that these queries are optimized and using appropriate indexes\.

  If the top queries responsible for the wait event are related to the same database object or table, then consider partitioning that object or table\.

### Create Aurora Replicas<a name="ams-waits.bufpoolmutex.actions.action2"></a>

You can create Aurora Replicas to serve read\-only traffic\. You can also use Aurora Auto Scaling to handle surges in read traffic\. Make sure to run scheduled read\-only tasks and logical backups on Aurora Replicas\.

For more information, see [Using Amazon Aurora Auto Scaling with Aurora Replicas](Aurora.Integrating.AutoScaling.md)\.

### Examine the buffer pool size<a name="ams-waits.bufpoolmutex.actions.action3"></a>

Check whether the buffer pool size is sufficient for the workload by looking at the metric `innodb_buffer_pool_wait_free`\. If the value of this metric is high and increasing continuously, that indicates that the size of the buffer pool isn't sufficient to handle the workload\. If `innodb_buffer_pool_size` has been set properly, the value of `innodb_buffer_pool_wait_free` should be small\. For more information, see [Innodb\_buffer\_pool\_wait\_free](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html#statvar_Innodb_buffer_pool_wait_free) in the MySQL documentation\.

Increase the buffer pool size if the DB instance has enough memory for session buffers and operating\-system tasks\. If it doesn't, change the DB instance to a larger DB instance class to get additional memory that can be allocated to the buffer pool\.

**Note**  
Aurora MySQL automatically adjusts the value of `innodb_buffer_pool_instances` based on the configured `innodb_buffer_pool_size`\.

### Monitor the global status history<a name="ams-waits.bufpoolmutex.actions.action4"></a>

By monitoring the change rates of status variables, you can detect locking or memory issues on your DB instance\. Turn on Global Status History \(GoSH\) if it isn't already turned on\. For more information on GoSH, see [Managing the global status history](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.CommonDBATasks.html#Appendix.MySQL.CommonDBATasks.GoSH)\.

You can also create custom Amazon CloudWatch metrics to monitor status variables\. For more information, see [Publishing custom metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html)\.