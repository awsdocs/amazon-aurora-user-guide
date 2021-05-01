# Viewing Aurora metrics in the Amazon RDS console<a name="Aurora.Monitoring.Metrics.RDS"></a>

To monitor the health and performance of your Aurora DB cluster, you can view some, but not all, of the metrics provided by Amazon Aurora in the Amazon RDS console\. For a detailed list of Aurora metrics available to the Amazon RDS console, see [Availability of Aurora metrics in the Amazon RDS console](Aurora.Monitoring.Metrics.RDSAvailability.md)\.

**To view Aurora metrics in the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the name of the DB instance you want to monitor to see its details\. 

1. In the Cloudwatch section, choose one of the following options from **Monitoring** for how you want to view your metrics:
   + **Cloudwatch** – Shows a summary of CloudWatch metrics\. Each metric includes a graph showing the metric monitored over a specific time span\.
   + **Enhanced monitoring** – Shows a summary of OS metrics available to an Aurora DB instance with Enhanced Monitoring enabled\. Each metric includes a graph showing the metric monitored over a specific time span\. For more information, see [Monitoring OS metrics using Enhanced Monitoring](USER_Monitoring.OS.md)\.
   + **OS process list** – Shows the processes running on the DB instance or DB cluster and their related metrics including CPU percentage, memory usage, and so on\.   
![\[RDS metrics viewing options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraMetrics01.png)

1. The following image shows the metrics view with **Enhanced monitoring** selected\.  
![\[Latest Metrics View\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraMetrics02.png)