# Monitoring an Amazon Aurora DB cluster<a name="MonitoringAurora"></a>

Amazon Aurora uses a cluster of replicated database servers\. Typically, monitoring an Aurora cluster requires checking the health of multiple DB instances\. The instances might have specialized roles, handling mostly write operations, only read operations, or a combination\. You also monitor the overall health of the cluster by measuring the *replication lag*\. This is the amount of time for changes made by one DB instance to be available to the other instances\. 

**Topics**
+ [Overview of monitoring Amazon Aurora](MonitoringOverview.md)
+ [Viewing key monitoring information](accessing-monitoring.md)
+ [Using Performance Insights on Amazon Aurora](USER_PerfInsights.md)
+ [Monitoring OS metrics using Enhanced Monitoring](USER_Monitoring.OS.md)
+ [Monitoring Amazon Aurora metrics with Amazon CloudWatch](Aurora.Monitoring.md)
+ [Working with Amazon RDS events](working-with-aurora-events.md)
+ [Working with Amazon Aurora database log files](USER_LogAccess.md)
+ [Working with AWS CloudTrail and Amazon RDS](logging-using-cloudtrail.md)
+ [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)