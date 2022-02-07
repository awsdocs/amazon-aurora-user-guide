# Restoring a DB cluster to a specified time<a name="aurora-pitr"></a>

You can restore a DB cluster to a specific point in time, creating a new DB cluster\.

When you restore a DB cluster to a point in time, you can choose the default virtual private cloud \(VPC\) security group\. Or you can apply a custom VPC security group to your DB cluster\.

Restored DB clusters are automatically associated with the default DB cluster and DB parameter groups\. However, you can apply custom parameter groups by specifying them during a restore\.

Amazon RDS uploads transaction logs for DB clusters to Amazon S3 continuously\. To see the latest restorable time for a DB cluster, use the AWS CLI [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command and look at the value returned in the `LatestRestorableTime` field for the DB cluster\.

You can restore to any point in time within your backup retention period\. To see the earliest restorable time for a DB cluster, use the AWS CLI [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command and look at the value returned in the `EarliestRestorableTime` field for the DB cluster\.

**Note**  
Information in this topic applies to Amazon Aurora\. For information on restoring an Amazon RDS DB instance, see [Restoring a DB instance to a specified time](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html)\.  
For more information about backing up and restoring an Aurora DB cluster, see [Overview of backing up and restoring an Aurora DB cluster](Aurora.Managing.Backups.md)\.  
For Aurora MySQL, you can restore a provisioned DB cluster to an Aurora Serverless DB cluster\. For more information, see [Restoring an Aurora Serverless v1 DB cluster](aurora-serverless.restorefromsnapshot.md)\.

You can restore a DB cluster to a point in time using the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="aurora-pitr.CON"></a>

**To restore a DB cluster to a specified time**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to restore\.

1. For **Actions**, choose **Restore to point in time**\.

   The **Restore to point in time** window appears\.

1. Choose **Latest restorable time** to restore to the latest possible time, or choose **Custom** to choose a time\.

   If you chose **Custom**, enter the date and time to which you want to restore the cluster\.
**Note**  
Times are shown in your local time zone, which is indicated by an offset from Coordinated Universal Time \(UTC\)\. For example, UTC\-5 is Eastern Standard Time/Central Daylight Time\.

1. For **DB instance identifier**, enter the name of the target restored DB cluster\. The name must be unique\.

1. Choose other options as needed, such as DB instance class and storage\.

1. Choose **Restore to point in time**\.

## AWS CLI<a name="aurora-pitr.CLI"></a>

To restore a DB cluster to a specified time, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) to create a new DB cluster\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-cluster-to-point-in-time \
2.     --source-db-cluster-identifier mysourcedbcluster \
3.     --db-cluster-identifier mytargetdbcluster \
4.     --restore-to-time 2017-10-14T23:45:00.000Z
```
For Windows:  

```
1. aws rds restore-db-cluster-to-point-in-time ^
2.     --source-db-cluster-identifier mysourcedbcluster ^
3.     --db-cluster-identifier mytargetdbcluster ^
4.     --restore-to-time 2017-10-14T23:45:00.000Z
```

**Important**  
If you use the console to restore a DB cluster to a specified time, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the AWS CLI to restore a DB cluster to a specified time, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.   
To create the primary instance for your DB cluster, call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command\. Include the name of the DB cluster as the `--db-cluster-identifier` option value\.

## RDS API<a name="aurora-pitr.API"></a>

To restore a DB cluster to a specified time, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) operation with the following parameters:
+ `SourceDBClusterIdentifier`
+ `DBClusterIdentifier`
+ `RestoreToTime`

**Important**  
If you use the console to restore a DB cluster to a specified time, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the RDS API to restore a DB cluster to a specified time, make sure to explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.   
To create the primary instance for your DB cluster, call the RDS API operation [ CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\. Include the name of the DB cluster as the `DBClusterIdentifier` parameter value\.