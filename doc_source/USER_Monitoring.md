# Viewing metrics in the Amazon RDS console<a name="USER_Monitoring"></a>

Amazon RDS integrates with Amazon CloudWatch to display a variety of Aurora DB cluster metrics in the RDS console\. Some metrics are apply at the cluster level, whereas others apply at the instance level\. For descriptions of the instance\-level and cluster\-level metrics, see [Metrics reference for Amazon Aurora](metrics-reference.md)\.

For your Aurora DB cluster, the following categories of metrics are monitored:
+ **CloudWatch** – Shows the Amazon CloudWatch metrics for Aurora that you can access in the RDS console\. You can also access these metrics in the CloudWatch console\. Each metric includes a graph that shows the metric monitored over a specific time span\. For a list of CloudWatch metrics,  see [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md)\.
+ **Enhanced monitoring** – Shows a summary of operating\-system metrics when your Aurora DB cluster has turned on Enhanced Monitoring\. RDS delivers the metrics from Enhanced Monitoring to your Amazon CloudWatch Logs account\. Each OS metric includes a graph showing the metric monitored over a specific time span\. For an overview, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\. For a list of Enhanced Monitoring metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.
+ **OS Process list** – Shows details for each process running in your DB cluster\.
+ **Performance Insights** – Opens the Amazon RDS Performance Insights dashboard for a DB instance in your Aurora DB cluster\. Performance Insights isn't supported at the cluster level\. For an overview of Performance Insights, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\. For a list of Performance Insights metrics, see [Amazon CloudWatch metrics for Performance Insights](USER_PerfInsights.Cloudwatch.md)\.

Amazon RDS now provides a consolidated view of Performance Insights and CloudWatch metrics in the Performance Insights dashboard\. Performance Insights must be turned on for your DB cluster to use this view\. You can choose the new monitoring view in the **Monitoring** tab or **Performance Insights** in the navigation pane\. To view the instructions for choosing this view, see [Viewing combined metrics in the Amazon RDS console](Viewing_Unifiedmetrics.md)\.

If you want to continue with the legacy monitoring view, continue with this procedure\.

**Note**  
The legacy monitoring view will be discontinued on December 15, 2023\.

**To view metrics for your DB cluster in the legacy monitoring view:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the Aurora DB cluster that you want to monitor\.

   The database page appears\. The following example shows an Amazon Aurora PostgreSQL database named `apga`\.  
![\[Database page with monitoring tab shown\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/cluster-with-monitoring-tab.png)

1. Scroll down and choose **Monitoring**\.

   The monitoring section appears\. By default, CloudWatch metrics are shown\. For descriptions of these metrics, see  [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md)\.  
![\[Database page with monitoring tab shown\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/cluster-monitoring-subpage.png)

1. Choose **Monitoring** to see the metric categories\.  
![\[Monitoring options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/monitoring-options.png)

1. Choose the category of metrics that you want to see\.

   The following example shows Enhanced Monitoring metrics\. For descriptions of these metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.  
![\[Enhanced Monitoring\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/cluster-em-metrics.png)
**Tip**  
To choose the time range of the metrics represented by the graphs, you can use the time range list\.  
To bring up a more detailed view, you can choose any graph\. You can also apply metric\-specific filters to the data\. 