# Monitoring an Amazon Aurora DB cluster<a name="MonitoringAurora"></a>

This section shows how to monitor Amazon Aurora\. Aurora involves clusters of database servers that use replication\. Typically, monitoring an Aurora cluster requires checking the health of multiple DB instances\. The instances might have specialized roles, handling mostly write operations, only read operations, or a combination of both\. You also monitor the overall health of the cluster by measuring the *replication lag*\. This is the amount of time for changes made by one DB instance to be available to the other instances\. 

**Topics**
+ [Overview of monitoring Amazon Aurora](MonitoringOverview.md)
+ [Viewing an Amazon Aurora DB cluster](Aurora.Viewing.md)
+ [DB cluster status](Aurora.Status.md)
+ [DB instance status](Overview.DBInstance.Status.md)
+ [Using Amazon Aurora recommendations](USER_Recommendations.md)
+ [Using Performance Insights on Amazon Aurora](USER_PerfInsights.md)
+ [Using Enhanced Monitoring](USER_Monitoring.OS.md)
+ [Using Amazon RDS event notification](USER_Events.md)
+ [Viewing Amazon RDS events](USER_ListEvents.md)
+ [Accessing Amazon Aurora database log files](USER_LogAccess.md)
+ [Monitoring Amazon Aurora metrics with Amazon CloudWatch](Aurora.Monitoring.md)
+ [Publishing database engine logs to Amazon CloudWatch Logs](publishing_cloudwatchlogs.md)
+ [Getting CloudWatch Events and Amazon EventBridge events for Amazon Aurora](rds-cloud-watch-events.md)
+ [Working with AWS CloudTrail and Amazon RDS](logging-using-cloudtrail.md)
+ [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)