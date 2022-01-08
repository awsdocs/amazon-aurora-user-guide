# Publishing Aurora PostgreSQL logs to Amazon CloudWatch Logs<a name="AuroraPostgreSQL.CloudWatch"></a>

You can configure your Aurora PostgreSQL DB cluster to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage\. Unlike with RDS for PostgreSQL which lets you publish both upgrade and postgresql logs, Aurora PostgreSQL supports uploading postgresql logs only to CloudWatch Logs\. 

 

Aurora PostgreSQL supports publishing logs to CloudWatch Logs for the following versions:
+ 13\.3 and higher 13 versions
+ 12\.4 and higher 12 versions
+ 11\.6 and higher 11 versions
+ 10\.11 and higher 10 versions

**Note**  
Be aware of the following:  
If exporting log data is disabled, Aurora doesn't delete existing log groups or log streams\.
If exporting log data is disabled, existing log data remains available in CloudWatch Logs based on log retention settings, which means you still incur charges for stored audit log data\.  You can delete log streams and log groups using the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API\.
If you don't want to export audit logs to CloudWatch Logs, make sure that all methods of exporting audit logs are disabled\. These methods are the AWS Management Console, the AWS CLI, and the RDS API\.

## Publishing logs to Amazon CloudWatch<a name="AuroraPostgreSQL.CloudWatch.Publishing"></a>

You can use the AWS Management Console, the AWS CLI, or the RDS API to publish Aurora PostgreSQL logs to Amazon CloudWatch Logs\.

### Console<a name="AuroraPostgreSQL.CloudWatch.Console"></a>

You can publish Aurora PostgreSQL logs to CloudWatch Logs with the console\.

**To publish Aurora PostgreSQL logs from the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora PostgreSQL DB cluster that you want to publish the log data for\.

1. Choose **Modify**\.

1. In the **Log exports** section, choose **Postgresql log**\.

1. Choose **Continue**, and then choose **Modify cluster** on the summary page\.

### AWS CLI<a name="AuroraPostgreSQL.CloudWatch.CLI"></a>

You can publish Aurora PostgreSQL logs with the AWS CLI\. You can run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command with the following options: 
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

You can publish Aurora PostgreSQL logs with the RDS API\. You can run the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation with the following options: 
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

After enabling Aurora PostgreSQL log events, you can monitor the events in Amazon CloudWatch Logs\. For more information about monitoring, see [View log data sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData)\.

A new log group is automatically created for the Aurora DB cluster under the following prefix\. In this prefix, `cluster-name` represents the DB cluster name and `log_type` represents the log type\.

```
/aws/rds/cluster/cluster-name/log_type
```

For example, suppose that you configure the export function to include the postgresql log for a DB cluster named `my-db-cluster`\. In this case, PostgreSQL log data is stored in the `/aws/rds/cluster/my-db-cluster/postgresql` log group\.

All of the events from all of the DB instances in a DB cluster are pushed to a log group using different log streams\.

If a log group with the specified name exists, Aurora uses that log group to export log data for the Aurora DB cluster\. You can use automated configuration, such as AWS CloudFormation, to create log groups with predefined log retention periods, metric filters, and customer access\. Otherwise, a new log group is automatically created using the default log retention period, **Never Expire**, in CloudWatch Logs\. You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to change the log retention period\. For more information about changing log retention periods in CloudWatch Logs, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html)\.

You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to search for information within the log events for a DB cluster\. For more information about searching and filtering log data, see [Searching and filtering log data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\.

## Analyze Aurora PostgreSQL logs using CloudWatch Logs Insights<a name="AuroraPostgreSQL.CloudWatch.Analyzing"></a>

After publishing Aurora PostgreSQL logs to CloudWatch Logs, you can analyze logs graphically and create dashboards using CloudWatch Logs Insights\.

**To analyze Aurora PostgreSQL logs with CloudWatch Logs Insights**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, open **Logs** and choose **Log insights**\.

1. In **Select log group\(s\)**, select the log group for your DB cluster\.  
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