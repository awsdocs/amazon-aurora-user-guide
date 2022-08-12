# Publishing Aurora PostgreSQL logs to Amazon CloudWatch Logs<a name="AuroraPostgreSQL.CloudWatch"></a>

You can configure your Aurora PostgreSQL DB cluster to export log data to Amazon CloudWatch Logs on a regular basis\. When you do so, events from your Aurora PostgreSQL DB cluster's PostgreSQL log are automatically *published* to Amazon CloudWatch, as Amazon CloudWatch Logs\. In CloudWatch, you can find the exported log data in a *Log group* for your Aurora PostgreSQL DB cluster\. The log group contains one or more *log streams* that contain the events from the PostgreSQL log from each instance in the cluster\. 

Publishing the logs to CloudWatch Logs allows you to keep your cluster's PostgreSQL log records in highly durable storage\. With the log data available in CloudWatch Logs, you can evaluate and improve your cluster's operations\. You can also use CloudWatch to create alarms and view metrics\. To learn more, see [Monitoring log events in Amazon CloudWatch](#AuroraPostgreSQL.CloudWatch.Monitor)\.

**Note**  
Publishing your PostgreSQL logs to CloudWatch Logs consumes storage, and you incur charges for that storage\. Be sure to delete any CloudWatch Logs that you no longer need\. 

Turning the export log option off for an existing Aurora PostgreSQL DB cluster doesn't affect any data that's already held in CloudWatch Logs\. Existing logs remain available in CloudWatch Logs based on your log retention settings\. To learn more about CloudWatch Logs, see [What is Amazon CloudWatch Logs?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) 

Aurora PostgreSQL supports publishing logs to CloudWatch Logs for the following versions\. 
+ 14\.3 and higher 14 versions
+ 13\.3 and higher 13 versions
+ 12\.8 and higher 12 versions
+ 11\.12 and higher 11 versions

## Turning on the option to publish logs to Amazon CloudWatch<a name="AuroraPostgreSQL.CloudWatch.Publishing"></a>

To publish your Aurora PostgreSQL DB cluster's PostgreSQL log to CloudWatch Logs, choose the **Log export** option for the cluster\. You can choose the Log export setting when you create your Aurora PostgreSQL DB cluster\. Or, you can modify the cluster later on\. When you modify an existing cluster, its PostgreSQL logs from each instance are published to CloudWatch cluster from that point on\. For Aurora PostgreSQL, the PostgreSQL log \(`postgresql.log`\) is the only log that gets published to Amazon CloudWatch\. 

You can use the AWS Management Console, the AWS CLI, or the RDS API to turn on the Log export feature for your Aurora PostgreSQL DB cluster\. 

### Console<a name="AuroraPostgreSQL.CloudWatch.Console"></a>

You choose the Log exports option to start publishing the PostgreSQL logs from your Aurora PostgreSQL DB cluster to CloudWatch Logs\.

**To turn on the Log export feature from the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora PostgreSQL DB cluster whose log data you want to publish to CloudWatch Logs\.

1. Choose **Modify**\.

1. In the **Log exports** section, choose **PostgreSQL log**\.

1. Choose **Continue**, and then choose **Modify cluster** on the summary page\.

### AWS CLI<a name="AuroraPostgreSQL.CloudWatch.CLI"></a>

You can turn on the log export option to start publishing Aurora PostgreSQL logs to Amazon CloudWatch Logs with the AWS CLI\. To do so, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command with the following options: 
+ `--db-cluster-identifier`—The DB cluster identifier\.
+ `--cloudwatch-logs-export-configuration`—The configuration setting for the log types to be set for export to CloudWatch Logs for the DB cluster\.

You can also publish Aurora PostgreSQL logs by running one of the following AWS CLI commands: 
+ [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)
+ [restore\-db\-cluster\-from\-s3](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-s3.html)
+ [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html)
+ [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)

Run one of these AWS CLI commands with the following options:
+ `--db-cluster-identifier`—The DB cluster identifier\.
+ `--engine`—The database engine\.
+ `--enable-cloudwatch-logs-exports`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

Other options might be required depending on the AWS CLI command that you run\.

**Example**  
The following command creates an Aurora PostgreSQL DB cluster to publish log files to CloudWatch Logs\.  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-cluster \
2.     --db-cluster-identifier my-db-cluster \
3.     --engine aurora-postgresql \
4.     --enable-cloudwatch-logs-exports postgresql
```
For Windows:  

```
1. aws rds create-db-cluster ^
2.     --db-cluster-identifier my-db-cluster ^
3.     --engine aurora-postgresql ^
4.     --enable-cloudwatch-logs-exports postgresql
```

**Example**  
The following command modifies an existing Aurora PostgreSQL DB cluster to publish log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `EnableLogTypes`, and its value is `postgresql`\.  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier my-db-cluster \
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["postgresql"]}'
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier my-db-cluster ^
3.     --cloudwatch-logs-export-configuration '{\"EnableLogTypes\":[\"postgresql\"]}'
```
When using the Windows command prompt, make sure to escape double quotation marks \("\) in JSON code by prefixing them with a backslash \(\\\)\.

**Example**  
The following example modifies an existing Aurora PostgreSQL DB cluster to disable publishing log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `DisableLogTypes`, and its value is `postgresql`\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
    --db-cluster-identifier mydbinstance \
    --cloudwatch-logs-export-configuration '{"DisableLogTypes":["postgresql"]}'
```
For Windows:  

```
aws rds modify-db-cluster ^
    --db-cluster-identifier mydbinstance ^
    --cloudwatch-logs-export-configuration "{\"DisableLogTypes\":[\"postgresql\"]}"
```
When using the Windows command prompt, you must escape double quotes \("\) in JSON code by prefixing them with a backslash \(\\\)\.

### RDS API<a name="AuroraPostgreSQL.CloudWatch.API"></a>

You can turn on the log export option to start publishing Aurora PostgreSQL logs with the RDS API\. To do so, run the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation with the following options: 
+ `DBClusterIdentifier` – The DB cluster identifier\.
+ `CloudwatchLogsExportConfiguration` – The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

You can also publish Aurora PostgreSQL logs with the RDS API by running one of the following RDS API operations: 
+ [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)
+ [RestoreDBClusterFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromS3.html)
+ [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html)
+ [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)

Run the RDS API action with the following parameters: 
+ `DBClusterIdentifier`—The DB cluster identifier\.
+ `Engine`—The database engine\.
+ `EnableCloudwatchLogsExports`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

Other parameters might be required depending on the AWS CLI command that you run\.

## Monitoring log events in Amazon CloudWatch<a name="AuroraPostgreSQL.CloudWatch.Monitor"></a>

With Aurora PostgreSQL log events published and available as Amazon CloudWatch Logs, you can view and monitor events using Amazon CloudWatch\. For more information about monitoring, see [View log data sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData)\.

When you turn on Log exports, a new log group is automatically created using the prefix `/aws/rds/cluster/` with the name of your Aurora PostgreSQL and the log type, as in the following pattern\. 

```
/aws/rds/cluster/your-cluster-name/postgresql
```

As an example, suppose that an Aurora PostgreSQL DB cluster named `docs-lab-apg-small` exports its log to Amazon CloudWatch Logs\. Its log group name in Amazon CloudWatch is shown following\.

```
/aws/rds/cluster/docs-lab-apg-small/postgresql
```

If a log group with the specified name exists, Aurora uses that log group to export log data for the Aurora DB cluster\. Each DB instance in the Aurora PostgreSQL DB cluster uploads its PostgreSQL log to the log group as a distinct log stream\. You can examine the log group and its log streams using the various graphical and analytical tools available in Amazon CloudWatch\.

For example, you can search for information within the log events from your Aurora PostgreSQL DB cluster, and filter events by using the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API\. For more information, [Searching and filtering log data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html) in the *Amazon CloudWatch Logs User Guide*\. 

By default, new log groups are created using **Never expire** for their retention period\. You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to change the log retention period\. To learn more, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.

**Tip**  
You can use automated configuration, such as AWS CloudFormation, to create log groups with predefined log retention periods, metric filters, and access permissions\. 

## Analyzing PostgreSQL logs using CloudWatch Logs Insights<a name="AuroraPostgreSQL.CloudWatch.Analyzing"></a>

With the PostgreSQL logs from your Aurora PostgreSQL DB cluster published as CloudWatch Logs, you can use CloudWatch Logs Insights to interactively search and analyze your log data in Amazon CloudWatch Logs\. CloudWatch Logs Insights includes a query language, sample queries, and other tools for analyzing your log data so that you can identify potential issues and verify fixes\. To learn more, see [Analyzing log data with CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) in the *Amazon CloudWatch Logs User Guide*\. Amazon CloudWatch Logs 

**To analyze PostgreSQL logs with CloudWatch Logs Insights**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, open **Logs** and choose **Log insights**\.

1. In **Select log group\(s\)**, select the log group for your Aurora PostgreSQL DB cluster\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-select-log-group.png)

1. In the query editor, delete the query that is currently shown, enter the following, and then choose **Run query**\.

   ```
   ##Autovacuum execution time in seconds per 5 minute
   fields @message
   | parse @message "elapsed: * s" as @duration_sec
   | filter @message like / automatic vacuum /
   | display @duration_sec
   | sort @timestamp
   | stats avg(@duration_sec) as avg_duration_sec, 
   max(@duration_sec) as max_duration_sec 
   by bin(5 min)
   ```  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-query.png)

1. Choose the **Visualization** tab\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-visualization.png)

1. Choose **Add to dashboard**\.

1. In **Select a dashboard**, either select a dashboard or enter a name to create a new dashboard\.

1. In **Widget type**, choose a widget type for your visualization\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-dashboard.png)

1. \(Optional\) Add more widgets based on your log query results\.

   1. Choose **Add widget**\.

   1. Choose a widget type, such as **Line**\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-widget.png)

   1. In the **Add to this dashboard** window, choose **Logs**\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-add-logs-to-dashboard.png)

   1. In **Select log group\(s\)**, select the log group for your DB cluster\.

   1. In the query editor, delete the query that is currently shown, enter the following, and then choose **Run query**\.

      ```
      ##Autovacuum tuples statistics per 5 min
      fields @timestamp, @message
      | parse @message "tuples: " as @tuples_temp
      | parse @tuples_temp "* removed," as @tuples_removed
      | parse @tuples_temp "remain, * are dead but not yet removable, " as @tuples_not_removable
      | filter @message like / automatic vacuum /
      | sort @timestamp
      | stats  avg(@tuples_removed) as avg_tuples_removed, 
      avg(@tuples_not_removable) as avg_tuples_not_removable 
      by bin(5 min)
      ```  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-query2.png)

   1. Choose **Create widget**\.

      Your dashboard should look similar to the following image\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-cwl-insights-dashboard-two-graphs.png)