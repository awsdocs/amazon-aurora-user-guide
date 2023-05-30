# Overview of monitoring metrics in Amazon Aurora<a name="MonitoringOverview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon Aurora and your AWS solutions\. To more easily debug multi\-point failures, we recommend that you collect monitoring data from all parts of your AWS solution\.

**Topics**
+ [Monitoring plan](#MonitoringOverview.plan)
+ [Performance baseline](#MonitoringOverview.baseline)
+ [Performance guidelines](#MonitoringOverview.guidelines)
+ [Monitoring tools](#MonitoringOverview.tools)

## Monitoring plan<a name="MonitoringOverview.plan"></a>

Before you start monitoring Amazon Aurora, create a monitoring plan\. This plan should answer the following questions:
+ What are your monitoring goals?
+ Which resources will you monitor?
+ How often will you monitor these resources?
+ Which monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Whom should be notified when something goes wrong?

## Performance baseline<a name="MonitoringOverview.baseline"></a>

To achieve your monitoring goals, you need to establish a baseline\. To do this, measure performance under different load conditions at various times in your Amazon Aurora environment\. You can monitor metrics such as the following:
+ Network throughput
+ Client connections
+ I/O for read, write, or metadata operations
+ Burst credit balances for your DB instances

We recommend that you store historical performance data for Amazon Aurora\. Using the stored data, you can compare current performance against past trends\. You can also distinguish normal performance patterns from anomalies, and devise techniques to address issues\.

## Performance guidelines<a name="MonitoringOverview.guidelines"></a>

In general, acceptable values for performance metrics depend on what your application is doing relative to your baseline\. Investigate consistent or trending variances from your baseline\. The following metrics are often the source of performance issues:
+  **High CPU or RAM consumption** – High values for CPU or RAM consumption might be appropriate, if they're in keeping with your goals for your application \(like throughput or concurrency\) and are expected\. 
+  **Disk space consumption** – Investigate disk space consumption if space used is consistently at or above 85 percent of the total disk space\. See if it is possible to delete data from the instance or archive data to a different system to free up space\. 
+  **Network traffic** – For network traffic, talk with your system administrator to understand what expected throughput is for your domain network and internet connection\. Investigate network traffic if throughput is consistently lower than expected\. 
+  **Database connections** – If you see high numbers of user connections and also decreases in instance performance and response time, consider constraining database connections\. The best number of user connections for your DB instance varies based on your instance class and the complexity of the operations being performed\. To determine the number of database connections, associate your DB instance with a parameter group where the `User Connections` parameter is set to a value other than 0 \(unlimited\)\. You can either use an existing parameter group or create a new one\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 
+  **IOPS metrics** – The expected values for IOPS metrics depend on disk specification and server configuration, so use your baseline to know what is typical\. Investigate if values are consistently different than your baseline\. For best IOPS performance, make sure that your typical working set fits into memory to minimize read and write operations\. 

When performance falls outside your established baseline, you might need to make changes to optimize your database availability for your workload\. For example, you might need to change the instance class of your DB instance\. Or you might need to change the number of DB instances and read replicas that are available for clients\. 

## Monitoring tools<a name="MonitoringOverview.tools"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon Aurora and your other AWS solutions\. AWS provides various monitoring tools to watch Amazon Aurora, report when something is wrong, and take automatic actions when appropriate\.

**Topics**
+ [Automated monitoring tools](#MonitoringOverview.tools.automated)
+ [Manual monitoring tools](#monitoring_manual_tools)

### Automated monitoring tools<a name="MonitoringOverview.tools.automated"></a>

We recommend that you automate monitoring tasks as much as possible\.

**Topics**
+ [Amazon Aurora cluster status and recommendations](#MonitoringOverview.tools.automated.rds)
+ [Amazon CloudWatch metrics for Amazon Aurora](#MonitoringOverview.tools.automated.integrated)
+ [Amazon RDS Performance Insights and operating\-system monitoring](#MonitoringOverview.tools.automated.metrics.rds)
+ [Integrated services](#MonitoringOverview.tools.automated.integrated.events-logs-streams)

#### Amazon Aurora cluster status and recommendations<a name="MonitoringOverview.tools.automated.rds"></a>

You can use the following automated tools to watch Amazon Aurora and report when something is wrong:
+ **Amazon Aurora cluster status** — View details about the current status of your cluster by using the Amazon RDS console, the AWS CLI, or the RDS API\.
+ **Amazon Aurora recommendations** — Respond to automated recommendations for database resources, such as DB instances, DB clusters, and DB cluster parameter groups\. For more information, see [Viewing Amazon Aurora recommendations](accessing-monitoring.md#USER_Recommendations)\.

#### Amazon CloudWatch metrics for Amazon Aurora<a name="MonitoringOverview.tools.automated.integrated"></a>

Amazon Aurora integrates with Amazon CloudWatch for additional monitoring capabilities\.
+ **Amazon CloudWatch** – This service monitors your AWS resources and the applications you run on AWS in real time\. You can use the following Amazon CloudWatch features with Amazon Aurora:
  + **Amazon CloudWatch metrics** – Amazon Aurora automatically sends metrics to CloudWatch every minute for each active database\. You don't get additional charges for Amazon RDS metrics in CloudWatch\.  For more information, see [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md) 
  + **Amazon CloudWatch alarms** – You can watch a single Amazon Aurora metric over a specific time period\. You can then perform one or more actions based on the value of the metric relative to a threshold that you set\. 

#### Amazon RDS Performance Insights and operating\-system monitoring<a name="MonitoringOverview.tools.automated.metrics.rds"></a>

You can use the following automated tools to monitor Amazon Aurora performance:
+ **Amazon RDS Performance Insights** – Assess the load on your database, and determine when and where to take action\. For more information, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\.
+ **Amazon RDS Enhanced Monitoring** – Look at metrics in real time for the operating system\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.

#### Integrated services<a name="MonitoringOverview.tools.automated.integrated.events-logs-streams"></a>

The following AWS services are integrated with Amazon Aurora:
+ *Amazon EventBridge* is a serverless event bus service that makes it easy to connect your applications with data from a variety of sources\. For more information, see [Monitoring Amazon Aurora events](working-with-events.md)\.
+ *Amazon CloudWatch Logs* lets you monitor, store, and access your log files from Amazon Aurora instances, CloudTrail, and other sources\. For more information, see [Monitoring Amazon Aurora log files](USER_LogAccess.md)\.
+ *AWS CloudTrail* captures API calls and related events made by or on behalf of your AWS account and delivers the log files to an Amazon S3 bucket that you specify\. For more information, see [Monitoring Amazon Aurora API calls in AWS CloudTrail](logging-using-cloudtrail.md)\.
+ *Database Activity Streams* is an Amazon Aurora feature that provides a near\-real\-time stream of the activity in your DB cluster\. For more information, see [Monitoring Amazon Aurora with Database Activity Streams](DBActivityStreams.md)\.
+ *DevOps Guru for RDS* is a capability of Amazon DevOps Guru that applies machine learning to Performance Insights metrics for Amazon Aurora databases\. For more information, see [Analyzing performance anomalies with Amazon DevOps Guru for Amazon RDS](devops-guru-for-rds.md)\.

### Manual monitoring tools<a name="monitoring_manual_tools"></a>

You need to manually monitor those items that the CloudWatch alarms don't cover\. The Amazon RDS, CloudWatch, AWS Trusted Advisor and other AWS console dashboards provide an at\-a\-glance view of the state of your AWS environment\. We recommend that you also check the log files on your DB instance\.
+ From the Amazon RDS console, you can monitor the following items for your resources:
  + The number of connections to a DB instance
  + The amount of read and write operations to a DB instance
  + The amount of storage that a DB instance is currently using
  + The amount of memory and CPU being used for a DB instance
  + The amount of network traffic to and from a DB instance
+ From the Trusted Advisor dashboard, you can review the following cost optimization, security, fault tolerance, and performance improvement checks:
  + Amazon RDS Idle DB Instances
  + Amazon RDS Security Group Access Risk
  + Amazon RDS Backups
  + Amazon RDS Multi\-AZ
  + Aurora DB Instance Accessibility

  For more information on these checks, see [Trusted Advisor best practices \(checks\)](https://aws.amazon.com/premiumsupport/trustedadvisor/best-practices/)\.
+ CloudWatch home page shows:
  + Current alarms and status
  + Graphs of alarms and resources
  + Service health status

  In addition, you can use CloudWatch to do the following: 
  + Create [customized dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatch_Dashboards.html) to monitor the services that you care about\.
  + Graph metric data to troubleshoot issues and discover trends\.
  + Search and browse all your AWS resource metrics\.
  + Create and edit alarms to be notified of problems\.