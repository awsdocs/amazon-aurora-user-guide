# Overview of Amazon Aurora and Amazon CloudWatch<a name="cw-metrics-overview"></a>

By default, Amazon Aurora automatically sends metric data to CloudWatch in 1\-minute periods\. For example, the `CPUUtilization` metric records the percentage of CPU utilization for a DB instance over time\. Data points with a period of 60 seconds \(1 minute\) are available for 15 days\. This means that you can access historical information and see how your web application or service is performing\.

As shown in the following diagram, you can set up alarms for your CloudWatch metrics\. For example, you might create an alarm that signals when the CPU utilization for an instance is over 70%\. You can configure Amazon Simple Notification Service to email you when the threshold is passed\.

![\[RDS metrics in AWS CloudWatch\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/rds-cloudwatch.png)

Amazon RDS publishes the following types of metrics to Amazon CloudWatch:
+ Aurora metrics at both the cluster and instance level

  For a table of these metrics, see [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md)\.
+ Performance Insights metrics

  For a table of these metrics, see [Amazon CloudWatch metrics for Performance Insights](USER_PerfInsights.Cloudwatch.md) and [Performance Insights counter metrics](USER_PerfInsights_Counters.md)\.
+ Enhanced Monitoring metrics \(published to Amazon CloudWatch Logs\)

  For a table of these metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.
+ Usage metrics for the Amazon RDS service quotas in your AWS account

  For a table of these metrics, see [Amazon CloudWatch usage metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md#rds-metrics-usage)\. For more information about Amazon RDS quotas, see [Quotas and constraints for Amazon Aurora](CHAP_Limits.md)\.

For more information about CloudWatch, see [ What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\. For more information about CloudWatch metrics retention, see [Metrics retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_concepts.html#metrics-retention)\.