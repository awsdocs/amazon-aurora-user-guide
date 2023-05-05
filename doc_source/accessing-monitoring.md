# Viewing cluster status and recommendations<a name="accessing-monitoring"></a>

Using the Amazon RDS console, you can quickly access the status of your DB cluster and respond to Amazon Aurora recommendations\.

**Topics**
+ [Viewing an Amazon Aurora DB cluster](#Aurora.Viewing)
+ [Viewing DB cluster status](#Aurora.Status)
+ [Viewing DB instance status in an Aurora cluster](#Overview.DBInstance.Status)
+ [Viewing Amazon Aurora recommendations](#USER_Recommendations)

## Viewing an Amazon Aurora DB cluster<a name="Aurora.Viewing"></a>

You have several options for viewing information about your Amazon Aurora DB clusters and the DB instances in your DB clusters\.
+ You can view DB clusters and DB instances in the Amazon RDS console by choosing **Databases** from the navigation pane\. 
+ You can get DB cluster and DB instance information using the AWS Command Line Interface \(AWS CLI\)\.
+ You can get DB cluster and DB instance information using the Amazon RDS API\.

### Console<a name="Aurora.Viewing.Console"></a>

In the Amazon RDS console, you can see details about a DB cluster by choosing **Databases** from the console's navigation pane\. You can also see details about DB instances that are members of an Amazon Aurora DB cluster\.

**To view or modify DB clusters in the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the Aurora DB cluster that you want to view from the list\.

   For example, the following image shows the details page for the DB cluster named `aurora-test`\. The DB cluster has four DB instances shown in the DB identifier list\. The writer DB instance, `dbinstance4`, is the primary DB instance for the DB cluster\.  
![\[Amazon Aurora DB Cluster View\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraView01.png)

1. To modify a DB cluster, select the DB cluster from the list and choose **Modify**\.

**To view or modify DB instances of a DB cluster in the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Do one of the following:
   + To view a DB instance, choose one from the list that is a member of the Aurora DB cluster\.

     For example, if you choose the `dbinstance4` DB instance identifier, the console shows the details page for the `dbinstance4` DB instance, as shown in the following image\.  
![\[Amazon Aurora DB Instance View\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraView02.png)
   + To modify a DB instance, choose the DB instance from the list and choose **Modify**\. For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\. 

### AWS CLI<a name="Aurora.Viewing.CLI"></a>

To view DB cluster information by using the AWS CLI, use the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. For example, the following AWS CLI command lists the DB cluster information for all of the DB clusters in the modify `us-east-1` region for the configured AWS account\.

```
aws rds describe-db-clusters --region us-east-1
```

The command returns the following output if your AWS CLI is configured for JSON output\.

```
{
    "DBClusters": [
        {
            "Status": "available",
            "Engine": "aurora-mysql",
            "Endpoint": "sample-cluster1.cluster-123456789012.us-east-1.rds.amazonaws.com"
            "AllocatedStorage": 1,
            "DBClusterIdentifier": "sample-cluster1",
            "MasterUsername": "mymasteruser",
            "EarliestRestorableTime": "2023-03-30T03:35:42.563Z",
            "DBClusterMembers": [
                {
                    "IsClusterWriter": false,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "sample-replica"
                },
                {
                    "IsClusterWriter": true,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "sample-primary"
                }
            ],
            "Port": 3306,
            "PreferredBackupWindow": "03:34-04:04",
            "VpcSecurityGroups": [
                {
                    "Status": "active",
                    "VpcSecurityGroupId": "sg-ddb65fec"
                }
            ],
            "DBSubnetGroup": "default",
            "StorageEncrypted": false,
            "DatabaseName": "sample",
            "EngineVersion": "5.7.mysql_aurora.2.11.0",
            "DBClusterParameterGroup": "default.aurora-mysql5.7",
            "BackupRetentionPeriod": 1,
            "AvailabilityZones": [
                "us-east-1b",
                "us-east-1c",
                "us-east-1d"
            ],
            "LatestRestorableTime": "2023-03-31T20:06:08.903Z",
            "PreferredMaintenanceWindow": "wed:08:15-wed:08:45"
        },
        {
            "Status": "available",
            "Engine": "aurora-mysql",
            "Endpoint": "aurora-sample.cluster-123456789012.us-east-1.rds.amazonaws.com",
            "AllocatedStorage": 1,
            "DBClusterIdentifier": "aurora-sample-cluster",
            "MasterUsername": "mymasteruser",
            "EarliestRestorableTime": "2023-03-30T10:21:34.826Z",
            "DBClusterMembers": [
                {
                    "IsClusterWriter": false,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "aurora-replica-sample"
                },
                {
                    "IsClusterWriter": true,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "aurora-sample"
                }
            ],
            "Port": 3306,
            "PreferredBackupWindow": "10:20-10:50",
            "VpcSecurityGroups": [
                {
                    "Status": "active",
                    "VpcSecurityGroupId": "sg-55da224b"
                }
            ],
            "DBSubnetGroup": "default",
            "StorageEncrypted": false,
            "DatabaseName": "sample",
            "EngineVersion": "5.7.mysql_aurora.2.11.0",
            "DBClusterParameterGroup": "default.aurora-mysql5.7",
            "BackupRetentionPeriod": 1,
            "AvailabilityZones": [
                "us-east-1b",
                "us-east-1c",
                "us-east-1d"
            ],
            "LatestRestorableTime": "2023-03-31T20:00:11.491Z",
            "PreferredMaintenanceWindow": "sun:03:53-sun:04:23"
        }
    ]
}
```

### RDS API<a name="Aurora.Viewing.API"></a>

To view DB cluster information using the Amazon RDS API, use the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) operation\.

## Viewing DB cluster status<a name="Aurora.Status"></a>

The status of a DB cluster indicates its health\. You can view the status of a DB cluster and the cluster instances by using the Amazon RDS console, the AWS CLI, or the API\.

**Note**  
Aurora also uses another status called *maintenance status*, which is shown in the **Maintenance** column of the Amazon RDS console\. This value indicates the status of any maintenance patches that need to be applied to a DB cluster\. Maintenance status is independent of DB cluster status\. For more information about maintenance status, see [Applying updates for a DB cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\.

Find the possible status values for DB clusters in the following table\.


| DB cluster status | Billed | Description | 
| --- | --- | --- | 
| Available | Billed |  The DB cluster is healthy and available\. When an Aurora Serverless cluster is available and paused, you're billed for storage only\.  | 
| Backing\-up | Billed |  The DB cluster is currently being backed up\.  | 
| Backtracking | Billed |  The DB cluster is currently being backtracked\. This status only applies to Aurora MySQL\.  | 
| Cloning\-failed | Not billed |  Cloning a DB cluster failed\.  | 
| Creating | Not billed |  The DB cluster is being created\. The DB cluster is inaccessible while it is being created\.  | 
| Deleting | Not billed |  The DB cluster is being deleted\.  | 
| Failing\-over | Billed |  A failover from the primary instance to an Aurora Replica is being performed\.  | 
| Inaccessible\-encryption\-credentials | Not billed |  The AWS KMS key used to encrypt or decrypt the DB cluster can't be accessed or recovered\.  | 
|  **Inaccessible\-encryption\-credentials\-recoverable**  | Billed for storage |  The KMS key used to encrypt or decrypt the DB cluster can't be accessed\. However, if the KMS key is active, restarting the DB cluster can recover it\. For more information, see [Encrypting an Amazon Aurora DB cluster](Overview.Encryption.md#Overview.Encryption.Enabling)\.  | 
| Maintenance | Billed |  Amazon RDS is applying a maintenance update to the DB cluster\. This status is used for DB cluster\-level maintenance that RDS schedules well in advance\.  | 
| Migrating | Billed |  A DB cluster snapshot is being restored to a DB cluster\.  | 
| Migration\-failed | Not billed |  A migration failed\.  | 
| Modifying | Billed |  The DB cluster is being modified because of a customer request to modify the DB cluster\.  | 
| Promoting | Billed |  A read replica is being promoted to a standalone DB cluster\.  | 
| Renaming | Billed |  The DB cluster is being renamed because of a customer request to rename it\.  | 
| Resetting\-master\-credentials | Billed |  The master credentials for the DB cluster are being reset because of a customer request to reset them\.  | 
| Starting | Billed for storage |  The DB cluster is starting\.  | 
| Stopped | Billed for storage |  The DB cluster is stopped\.  | 
| Stopping | Billed for storage |  The DB cluster is being stopped\.  | 
| Storage\-optimization | Billed |  Your DB instance is being modified to change the storage size or type\. The DB instance is fully operational\. However, while the status of your DB instance is **storage\-optimization**, you can't request any changes to the storage of your DB instance\. The storage optimization process is usually short, but can sometimes take up to and even beyond 24 hours\.  | 
| Update\-iam\-db\-auth | Billed |  IAM authorization for the DB cluster is being updated\.  | 
| Upgrading | Billed |  The DB cluster engine version is being upgraded\.  | 

### Console<a name="DBcluster.Status.Console"></a>

**To view the status of a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

   The **Databases page** appears with the list of DB clusters\. For each DB cluster, the status value is displayed\.  
![\[Viewing the status of a DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Aurora_cluster_status.png)

### CLI<a name="DBcluster.Status.Cli"></a>

To view just the status of the DB clusters, use the following query in AWS CLI\.

```
aws rds describe-db-clusters --query 'DBClusters[*].[DBClusterIdentifier,Status]' --output table
```

## Viewing DB instance status in an Aurora cluster<a name="Overview.DBInstance.Status"></a>

The status of a DB instance in an Aurora cluster indicates the health of the DB instance\. You can use the following procedures to view the DB instance status of a cluster in the Amazon RDS console, the AWS CLI command, or the API operation\.

**Note**  
Amazon RDS also uses another status called *maintenance status*, which is shown in the **Maintenance** column of the Amazon RDS console\. This value indicates the status of any maintenance patches that need to be applied to a DB instance\. Maintenance status is independent of DB instance status\. For more information about maintenance status, see [Applying updates for a DB cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\. 

Find the possible status values for DB instances in the following table\. This table also shows whether you will be billed for the DB instance and storage, billed only for storage, or not billed\. For all DB instance statuses, you are always billed for backup usage\.


| DB instance status | Billed  | Description | 
| --- | --- | --- | 
|  **Available**  | Billed |  The DB instance is healthy and available\.  | 
|  **Backing\-up**  | Billed |  The DB instance is currently being backed up\.  | 
| Backtracking | Billed |  The DB instance is currently being backtracked\. This status only applies to Aurora MySQL\.  | 
|  **Configuring\-enhanced\-monitoring**  | Billed |  Enhanced Monitoring is being enabled or disabled for this DB instance\.  | 
|  **Configuring\-iam\-database\-auth**  | Billed |  AWS Identity and Access Management \(IAM\) database authentication is being enabled or disabled for this DB instance\.  | 
|  **Configuring\-log\-exports**  | Billed |  Publishing log files to Amazon CloudWatch Logs is being enabled or disabled for this DB instance\.  | 
|  **Converting\-to\-vpc**  | Billed |  The DB instance is being converted from a DB instance that is not in an Amazon Virtual Private Cloud \(Amazon VPC\) to a DB instance that is in an Amazon VPC\.  | 
|  **Creating**  | Not billed |  The DB instance is being created\. The DB instance is inaccessible while it is being created\.   | 
|  **Delete\-precheck**  | Not billed |  Amazon RDS is validating that read replicas are healthy and are safe to delete\.  | 
|  **Deleting**  | Not billed |  The DB instance is being deleted\.  | 
|  **Failed**  | Not billed |  The DB instance has failed and Amazon RDS can't recover it\. Perform a point\-in\-time restore to the latest restorable time of the DB instance to recover the data\.   | 
|  **Inaccessible\-encryption\-credentials**  | Not billed |  The AWS KMS key used to encrypt or decrypt the DB instance can't be accessed or recovered\.   | 
|  **Inaccessible\-encryption\-credentials\-recoverable**  | Billed for storage |  The KMS key used to encrypt or decrypt the DB instance can't be accessed\. However, if the KMS key is active, restarting the DB instance can recover it\. For more information, see [Encrypting an Amazon Aurora DB cluster](Overview.Encryption.md#Overview.Encryption.Enabling)\.  | 
|  **Incompatible\-network**  | Not billed |  Amazon RDS is attempting to perform a recovery action on a DB instance but can't do so because the VPC is in a state that prevents the action from being completed\. This status can occur if, for example, all available IP addresses in a subnet are in use and Amazon RDS can't get an IP address for the DB instance\.   | 
|  **Incompatible\-option\-group**  | Billed |  Amazon RDS attempted to apply an option group change but can't do so, and Amazon RDS can't roll back to the previous option group state\. For more information, check the **Recent Events** list for the DB instance\. This status can occur if, for example, the option group contains an option such as TDE and the DB instance doesn't contain encrypted information\.   | 
|  **Incompatible\-parameters**  | Billed |  Amazon RDS can't start the DB instance because the parameters specified in the DB instance's DB parameter group aren't compatible with the DB instance\. Revert the parameter changes or make them compatible with the DB instance to regain access to your DB instance\. For more information about the incompatible parameters, check the **Recent Events** list for the DB instance\.   | 
|  **Incompatible\-restore**  | Not billed |  Amazon RDS can't do a point\-in\-time restore\. Common causes for this status include using temp tables or using MyISAM tables with MySQL\.  | 
| Insufficient\-capacity | Not billed |  Amazon RDS can’t create your instance because sufficient capacity isn’t currently available\. To create your DB instance in the same AZ with the same instance type, delete your DB instance, wait a few hours, and try to create again\. Alternatively, create a new instance using a different instance class or AZ\.  | 
|  **Maintenance**  | Billed |  Amazon RDS is applying a maintenance update to the DB instance\. This status is used for instance\-level maintenance that RDS schedules well in advance\.   | 
|  **Modifying**  | Billed |  The DB instance is being modified because of a customer request to modify the DB instance\.   | 
|  **Moving\-to\-vpc**  | Billed |  The DB instance is being moved to a new Amazon Virtual Private Cloud \(Amazon VPC\)\.  | 
|  **Rebooting**  | Billed |  The DB instance is being rebooted because of a customer request or an Amazon RDS process that requires the rebooting of the DB instance\.  | 
|  **Resetting\-master\-credentials**  | Billed |  The master credentials for the DB instance are being reset because of a customer request to reset them\.  | 
|  **Renaming**  | Billed |  The DB instance is being renamed because of a customer request to rename it\.   | 
|  **Restore\-error**  | Billed |  The DB instance encountered an error attempting to restore to a point\-in\-time or from a snapshot\.  | 
|  **Starting**  | Billed for storage |  The DB instance is starting\.  | 
|  **Stopped**  | Billed for storage |  The DB instance is stopped\.  | 
|  **Stopping**  | Billed for storage |  The DB instance is being stopped\.  | 
|  **Storage\-full**  | Billed |  The DB instance has reached its storage capacity allocation\. This is a critical status, and we recommend that you fix this issue immediately\. To do so, scale up your storage by modifying the DB instance\. To avoid this situation, set Amazon CloudWatch alarms to warn you when storage space is getting low\.   | 
|  **Storage\-optimization**  | Billed |  Amazon RDS is optimizing the storage of your DB instance\. The DB instance is fully operational\. The storage optimization process is usually short, but can sometimes take up to and even beyond 24 hours\.  | 
|  **Upgrading**  | Billed |  The database engine version is being upgraded\.   | 

### Console<a name="DBinstance.Status.Console"></a>

**To view the status of a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

   The **Databases page** appears with the list of DB instances\. For each DB instance in a cluster, the status value is displayed\.   
![\[View the status of a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Aurora_instance_status.png)

### CLI<a name="DBinstance.Status.Cli"></a>

To view DB instance and its status information by using the AWS CLI, use the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command\. For example, the following AWS CLI command lists all the DB instances information \.

```
aws rds describe-db-instances
```

To view a specific DB instance and its status, call the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command with the following option:
+ `DBInstanceIdentifier` – The name of the DB instance\. 

```
aws rds describe-db-instances --db-instance-identifier mydbinstance
```

To view just the status of all the DB instances, use the following query in AWS CLI\.

```
aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,DBInstanceStatus]' --output table
```

### API<a name="DBinstance.Status.Api"></a>

To view the status of the DB instance using the Amazon RDS API, call the [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\.

## Viewing Amazon Aurora recommendations<a name="USER_Recommendations"></a>

Amazon Aurora provides automated recommendations for database resources, such as DB instances, DB clusters, and DB cluster parameter groups\. These recommendations provide best practice guidance by analyzing DB cluster configuration,  DB instance configuration, usage, and performance data\.

You can find examples of these recommendations in the following table\.


| Type | Description | Recommendation | Additional information | 
| --- | --- | --- | --- | 
|  Reader instances in the cluster are in the same Availability Zone  |  Your DB cluster has all the reader instances in the same Availability Zone\.  |  We recommend that you distribute the Reader instances across multiple Availability Zones\. Distribution increases the availability of the database, and improves the response time by reducing network latency between clients and database\.  |  [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  Enhanced Monitoring disabled  |  Your DB instance doesn't have Enhanced Monitoring enabled\.  |  We recommend enabling Enhanced Monitoring\. Enhanced Monitoring provides real\-time operating system metrics for monitoring and troubleshooting\.  |  [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)  | 
|  Performance Insights disabled  |  Your DB instance doesn't have Performance Insights enabled\.  |  We recommend enabling Performance Insights\. Performance Insights monitors your database load for better analysis and troubleshooting\.  |  [Overview of Performance Insights on Amazon Aurora](USER_PerfInsights.Overview.md)  | 
|  Encryption isn't enabled for the DB Cluster  |  Your DB clusters don’t have the encryption enabled\.  |  We recommend that you enable encryption of data at rest for your DB Cluster\. You can turn on encryption while creating a DB cluster\. You can’t convert an unencrypted DB cluster to an encrypted one\. However, you can restore an unencrypted snapshot to an encrypted Aurora DB cluster\. To do this, specify a KMS key when you restore from the unencrypted snapshot\.  |  [Encrypting Amazon Aurora resources](Overview.Encryption.md)  | 
|  Nondefault custom memory parameters  |  Your DB parameter group sets memory parameters that diverge too much from the default values\.  |  Settings that diverge too much from the default values can cause poor performance and errors\. We recommend setting custom memory parameters to their default values in the DB parameter group used by the DB instance\.  |  [Working with parameter groups](USER_WorkingWithParamGroups.md)  | 
|  Found an unsafe durability parameter value for a MySQL DB instance  |  Your DB instance has an unsafe value for the `innodb_flush_log_at_trx_commit` parameter\. This parameter controls the persistence of commit operations to disk\.  |  We recommend that you set the value of the `innodb_flush_log_at_trx_commit` parameter to `1`\. The current value might improve performance but transactions can be lost if the database crashes\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](http://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  Optimizer statistics aren't persisted to the disk for a MySQL DB instance  |  Your DB instance isn't configured to persist the InnoDB statistics to the disk\. When it isn't configured, the statistics may recalculate frequently, which leads to variations in query execution plan\. You can modify the value of this global parameter at the table level\.  |  Global statistics persistence is disabled\. We recommend that you set the `innodb_stats_persistent` parameter to `ON`\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](http://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  General logging is enabled for a MySQL DB instance  |  Your DB instance has the general logging turned on\. Turning on general logging increases the amount of I/O operations and allocated storage space,which can lead to contention and performance degradation\.  |  Evaluate your required general logging usage\. General logging can increase the amount of I/O operations and allocated storage space, and lead to contention and performance degradation\.  |  [Managing table\-based Aurora MySQL logs](Appendix.MySQL.CommonDBATasks.Logs.md)  | 
|  Maximum InnoDB open files setting is misconfigured for a MySQL DB instance  |  Your DB instance has a low value for the maximum number of files InnoDB can open at one time\.  |  We recommend that you set the `innodb_open_files` parameter to a minimum value of `65`\.  |  [innodb\_open\_files](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_open_files)  | 
|  Number of allowed simultaneous connections for a given database user is misconfigured for a MySQL DB instance  |  Your DB instance has a low value for the maximum number of simultaneous connections for each database account\.  |  We recommend that you increase the setting of the `max_user_connections` parameter to a number greater than 5\. The current `max_user_connections` value is low which impacts the database health checks and regular operations\.  |  [Setting Account Resource Limits](https://dev.mysql.com/doc/refman/8.0/en/user-resources.html)  | 
|  Found an unsafe durability parameter value for a MySQL DB instance  |  The synchronization of the binary log to disk isn't enforced before the acknowledgement of the transactions commit in your DB instance\.  |  We recommend that you set the `sync_binlog` parameter to `1`\. Currently, the synchronization of the binary log to disk isn't enforced before acknowledgement of the transactions commit\. If there is a power failure or the operating system crashes, the committed transactions can be lost\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 2: Parameters related to replication](http://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-2-parameters-related-to-replication/) on the AWS Database Blog  | 
|  Change buffering enabled for a MySQL DB instance  |  Your DB parameter group has change buffering enabled\.  |  Change buffering allows a MySQL DB instance to defer some writes necessary to maintain secondary indexes\. This configuration can improve performance slightly, but it can create a large delay in crash recovery\. During crash recovery, the secondary index must be brought up to date\. So, the benefits of change buffering are outweighed by the potentially very long crash recovery events\. We recommend disabling change buffering\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](http://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  Autovacuum is disabled for a PostgreSQL DB instance  |  Your DB instance has autovacuum turned off\. Turning off autovacuum increases table and index bloat and impacts performance\.  |  We recommend that you set the autovacuum parameter to on\.  |  [Understanding autovacuum in Amazon RDS for PostgreSQL environments](https://aws.amazon.com/blogs/database/understanding-autovacuum-in-amazon-rds-for-postgresql-environments/)  | 
|  Synchronous commit is turned off for a PostgreSQL DB instance  |  When the `synchronous_commit` parameter is set to `OFF`, it causes data loss when the database crashes, which can impact the durability of the database\.  |  We recommend that you turn on the `synchronous_commit` parameter\.  |  [Asynchronous Commit](https://www.postgresql.org/docs/current/wal-async-commit.html)  | 
|  `track_counts` parameter is disabled for a PostgreSQL DB instance  |  If the track\_counts parameter is turned off, the database doesn't collect the database activity statistics\. Autovacuum requires these statistics to work correctly\.  |  We recommend that you set `track_counts` parameter to `ON`\.  |  [track\_counts \(boolean\)](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS)  | 
|  Index only scan plan type is disabled for a PostgreSQL DB instance  |  The query planner or optimizer can't use the index only scan plan when it is disabled\.   |  We recommend that you set the parameter `enable_indexonlyscan` to `ON`\.  |  [enable\_indexonlyscan \(boolean\)](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN)  | 
|  index\-scan plan type is disabled for a PostgreSQL DB instance  |  The query planner or optimizer can't use the index scan plan types when it is disabled\.  |  We recommend that you set the parameter `enable_indexscan` to `ON`\.  |  [enable\_indexscan \(boolean\) ](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN)  | 
|  Logging to table  |  Your DB parameter group sets logging output to `TABLE`\.  |  Setting logging output to `TABLE` uses more storage than setting this parameter to `FILE`\. To avoid reaching the storage limit, we recommend setting the logging output parameter to `FILE`\.  |  [Aurora MySQL database log files](USER_LogAccess.Concepts.MySQL.md)  | 
|  DB cluster with one DB instance  |  Your DB cluster only contains one DB instance\.  |  For improved performance and availability, we recommend adding another DB instance with the same DB instance class in a different Availability Zone\.  |  [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  DB cluster in one Availability Zone  |  Your DB cluster has all of its DB instances in the same Availability Zone\.  |  For improved availability, we recommend adding another DB instance with the same DB instance class in a different Availability Zone\.  |  [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  DB cluster outdated  |  Your DB cluster is running an older engine version\.  |  We recommend that you keep your DB cluster at the most current minor version because it includes the latest security and functionality fixes\. Unlike major version upgrades, minor version upgrades include only changes that are backward\-compatible with previous minor versions \(of the same major version\) of the DB engine\. We recommend that you upgrade to a recent engine version  |  [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)  | 
|  DB cluster with different parameter groups  |  Your DB cluster has different DB parameter groups assigned to its DB instances\.  |  Using different parameter groups can cause incompatibilities between the DB instances\. To avoid problems and for easier maintenance, we recommend using the same parameter group for all of the DB instances in the DB cluster\.  |  [Working with parameter groups](USER_WorkingWithParamGroups.md)  | 
|  DB cluster with different DB instance classes  |  Your DB cluster has DB instances that use different DB instance classes\.  |  Using different DB instance classes for DB instances can cause problems\. For example, performance might suffer if a less powerful DB instance class is promoted to replace a more powerful DB instance class\. To avoid problems and for easier maintenance, we recommend using the same DB instance class for all of the DB instances in the DB cluster\.  |  [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)  | 

Amazon Aurora generates recommendations for a resource when the resource is created or modified\. Amazon Aurora also periodically scans your resources and generates recommendations\.

### <a name="USER_Recommendations.Responding"></a>

**To view Amazon Aurora recommendations**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Recommendations**\.  
![\[Select Recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-select.png)

   The Recommendations page appears\.  
![\[Main Recommendations page in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-main-aurora.png)

1. On the **Recommendations** page, choose one of the following:
   + **Active** – Shows the current recommendations that you can apply, dismiss, or schedule\.
   + **Dismissed** – Shows the recommendations that have been dismissed\. When you choose **Dismissed**, you can apply these dismissed recommendations\.
   + **Scheduled** – Shows the recommendations that are scheduled but not yet applied\. These recommendations will be applied in the next scheduled maintenance window\.
   + **Applied** – Shows the recommendations that are currently applied\.

   From any list of recommendations, you can open a section to view the recommendations in that section\.  
![\[Take action on recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-active-aurora.png)

   To configure preferences for displaying recommendations in each section, choose the **Preferences** icon\.  
![\[Preferences icon for Recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-preferences-aurora.png)

   From the **Preferences** window that appears, you can set display options\. These options include the visible columns and the number of recommendations to display on the page\.

1. \(optional\) Respond to your active recommendations as follows:

   1. Choose **Active** and open one or more sections to view the recommendations in them\.

   1. Choose one or more recommendations and choose **Apply now** \(to apply them immediately\), **Schedule** \(to apply them in next maintenance window\), or **Dismiss**\.

      If the **Apply now** button appears for a recommendation but is unavailable \(grayed out\), the DB instance is not available\. You can apply recommendations immediately only if the DB instance status is **available**\. For example, you can't apply recommendations immediately to the DB instance if its status is **modifying**\. In this case, wait for the DB instance to be available and then apply the recommendation\.

      If the **Apply now** button doesn't appear for a recommendation, you can't apply the recommendation using the **Recommendations** page\. You can modify the DB instance to apply the recommendation manually\.

      For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.
**Note**  
When you choose **Apply now**, a brief DB instance outage might result\.