# Monitoring an Amazon Aurora DB cluster<a name="MonitoringAurora"></a>

 This section shows how to monitor Amazon Aurora\. Aurora involves clusters of database servers that are connected in a replication topology\. Monitoring an Aurora cluster typically requires checking the health of multiple DB instances\. The instances might have specialized roles, handling mostly write operations, only read operations, or a combination of both\. You also monitor the overall health of the cluster by measuring the *replication lag*, the amount of time for changes made by one DB instance to be available to the other instances\. 

**Topics**
+ [Overview of monitoring Amazon Aurora](MonitoringOverview.md)
+ [Viewing an Amazon Aurora DB cluster](Aurora.Viewing.md)
+ [DB cluster status](Aurora.Status.md)
+ [DB instance status](Overview.DBInstance.Status.md)
+ [Monitoring Amazon Aurora DB cluster metrics](Aurora.Monitoring.md)
+ [Enhanced Monitoring](USER_Monitoring.OS.md)
+ [Using Amazon RDS Performance Insights](USER_PerfInsights.md)
+ [Using Amazon Aurora recommendations](USER_Recommendations.md)
+ [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)
+ [Using Amazon RDS event notification](USER_Events.md)
+ [Viewing Amazon RDS events](USER_ListEvents.md)
+ [Getting CloudWatch Events and Amazon EventBridge events for Amazon Aurora](rds-cloud-watch-events.md)
+ [Amazon Aurora database log files](USER_LogAccess.md)
+ [Working with AWS CloudTrail and Amazon RDS](logging-using-cloudtrail.md)