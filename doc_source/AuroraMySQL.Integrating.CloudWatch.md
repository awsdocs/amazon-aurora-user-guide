# Publishing Amazon Aurora MySQL Logs to Amazon CloudWatch Logs<a name="AuroraMySQL.Integrating.CloudWatch"></a>

You can configure your Aurora MySQL DB cluster to publish general, slow, audit, and error log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage\.

To publish logs to CloudWatch Logs, the respective logs must be enabled\. Error logs are enabled by default, but you must enable the other types of logs explicitly\. For information about enabling logs in MySQL, see [Selecting General Query and Slow Query Log Output Destinations](https://dev.mysql.com/doc/refman/5.6/en/log-destinations.html) in the MySQL documentation\. For more information about enabling Aurora MySQL audit logs, see [Enabling Advanced Auditing](AuroraMySQL.Auditing.md#AuroraMySQL.Auditing.Enable)\. 

**Note**  
Be aware of the following:  
You can't publish logs to CloudWatch Logs for the China \(Ningxia\) region\.
If exporting log data is disabled, Aurora doesn't delete existing log groups or log streams\. If exporting log data is disabled, existing log data remains available in CloudWatch Logs, depending on log retention, and you still incur charges for stored audit log data\. You can delete log streams and log groups using the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API\.
An alternative way to publish audit logs to CloudWatch Logs is by enabling advanced auditing and setting the cluster\-level DB parameter `server_audit_logs_upload` to `1`\. The default for the `server_audit_logs_upload` parameter is `0`\.  
If you use this alternative method, you must have an IAM role to access CloudWatch Logs and set the `aws_default_logs_role` cluster\-level parameter to the ARN for this role\. For information about creating the role, see [Setting Up IAM Roles to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.md)\. However, if you have the `AWSServiceRoleForRDS` service\-linked role, it provides access to CloudWatch Logs and overrides any custom\-defined roles\. For information service\-linked roles for Amazon RDS, see [Using Service\-Linked Roles for Amazon Aurora](UsingWithRDS.IAM.ServiceLinkedRoles.md)\. 
If you don't want to export audit logs to CloudWatch Logs, make sure that all methods of exporting audit logs are disabled\. These methods are the AWS Management Console, the AWS CLI, the RDS API, and the `server_audit_logs_upload` parameter\.
 The procedure is slightly different for Aurora Serverless clusters than for provisioned clusters\. Serverless clusters automatically upload all the kinds of logs that you enable through the configuration parameters\. Therefore, you enable or disable log upload for Serverless clusters by turning different log types on and off in the DB cluster parameter group\. You don't modify the settings of the cluster itself through the AWS Management Console, AWS CLI, or RDS API\. For information about enabling MySQL logs for Serverless clusters, see [Aurora Serverless and Parameter Groups](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\. 

## Console<a name="AuroraMySQL.Integrating.CloudWatch.Console"></a>

You can publish Aurora MySQL logs for provisioned clusters to CloudWatch Logs with the console\.

**To publish Aurora MySQL logs from the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora MySQL DB cluster that you want to publish the log data for\.

1. Choose **Modify**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

1. Choose **Continue**, and then choose **Modify DB Cluster** on the summary page\.

## AWS CLI<a name="AuroraMySQL.Integrating.CloudWatch.CLI"></a>

You can publish Aurora MySQL logs for provisioned clusters with the AWS CLI\. To do so, you run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command with the following options: 
+ `--db-cluster-identifier`—The DB cluster identifier\.
+ `--cloudwatch-logs-export-configuration`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

You can also publish Aurora MySQL logs by running one of the following AWS CLI commands: 
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
The following command modifies an existing Aurora MySQL DB cluster to publish log files to CloudWatch Logs\.  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["error","general","slowquery","audit"]}'
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["error","general","slowquery","audit"]}'
```

**Example**  
The following command creates an Aurora MySQL DB cluster to publish log files to CloudWatch Logs\.  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --engine aurora \
4.     --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
```
For Windows:  

```
1. aws rds create-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --engine aurora ^
4.     --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
```

## RDS API<a name="AuroraMySQL.Integrating.CloudWatch.API"></a>

You can publish Aurora MySQL logs for provisioned clusters with the RDS API\. To do so, you run the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation with the following options: 
+ `DBClusterIdentifier`—The DB cluster identifier\.
+ `CloudwatchLogsExportConfiguration`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

You can also publish Aurora MySQL logs with the RDS API by running one of the following RDS API operations: 
+ [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)
+ [RestoreDBClusterFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromS3.html)
+ [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html)
+ [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)

Run the RDS API operation with the following parameters: 
+ `DBClusterIdentifier`—The DB cluster identifier\.
+ `Engine`—The database engine\.
+ `EnableCloudwatchLogsExports`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

Other parameters might be required depending on the AWS CLI command that you run\.

## Monitoring Log Events in Amazon CloudWatch<a name="AuroraMySQL.Integrating.CloudWatch.Monitor"></a>

After enabling Aurora MySQL log events, you can monitor the events in Amazon CloudWatch Logs\. A new log group is automatically created for the Aurora DB cluster under the following prefix, in which `cluster-name` represents the DB cluster name, and `log_type` represents the log type\.

```
/aws/rds/cluster/cluster-name/log_type
```

For example, if you configure the export function to include the slow query log for a DB cluster named `mydbcluster`, slow query data is stored in the `/aws/rds/cluster/mydbcluster/slowquery` log group\.

All of the events from all of the DB instances in a DB cluster are pushed to a log group using different log streams\.

If a log group with the specified name exists, Aurora uses that log group to export log data for the Aurora DB cluster\. You can use automated configuration, such as AWS CloudFormation, to create log groups with predefined log retention periods, metric filters, and customer access\. Otherwise, a new log group is automatically created using the default log retention period, **Never Expire**, in CloudWatch Logs\. You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to change the log retention period\. For more information about changing log retention periods in CloudWatch Logs, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html)\.

You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to search for information within the log events for a DB cluster\. For more information about searching and filtering log data, see [Searching and Filtering Log Data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\.