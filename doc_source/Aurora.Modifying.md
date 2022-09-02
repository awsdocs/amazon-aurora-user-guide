# Modifying an Amazon Aurora DB cluster<a name="Aurora.Modifying"></a>

You can change the settings of a DB cluster to accomplish tasks such as changing its backup retention period or its database port\. You can also modify DB instances in a DB cluster to accomplish tasks such as changing its DB instance class or enabling Performance Insights for it\. This topic guides you through modifying an Aurora DB cluster and its DB instances, and describes the settings for each\. 

We recommend that you test any changes on a test DB cluster or DB instance before modifying a production DB cluster or DB instance, so that you fully understand the impact of each change\. This is especially important when upgrading database versions\. 

## Modifying the DB cluster by using the console, CLI, and API<a name="Aurora.Modifying.Cluster"></a><a name="modify_cluster"></a>

You can modify a DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
For Aurora, when you modify a DB cluster, only changes to the **DB cluster identifier**, **IAM DB authentication**, and **New master password** settings are affected by the **Apply immediately** setting\. All other modifications are applied immediately, regardless of the value of the **Apply immediately** setting\.

### Console<a name="Aurora.Modifying.Cluster.Console"></a>

**To modify a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB cluster that you want to modify\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 
**Note**  
 In the AWS Management Console, some instance level changes only apply to the current DB instance, while others apply to the entire DB cluster\. For information about whether a setting applies to the DB instance or the DB cluster, see the scope for the setting in [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. To change a setting that modifies the entire DB cluster at the instance level in the AWS Management Console, follow the instructions in [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, select **Apply immediately**\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="Aurora.Modifying.Cluster.CLI"></a>

To modify a DB cluster using the AWS CLI, call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\. Specify the DB cluster identifier, and the values for the settings that you want to modify\. For information about each setting, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

**Note**  
Some settings only apply to DB instances\. To change those settings, follow the instructions in [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\.

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
Some settings only apply to DB instances\. To change those settings, follow the instructions in [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\.

## Modify a DB instance in a DB cluster<a name="Aurora.Modifying.Instance"></a><a name="modify_instance"></a>

You can modify a DB instance in a DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

When you modify a DB instance, you can apply the changes immediately\. To apply changes immediately, you select the **Apply Immediately** option in the AWS Management Console, you use the `--apply-immediately` parameter when calling the AWS CLI, or you set the `ApplyImmediately` parameter to `true` when using the Amazon RDS API\. 

If you don't choose to apply changes immediately, the changes are deferred until the next maintenance window\. During the next maintenance window, any of these deferred changes are applied\. If you choose to apply changes immediately, your new changes and any previously deferred changes are applied\. 

**Important**  
If any of the deferred modifications require downtime, choosing **Apply immediately** can cause unexpected downtime for the DB instance\. There is no downtime for the other DB instances in the DB cluster\.   
 Modifications that you defer aren't listed in the output of the `describe-pending-maintenance-actions` CLI command\. Maintenance actions only include system upgrades that you schedule for the next maintenance window\. 

### Console<a name="Aurora.Modifying.Instance.Console"></a>

**To modify a DB instance in a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB instance that you want to modify\. 

1. For **Actions**, choose **Modify**\. The **Modify DB Instance** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 
**Note**  
Some settings apply to the entire DB cluster and must be changed at the cluster level\. To change those settings, follow the instructions in [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\.   
 In the AWS Management Console, some instance level changes only apply to the current DB instance, while others apply to the entire DB cluster\. For information about whether a setting applies to the DB instance or the DB cluster, see the scope for the setting in [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, select **Apply immediately**\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="Aurora.Modifying.Instance.CLI"></a>

To modify a DB instance in a DB cluster by using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier, and the values for the settings that you want to modify\. For information about each parameter, see [Settings for Amazon Aurora](#Aurora.Modifying.Settings)\. 

**Note**  
Some settings apply to the entire DB cluster\. To change those settings, follow the instructions in [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\.

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
Some settings apply to the entire DB cluster\. To change those settings, follow the instructions in [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\.

## Settings for Amazon Aurora<a name="Aurora.Modifying.Settings"></a>

The following table contains details about which settings you can modify, the methods for modifying the setting, and the scope of the setting\. The scope determines whether the setting applies to the entire DB cluster or if it can be set only for specific DB instances\. 

**Note**  
Additional settings are available if you are modifying an Aurora Serverless v1 or Aurora Serverless v2 DB cluster\. For information about these settings, see [Modifying an Aurora Serverless v1 DB cluster](aurora-serverless.modifying.md) and [Managing Aurora Serverless v2](aurora-serverless-v2-administration.md)\.  
Some settings aren't available for Aurora Serverless v1 and Aurora Serverless v2 because of their limitations\. For more information, see [Limitations of Aurora Serverless v1](aurora-serverless.md#aurora-serverless.limitations) and [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\.


****  

| Setting and description | Method | Scope | Downtime notes | 
| --- | --- | --- | --- | 
|  **Auto minor version upgrade** Whether you want the DB instance to receive preferred minor engine version upgrades automatically when they become available\. Upgrades are installed only during your scheduled maintenance window\.  For more information about engine updates, see [Amazon Aurora PostgreSQL updates](AuroraPostgreSQL.Updates.md) and [Database engine updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md)\. For more information about the **Auto minor version upgrade** setting for Aurora MySQL, see [Enabling automatic upgrades between minor Aurora MySQL versions](AuroraMySQL.Updates.Patching.md#AuroraMySQL.Updates.AMVU)\.   |    This setting is enabled by default\. For each new cluster, choose the appropriate value for this setting based on its importance, expected lifetime, and the amount of verification testing that you do after each upgrade\.   When you change this setting, perform this modification for every DB instance in your Aurora cluster\. If any DB instance in your cluster has this setting turned off, the cluster isn't automatically upgraded\.  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--auto-minor-version-upgrade\|--no-auto-minor-version-upgrade` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `AutoMinorVersionUpgrade` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\. Outages do occur during future maintenance windows when Aurora applies automatic upgrades\.  | 
|  **Backup retention period** The number of days that automatic backups are retained\. The minimum value is `1`\.  For more information, see [Backups](Aurora.Managing.Backups.md#Aurora.Managing.Backups.Backup)\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--backup-retention-period` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `BackupRetentionPeriod` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Backup window \(Start time\)** The time range during which automated backups of your database occurs\. The backup window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  Aurora backups are continuous and incremental, but the backup window is used to create a daily system backup that is preserved within the backup retention period\. You can copy it to preserve it outside of the retention period\. The maintenance window and the backup window for the DB cluster can't overlap\. For more information, see [Backup window](Aurora.Managing.Backups.md#Aurora.Managing.Backups.BackupWindow)\.  |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--preferred-backup-window` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `PreferredBackupWindow` parameter\.  |  The entire DB cluster\.  |  An outage doesn't occur during this change\.  | 
|  **Capacity range** The database capacity measured in Aurora Capacity Units \(ACUs\)\. For more information, see [Setting the Aurora Serverless v2 capacity range for a cluster](aurora-serverless-v2-administration.md#aurora-serverless-v2-setting-acus)\.  |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--serverless-v2-scaling-configuration` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `ServerlessV2ScalingConfiguration` parameter\.  |  The entire DB cluster\.  |  An outage doesn't occur during this change\.  | 
|  **Certificate Authority** The certificate that you want to use\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--ca-certificate-identifier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `CACertificateIdentifier` parameter\.  |  Only the specified DB instance  |  An outage occurs during this change\. The DB instance is rebooted\.  | 
| Copy tags to snapshots Select to specify that tags defined for this DB cluster are copied to DB snapshots created from this DB cluster\. For more information, see [Tagging Amazon RDS resources](USER_Tagging.md)\. |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--copy-tags-to-snapshot` or `--no-copy-tags-to-snapshot` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `CopyTagsToSnapshot` parameter\.  | The entire DB cluster |  An outage doesn't occur during this change\.  | 
|  **Data API** You can access Aurora Serverless v1 with web services–based applications, including AWS Lambda and AWS AppSync\. This setting only applies to an Aurora Serverless v1 DB cluster\. For more information, see [Using the Data API for Aurora Serverless v1](data-api.md)\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--enable-http-endpoint` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `EnableHttpEndpoint` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  Database authentication The database authentication you want to use\.For MySQL:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Modifying.html)For PostgreSQL:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Modifying.html) |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [ modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the following options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Modifying.html) Using the RDS API, call [ ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the following parameters: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Modifying.html)  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Database port** The port that you want to use to access the DB cluster\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--port` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `Port` parameter\.  |  The entire DB cluster  |  An outage occurs during this change\. All of the DB instances in the DB cluster are rebooted immediately\.  | 
|  **DB cluster identifier** The DB cluster identifier\. This value is stored as a lowercase string\.  When you change the DB cluster identifier, the DB cluster endpoint changes, and the endpoints of the DB instances in the DB cluster change\.  |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--new-db-cluster-identifier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `NewDBClusterIdentifier` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **DB cluster parameter group** The DB cluster parameter group that you want associated with the DB cluster\.  For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--db-cluster-parameter-group-name` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `DBClusterParameterGroupName` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instances in the DB cluster immediately without a reboot\. Changes to other parameters are applied only after the DB instances in the DB cluster are rebooted\.  | 
|  **DB instance class** The DB instance class that you want to use\.  For more information, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--db-instance-class` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `DBInstanceClass` parameter\.  |  Only the specified DB instance  |  An outage occurs during this change\.  | 
|  **DB instance identifier** The DB instance identifier\. This value is stored as a lowercase string\.    |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--new-db-instance-identifier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `NewDBInstanceIdentifier` parameter\.  |  Only the specified DB instance  |  An outage occurs during this change\. The DB instance is rebooted\.  | 
|  **DB parameter group** The DB parameter group that you want associated with the DB instance\.  For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--db-parameter-group-name` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `DBParameterGroupName` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\. When you associate a new DB parameter group with a DB instance, the modified static and dynamic parameters are applied only after the DB instance is rebooted\. However, if you modify dynamic parameters in the newly associated DB parameter group, these changes are applied immediately without a reboot\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md) and [Rebooting an Amazon Aurora DB cluster or Amazon Aurora DB instance](USER_RebootCluster.md)\.   | 
|  **Deletion protection** **Enable deletion protection** to prevent your DB cluster from being deleted\. For more information, see [Deletion protection for Aurora clusters](USER_DeleteCluster.md#USER_DeletionProtection)\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--deletion-protection\|--no-deletion-protection` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `DeletionProtection` parameter\.  | The entire DB cluster |  An outage doesn't occur during this change\.  | 
|  **Engine version** The version of the DB engine that you want to use\. Before you upgrade your production DB cluster, we recommend that you test the upgrade process on a test DB cluster to verify its duration and to validate your applications\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--engine-version` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `EngineVersion` parameter\.  |  The entire DB cluster  |  An outage occurs during this change\.  | 
|  **Enhanced monitoring** **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.  For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--monitoring-role-arn` and `--monitoring-interval` options\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `MonitoringRoleArn` and `MonitoringInterval` parameters\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Log exports** Select the log types to publish to Amazon CloudWatch Logs\.  For more information, see [Aurora MySQL database log files](USER_LogAccess.Concepts.MySQL.md)\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--cloudwatch-logs-export-configuration` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `CloudwatchLogsExportConfiguration` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Maintenance window** The time range during which system maintenance occurs\. System maintenance includes upgrades, if applicable\. The maintenance window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  If you set the window to the current time, there must be at least 30 minutes between the current time and end of the window to ensure any pending changes are applied\.  You can set the maintenance window independently for the DB cluster and for each DB instance in the DB cluster\. When the scope of a modification is the entire DB cluster, the modification is performed during the DB cluster maintenance window\. When the scope of a modification is the a DB instance, the modification is performed during maintenance window of that DB instance\. The maintenance window and the backup window for the DB cluster can't overlap\. For more information, see [The Amazon RDS maintenance window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   |  To change the maintenance window for the DB cluster using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. To change the maintenance window for a DB instance using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. To change the maintenance window for the DB cluster using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--preferred-maintenance-window` option\. To change the maintenance window for a DB instance using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--preferred-maintenance-window` option\. To change the maintenance window for the DB cluster using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `PreferredMaintenanceWindow` parameter\. To change the maintenance window for a DB instance using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PreferredMaintenanceWindow` parameter\.  |  The entire DB cluster or a single DB instance  |  If there are one or more pending actions that cause an outage, and the maintenance window is changed to include the current time, then those pending actions are applied immediately, and an outage occurs\.  | 
|  **Network type** The IP addressing protocols supported by the DB cluster\. **IPv4** to specify that resources can communicate with the DB cluster only over the IPv4 addressing protocol\. **Dual\-stack mode** to specify that resources can communicate with the DB cluster over IPv4, IPv6, or both\. Use dual\-stack mode if you have any resources that must communicate with your DB cluster over the IPv6 addressing protocol\. To use dual\-stack mode, make sure at least two subnets spanning two Availability Zones that support both the IPv4 and IPv6 network protocol\. Also, make sure you associate an IPv6 CIDR block with subnets in the DB subnet group you specify\. For more information, see [Amazon Aurora IP addressing](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.IP_addressing)\.  |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `-network-type` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `NetworkType` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **New master password** The password for your master user\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Modifying.html)  |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--master-user-password` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `MasterUserPassword` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Performance Insights** Whether to enable Performance Insights, a tool that monitors your DB instance load so that you can analyze and troubleshoot your database performance\.  For more information, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--enable-performance-insights\|--no-enable-performance-insights` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `EnablePerformanceInsights` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Performance Insights AWS KMS key** The AWS KMS key identifier for encryption of Performance Insights data\. The KMS key identifier is the Amazon Resource Name \(ARN\), key identifier, or key alias for the KMS key\.  For more information, see [Turning Performance Insights on and off](USER_PerfInsights.Enabling.md)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--performance-insights-kms-key-id` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PerformanceInsightsKMSKeyId` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Performance Insights retention period** The amount of time, in days, to retain Performance Insights data\. The retention setting in the free tier is **Default \(7 days\)**\. To retain your performance data for longer, specify 1–24 months\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\.  For more information, see [Turning Performance Insights on and off](USER_PerfInsights.Enabling.md)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--performance-insights-retention-period` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PerformanceInsightsRetentionPeriod` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Promotion tier** A value that specifies the order in which an Aurora Replica is promoted to the primary instance in a cluster that uses single\-master replication, after a failure of the existing primary instance\.  For more information, see [Fault tolerance for an Aurora DB cluster](Concepts.AuroraHighAvailability.md#Aurora.Managing.FaultTolerance)\.   |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--promotion-tier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PromotionTier` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Public access** **Publicly accessible** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **Not publicly accessible** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB cluster in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.  To connect to a DB instance from outside of its Amazon VPC, the DB instance must be publicly accessible, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. If your DB instance is isn't publicly accessible, you can also use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |  Using the AWS Management Console, [Modify a DB instance in a DB cluster](#Aurora.Modifying.Instance)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and set the `--publicly-accessible\|--no-publicly-accessible` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) and set the `PubliclyAccessible` parameter\.  |  Only the specified DB instance  |  An outage doesn't occur during this change\.  | 
|  **Scaling configuration** The scaling properties of the DB cluster\. You can only modify scaling properties for DB clusters in `serverless` DB engine mode\. This setting is available only for Aurora MySQL\.  For information about Aurora Serverless v1, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\.  |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--scaling-configuration` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `ScalingConfiguration` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Security group** The security group you want associated with the DB cluster\.  For more information, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--vpc-security-group-ids` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `VpcSecurityGroupIds` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 
|  **Target Backtrack window** The amount of time you want to be able to backtrack your DB cluster, in seconds\. This setting is available only for Aurora MySQL and only if the DB cluster was created with Backtrack enabled\.   |  Using the AWS Management Console, [Modifying the DB cluster by using the console, CLI, and API](#Aurora.Modifying.Cluster)\. Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and set the `--backtrack-window` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) and set the `BacktrackWindow` parameter\.  |  The entire DB cluster  |  An outage doesn't occur during this change\.  | 

## Settings that don't apply to Amazon Aurora DB clusters<a name="Aurora.Modifying.SettingsNotApplicableDBClusters"></a>

The following settings in the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) don't apply to Amazon Aurora DB clusters\.

**Note**  
You can't use the AWS Management Console to modify these settings for Aurora DB clusters\.


****  

| AWS CLI setting | RDS API setting | 
| --- | --- | 
|  `--allocated-storage`  |  `AllocatedStorage`  | 
|  `--auto-minor-version-upgrade \| --no-auto-minor-version-upgrade`  |  `AutoMinorVersionUpgrade`  | 
|  `--db-cluster-instance-class`  |  `DBClusterInstanceClass`  | 
|  `--enable-performance-insights \| --no-enable-performance-insights`  |  `EnablePerformanceInsights`  | 
|  `--iops`  |  `Iops`  | 
|  `--monitoring-interval`  |  `MonitoringInterval`  | 
|  `--monitoring-role-arn`  |  `MonitoringRoleArn`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--performance-insights-kms-key-id`  |  `PerformanceInsightsKMSKeyId`  | 
|  `--performance-insights-retention-period`  |  `PerformanceInsightsRetentionPeriod`  | 
|  `--storage-type`  |  `StorageType`  | 

## Settings that don't apply to Amazon Aurora DB instances<a name="Aurora.Modifying.SettingsNotApplicable"></a>

The following settings in the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) don't apply to Amazon Aurora DB instances\.

**Note**  
You can't use the AWS Management Console to modify these settings for Aurora DB instances\.


****  

| AWS CLI setting | RDS API setting | 
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
|  `--network-type`  |  `NetworkType`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--processor-features`  |  `ProcessorFeatures`  | 
|  `--storage-type`  |  `StorageType`  | 
|  `--tde-credential-arn`  |  `TdeCredentialArn`  | 
|  `--tde-credential-password`  |  `TdeCredentialPassword`  | 
|  `--use-default-processor-features\|--no-use-default-processor-features`  |  `UseDefaultProcessorFeatures`  | 