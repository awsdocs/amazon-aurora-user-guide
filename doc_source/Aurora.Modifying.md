# Modifying an Amazon Aurora DB Cluster<a name="Aurora.Modifying"></a>

You can change the settings of a DB cluster to accomplish tasks such as changing its backup retention period or its database port\. You can also modify DB instances in a DB cluster to accomplish tasks such as changing its DB instance class or enabling Performance Insights for it\. This topic guides you through modifying an Aurora DB cluster and its DB instances, and describes the settings for each\. 

We recommend that you test any changes on a test DB cluster or DB instance before modifying a production DB cluster or DB instance, so that you fully understand the impact of each change\. This is especially important when upgrading database versions\. 

## Modifying the DB Cluster by Using the Console, CLI, and API<a name="Aurora.Modifying.Cluster"></a>

You can modify a DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
For Aurora, when you modify a DB cluster, only changes to the **DB cluster identifier**, **IAM DB authentication**, and **New master password** settings are affected by the **Apply Immediately** setting\. All other modifications are applied immediately, regardless of the value of the **Apply Immediately** setting\.

### Console<a name="Aurora.Modifying.Cluster.Console"></a>

**To modify a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB cluster that you want to modify\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 
**Note**  
 In the AWS Management Console, some instance level changes only apply to the current DB instance, while others apply to the entire DB cluster\. For information about whether a setting applies to the DB instance or the DB cluster, see the scope for the setting in [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. To change a setting that modifies the entire DB cluster at the instance level in the AWS Management Console, follow the instructions in [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, select **Apply immediately**\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="Aurora.Modifying.Cluster.CLI"></a>

To modify a DB cluster using the AWS CLI, call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\. Specify the DB cluster identifier, and the values for the settings that you want to modify\. For information about each setting, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

**Note**  
Some settings only apply to DB instances\. To change those settings, follow the instructions in [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\.

**Example**  
The following command modifies `mydbcluster` by setting the backup retention period to 1 week \(7 days\)\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
    --db-cluster-identifier mydbcluster \
    --backup-retention-period 7
```
For Windows:  

```
aws rds modify-db-cluster ^
    --db-cluster-identifier mydbcluster ^
    --backup-retention-period 7
```

### RDS API<a name="Aurora.Modifying.Cluster.API"></a>

To modify a DB cluster using the Amazon RDS API, call the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation\. Specify the DB cluster identifier, and the values for the settings that you want to modify\. For information about each parameter, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

**Note**  
Some settings only apply to DB instances\. To change those settings, follow the instructions in [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\.

## Modify a DB Instance in a DB Cluster<a name="Aurora.Modifying.Instance"></a>

You can modify a DB instance in a DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

When you modify a DB instance, you can apply the changes immediately\. To apply changes immediately, you select the **Apply Immediately** option in the AWS Management Console, you use the `--apply-immediately` parameter when calling the AWS CLI, or you set the `ApplyImmediately` parameter to `true` when using the Amazon RDS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. If you choose to apply changes immediately, your new changes and any changes in the pending modifications queue are applied\. 

**Important**  
If any of the pending modifications require downtime, choosing apply immediately can cause unexpected downtime for the DB instance\. There is no downtime for the other DB instances in the DB cluster\. 

### Console<a name="Aurora.Modifying.Instance.Console"></a>

**To modify a DB instance in a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB instance that you want to modify\. 

1. For **Actions**, choose **Modify**\. The **Modify DB Instance** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 
**Note**  
Some settings apply to the entire DB cluster and must be changed at the cluster level\. To change those settings, follow the instructions in [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\.   
 In the AWS Management Console, some instance level changes only apply to the current DB instance, while others apply to the entire DB cluster\. For information about whether a setting applies to the DB instance or the DB cluster, see the scope for the setting in [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, select **Apply immediately**\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="Aurora.Modifying.Instance.CLI"></a>

To modify a DB instance in a DB cluster by using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier, and the values for the settings that you want to modify\. For information about each parameter, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

**Note**  
Some settings apply to the entire DB cluster\. To change those settings, follow the instructions in [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\.

**Example**  
The following code modifies `mydbinstance` by setting the DB instance class to `db.r4.xlarge`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --db-instance-class db.r4.xlarge \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --db-instance-class db.r4.xlarge ^
    --no-apply-immediately
```

### RDS API<a name="Aurora.Modifying.Instance.API"></a>

To modify a MySQL instance by using the Amazon RDS API, call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation\. Specify the DB instance identifier, and the values for the settings that you want to modify\. For information about each parameter, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

**Note**  
Some settings apply to the entire DB cluster\. To change those settings, follow the instructions in [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\.

## Settings for Amazon Aurora<a name="Aurora.Modifying.Settings"></a>

The following table contains details about which settings you can modify, the methods for modifying the setting, and the scope of the setting\. The scope determines whether the setting applies to the entire DB cluster or if it can be set only for specific DB instances\. 


****  

| Setting and Description | Method | Scope | Downtime Notes | 
| --- | --- | --- | --- | 
|  **Auto minor version upgrade** Whether you want the DB instance to receive preferred minor engine version upgrades automatically when they become available\. Upgrades are installed only during your scheduled maintenance window\.  The **Auto minor version upgrade** setting applies to both Aurora PostgreSQL and Aurora MySQL DB clusters\. For Aurora MySQL 1\.x and 2\.x clusters, this setting upgrades the clusters to a maximum version of 1\.22\.2 and 2\.07\.2\. For more information about engine updates for Aurora PostgreSQL, see [Database Engine Updates for Amazon Aurora PostgreSQL ](AuroraPostgreSQL.Updates.md)\. For more information about engine updates for Aurora MySQL, see [Database Engine Updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md)\.  |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--auto-minor-version-upgrade\|--no-auto-minor-version-upgrade` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `AutoMinorVersionUpgrade` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Backup retention period** The number of days that automatic backups are retained\. The minimum value is `1`\.  For more information, see [Backups](Aurora.Managing.Backups.md#Aurora.Managing.Backups.Backup)\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--backup-retention-period` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `BackupRetentionPeriod` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Certificate Authority** The certificate that you want to use\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--ca-certificate-identifier` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `CACertificateIdentifier` parameter\.  |  Only the specified DB instance  |  An outage occurs during this change\. The DB instance is rebooted\.  | 
| Copy tags to snapshots Select to specify that tags defined for this DB cluster are copied to DB snapshots created from this DB cluster\. For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\. |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--copy-tags-to-snapshot` or `--no-copy-tags-to-snapshot` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `CopyTagsToSnapshot` parameter\.  | The entire DB cluster |  An outage doesn't occur during this change\.  | 
|  **Data API** You can access Aurora Serverless with web services–based applications, including AWS Lambda and AWS AppSync\. This setting only applies to an Aurora Serverless DB cluster\. For more information, see [Using the Data API for Aurora Serverless](data-api.md)\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--enable-http-endpoint` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `EnableHttpEndpoint` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  Database authentication The database authentication option you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and IAM DB authentication** to authenticate database users with database passwords and user credentials through IAM users and roles\. For more information, see [IAM Database Authentication](UsingWithRDS.IAMDBAuth.md)\.  |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--enable-iam-database-authentication\|--no-enable-iam-database-authentication` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `EnableIAMDatabaseAuthentication` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Database port** The port that you want to use to access the DB cluster\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--port` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `Port` parameter\.  |  The entire DB cluster  |  An outage occurs during this change\. All of the DB instances in the DB cluster are rebooted immediately\.  | 
|  **DB cluster identifier** The DB cluster identifier\. This value is stored as a lowercase string\.  When you change the DB cluster identifier, the DB cluster endpoint changes, and the endpoints of the DB instances in the DB cluster change\.  |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--new-db-cluster-identifier` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `NewDBClusterIdentifier` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **DB cluster parameter group** The DB cluster parameter group that you want associated with the DB cluster\.  For more information, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--db-cluster-parameter-group-name` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `DBClusterParameterGroupName` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instances in the DB cluster immediately without a reboot\. Changes to other parameters are applied only after the DB instances in the DB cluster are rebooted\.  | 
|  **DB engine version** The version of the DB engine that you want to use\. Before you upgrade your production DB cluster, we recommend that you test the upgrade process on a test DB cluster to verify its duration and to validate your applications\.  Currently, you can only use this setting to upgrade the minor engine version of an Aurora PostgreSQL DB cluster\.  |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--engine-version` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `EngineVersion` parameter\.  |  The entire DB cluster  |  An outage occurs during this change\.  | 
|  **DB instance class** The DB instance class that you want to use\.  For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--db-instance-class` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `DBInstanceClass` parameter\.  |  Only the specified DB instance  |  An outage occurs during this change\.  | 
|  **DB instance identifier** The DB instance identifier\. This value is stored as a lowercase string\.    |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--new-db-instance-identifier` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `NewDBInstanceIdentifier` parameter\.  |  Only the specified DB instance  |  An outage occurs during this change\. The DB instance is rebooted\.  | 
|  **DB parameter group** The DB parameter group that you want associated with the DB instance\.  For more information, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--db-parameter-group-name` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `DBParameterGroupName` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\. However, you must manually reboot the DB instance before the new DB parameter group is used by the DB instance\. For more information, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md) and [Rebooting a DB Instance in a DB Cluster](USER_RebootInstance.md)\.   | 
|  **Deletion protection** **Enable deletion protection** to prevent your DB cluster from being deleted\. For more information, see [Deleting a DB Instance in an Aurora DB Cluster](USER_DeleteInstance.md)\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--deletion-protection\|--no-deletion-protection` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `DeletionProtection` parameter\.  | The entire DB cluster |  An outage doesn't occur during this change\.  | 
|  **Enhanced monitoring** **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.  For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--monitoring-role-arn` and `--monitoring-interval` options\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `MonitoringRoleArn` and `MonitoringInterval` parameters\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Log exports** Select the log types to publish to Amazon CloudWatch Logs\.  For more information, see [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--cloudwatch-logs-export-configuration` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `CloudwatchLogsExportConfiguration` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Maintenance window** The time range during which system maintenance occurs\. System maintenance includes upgrades, if applicable\. The maintenance window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  If you set the window to the current time, there must be at least 30 minutes between the current time and end of the window to ensure any pending changes are applied\.  You can set the maintenance window independently for the DB cluster and for each DB instance in the DB cluster\. When the scope of a modification is the entire DB cluster, the modification is performed during the DB cluster maintenance window\. When the scope of a modification is the a DB instance, the modification is performed during maintenance window of that DB instance\. For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   |  To change the maintenance window for the DB cluster using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. To change the maintenance window for a DB instance using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. To change the maintenance window for the DB cluster using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--preferred-maintenance-window` option\. To change the maintenance window for a DB instance using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--preferred-maintenance-window` option\. To change the maintenance window for the DB cluster using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `PreferredMaintenanceWindow` parameter\. To change the maintenance window for a DB instance using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PreferredMaintenanceWindow` parameter\.  |  The entire DB cluster or a single DB instance  |  If there are one or more pending actions that cause an outage, and the maintenance window is changed to include the current time, then those pending actions are applied immediately, and an outage occurs\.  | 
|  **New master password** The password for your master user\. The password must contain from 8 to 41 alphanumeric characters\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--master-user-password` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `MasterUserPassword` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Performance Insights** Whether to enable Performance Insights, a tool that monitors your DB instance load so that you can analyze and troubleshoot your database performance\.  For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--enable-performance-insights\|--no-enable-performance-insights` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `EnablePerformanceInsights` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Performance Insights AWS KMS key identifier** The AWS KMS key identifier for encryption of Performance Insights data\. The AWS KMS key identifier is the Amazon Resource Name \(ARN\), key identifier, or key alias for the AWS KMS customer master key \(CMK\)\.  For more information, see [Enabling and Disabling Performance Insights](USER_PerfInsights.Enabling.md)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--performance-insights-kms-key-id` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PerformanceInsightsKMSKeyId` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Performance Insights retention period** The amount of time, in days, to retain Performance Insights data\. Valid values are 7 or 731 \(2 years\)\.  For more information, see [Enabling and Disabling Performance Insights](USER_PerfInsights.Enabling.md)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--performance-insights-retention-period` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PerformanceInsightsRetentionPeriod` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Promotion tier** A value that specifies the order in which an Aurora Replica is promoted to the primary instance in a cluster that uses single\-master replication, after a failure of the existing primary instance\.  For more information, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\.   |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--promotion-tier` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PromotionTier` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Public accessibility** Whether to give the DB instance a public IP address, meaning that it is accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. When the DB instance is not publicly accessible, it is accessible only from inside the VPC\.  For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.  To connect to a DB instance from outside of its Amazon VPC, the DB instance must be publicly accessible, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.  |  Using the AWS Management Console, [Modify a DB Instance in a DB Cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--publicly-accessible\|--no-publicly-accessible` option\. Using the RDS API, call [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PubliclyAccessible` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Scaling configuration** The scaling properties of the DB cluster\. You can only modify scaling properties for DB clusters in `serverless` DB engine mode\. This setting is available only for Aurora MySQL\.  For information about Aurora Serverless, see [Using Amazon Aurora Serverless](aurora-serverless.md)\.  |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--scaling-configuration` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `ScalingConfiguration` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Security group** The security group you want associated with the DB cluster\.  For more information, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--vpc-security-group-ids` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `VpcSecurityGroupIds` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Target Backtrack window** The amount of time you want to be able to backtrack your DB cluster, in seconds\. This setting is available only for Aurora MySQL and only if the DB cluster was created with Backtrack enabled\.   |  Using the AWS Management Console, [Modifying the DB Cluster by Using the Console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ `modify-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--backtrack-window` option\. Using the RDS API, call [ `ModifyDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `BacktrackWindow` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 

## Settings That Do Not Apply to Amazon Aurora<a name="Aurora.Modifying.SettingsNotApplicable"></a>

The following settings in the AWS CLI command [ `modify-db-instance`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and the RDS API operation [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) do not apply to Amazon Aurora\.

**Note**  
The AWS Management Console doesn't allow you to modify these settings for Aurora\.


****  

| AWS CLI Setting | RDS API Setting | 
| --- | --- | 
|  `--allocated-storage`  |  `AllocatedStorage`  | 
|  `--allow-major-version-upgrade\|--no-allow-major-version-upgrade`  |  `AllowMajorVersionUpgrade`  | 
|  `--copy-tags-to-snapshot\|--no-copy-tags-to-snapshot`  |  `CopyTagsToSnapshot`  | 
|  `--domain`  |  `Domain`  | 
|  `--db-security-groups`  |  `DBSecurityGroups`  | 
|  `--db-subnet-group-name`  |  `DBSubnetGroupName`  | 
|  `--domain-iam-role-name`  |  `DomainIAMRoleName`  | 
|  `--multi-az\|--no-multi-az`  |  `MultiAZ`  | 
|  `--iops`  |  `Iops`  | 
|  `--license-model`  |  `LicenseModel`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--preferred-backup-window`  |  `PreferredBackupWindow`  | 
|  `--processor-features`  |  `ProcessorFeatures`  | 
|  `--storage-type`  |  `StorageType`  | 
|  `--tde-credential-arn`  |  `TdeCredentialArn`  | 
|  `--tde-credential-password`  |  `TdeCredentialPassword`  | 
|  `--use-default-processor-features\|--no-use-default-processor-features`  |  `UseDefaultProcessorFeatures`  | 

**Note**  
Although the preferred backup window can be set for Aurora, the setting is ignored\. Aurora backups are continuous and incremental\.