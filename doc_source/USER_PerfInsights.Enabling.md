# Turning Performance Insights on and off<a name="USER_PerfInsights.Enabling"></a>

You can turn on Performance Insights for your DB cluster when you create it\. If needed, you can turn it off later at the instance level for any instance in your DB cluster\. Turning Performance Insights on and off doesn't cause downtime, a reboot, or a failover\.

**Note**  
Performance Schema is an optional performance tool used by Aurora MySQL\. If you turn Performance Schema on or off, you need to reboot\. If you turn Performance Insights on or off, however, you don't need to reboot\. For more information, see [Turning on the Performance Schema for Performance Insights on Aurora MySQL](USER_PerfInsights.EnableMySQL.md)\.

If you use Performance Insights with Aurora global databases, turn on Performance Insights individually for the DB instances in each AWS Region\. For details, see [Monitoring an Amazon Aurora global database with Amazon RDS Performance Insights](aurora-global-database-monitoring.md#aurora-global-database-pi)\. 

The Performance Insights agent consumes limited CPU and memory on the DB host\. When the DB load is high, the agent limits the performance impact by collecting data less frequently\.

## Console<a name="USER_PerfInsights.Enabling.Console"></a>

In the console, you can turn Performance Insights on or off when you create or modify a DB cluster\.

### Turning Performance Insights on or off when creating a DB cluster<a name="USER_PerfInsights.Console.Creating"></a>

When you create a new DB cluster, turn on Performance Insights by choosing **Enable Performance Insights** in the **Performance Insights** section\. Or choose **Disable Performance Insights**\. To create a DB cluster, follow the instructions for your DB engine in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

The following screenshot shows the **Performance Insights** section\.

![\[Turn on Performance Insights during DB cluster creation with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/./images/perf_insights_enabling.png)

If you choose **Enable Performance Insights**, you have the following options:
+ **Retention** – The amount of time to retain Performance Insights data\. The retention setting in the free tier is **Default \(7 days\)**\. To retain your performance data for longer, specify 1–24 months\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\.
+ **AWS KMS key** – Specify your AWS KMS key\. Performance Insights encrypts all potentially sensitive data using your KMS key\. Data is encrypted in flight and at rest\. For more information, see [Configuring an AWS KMS policy for Performance Insights](USER_PerfInsights.access-control.md#USER_PerfInsights.access-control.cmk-policy)\.

### Turning Performance Insights on or off when modifying a DB instance in your DB cluster<a name="USER_PerfInsights.Enabling.Console.Modifying"></a>

In the console, you can modify a DB instance in your DB cluster to turn Performance Insights on or off\. You can't turn Performance Insights on or off at the cluster level: you must do it for each instance in the cluster\.

**To turn Performance Insights on or off for a DB instance in your DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases**\.

1. Choose a DB instance, and choose **Modify**\.

1. In the **Performance Insights** section, choose either **Enable Performance Insights** or **Disable Performance Insights**\.

   If you choose **Enable Performance Insights**, you have the following options:
   + **Retention** – The amount of time to retain Performance Insights data\. The retention setting in the free tier is **Default \(7 days\)**\. To retain your performance data for longer, specify 1–24 months\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\.
   + **AWS KMS key** – Specify your KMS key\. Performance Insights encrypts all potentially sensitive data using your KMS key\. Data is encrypted in flight and at rest\. For more information, see [Encrypting Amazon Aurora resources](Overview.Encryption.md)\.

1. Choose **Continue**\.

1. For **Scheduling of Modifications**, choose Apply immediately\. If you choose Apply during the next scheduled maintenance window, your instance ignores this setting and turns on Performance Insights immediately\.

1. Choose **Modify instance**\.

## AWS CLI<a name="USER_PerfInsights.Enabling.CLI"></a>

When you use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command, turn on Performance Insights by specifying `--enable-performance-insights`\. Or turn off Performance Insights by specifying `--no-enable-performance-insights`\.

You can also specify these values using the following AWS CLI commands:
+  [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) 
+  [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) 
+  [restore\-db\-instance\-from\-s3](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html) 

The following procedure describes how to turn Performance Insights on or off for an existing DB instance in your DB cluster using the AWS CLI\.

**To turn Performance Insights on or off for a DB instance in your DB cluster using the AWS CLI**
+ Call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command and supply the following values:
  + `--db-instance-identifier` – The name of the DB instance in your DB cluster\.
  + `--enable-performance-insights` to turn on or `--no-enable-performance-insights` to turn off

  The following example turns on Performance Insights for `sample-db-instance`\.

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier sample-db-instance \
      --enable-performance-insights
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier sample-db-instance ^
      --enable-performance-insights
  ```

When you turn on Performance Insights in the CLI, you can optionally specify the number of days to retain Performance Insights data with the `--performance-insights-retention-period` option\. You can specify `7`, *month* \* 31 \(where *month* is a number from 1–23\), or `731`\. For example, if you want to retain your performance data for 3 months, specify `93`, which is 3 \* 31\. The default is `7` days\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\.

The following example turns on Performance Insights for `sample-db-instance` and specifies that Performance Insights data is retained for 93 days \(3 months\)\.

For Linux, macOS, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier sample-db-instance \
    --enable-performance-insights \
    --performance-insights-retention-period 93
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier sample-db-instance ^
    --enable-performance-insights ^
    --performance-insights-retention-period 93
```

If you specify a retention period such as 94 days, which isn't a valid value, RDS issues an error\.

```
An error occurred (InvalidParameterValue) when calling the CreateDBInstance operation: 
Invalid Performance Insights retention period. Valid values are: [7, 31, 62, 93, 124, 155, 186, 217, 
248, 279, 310, 341, 372, 403, 434, 465, 496, 527, 558, 589, 620, 651, 682, 713, 731]
```

## RDS API<a name="USER_PerfInsights.Enabling.API"></a>

When you create a new DB instance in your DB cluster using the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation Amazon RDS API operation, turn on Performance Insights by setting `EnablePerformanceInsights` to `True`\. To turn off Performance Insights, set `EnablePerformanceInsights` to `False`\.

You can also specify the `EnablePerformanceInsights` value using the following API operations:
+  [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) 
+  [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) 
+  [RestoreDBInstanceFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html) 

When you turn on Performance Insights, you can optionally specify the amount of time, in days, to retain Performance Insights data with the `PerformanceInsightsRetentionPeriod` parameter\. You can specify `7`, *month* \* 31 \(where *month* is a number from 1–23\), or `731`\. For example, if you want to retain your performance data for 3 months, specify `93`, which is 3 \* 31\. The default is `7` days\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\.