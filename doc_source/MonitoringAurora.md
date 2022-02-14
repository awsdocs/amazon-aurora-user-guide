# Monitoring metrics in an Amazon Aurora cluster<a name="MonitoringAurora"></a>

Amazon Aurora uses a cluster of replicated database servers\. Typically, monitoring an Aurora cluster requires checking the health of multiple DB instances\. The instances might have specialized roles, handling mostly write operations, only read operations, or a combination\. You also monitor the overall health of the cluster by measuring the *replication lag*\. This is the amount of time for changes made by one DB instance to be available to the other instances\.

**Topics**
+ [Overview of monitoring metrics in Amazon Aurora](MonitoringOverview.md)
+ [Viewing cluster status and recommendations](accessing-monitoring.md)
+ [Viewing metrics in the Amazon RDS console](USER_Monitoring.md)
+ [Monitoring Amazon Aurora metrics with Amazon CloudWatch](monitoring-cloudwatch.md)
+ [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)
+ [Analyzing performance anomalies with DevOps Guru for RDS](devops-guru-for-rds.md)
+ [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)
+ [Metrics reference for Amazon Aurora](metrics-reference.md)