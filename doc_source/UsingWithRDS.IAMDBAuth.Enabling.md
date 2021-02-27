# Enabling and disabling IAM database authentication<a name="UsingWithRDS.IAMDBAuth.Enabling"></a>

By default, IAM database authentication is disabled on DB clusters\. You can enable or disable IAM database authentication using the AWS Management Console, AWS CLI, or the API\.

You can enable IAM database authentication when you perform one of the following actions:
+ To create a new DB cluster with IAM database authentication enabled, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.
+ To modify a DB cluster to enable IAM database authentication, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.
+ To restore a DB cluster from a snapshot with IAM database authentication enabled, see [Restoring from a DB cluster snapshot](USER_RestoreFromSnapshot.md)\.
+ To restore a DB cluster to a point in time with IAM database authentication enabled, see [Restoring a DB cluster to a specified time](USER_PIT.md)\.

## Console<a name="UsingWithRDS.IAMDBAuth.Enabling.Console"></a>

Each creation or modification workflow has a **Database authentication** section, where you can enable or disable IAM database authentication\. In that section, choose **Password and IAM database authentication** to enable IAM database authentication\.

**To enable or disable IAM database authentication for an existing DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to modify\.
**Note**  
You can only enable IAM authentication if all DB instances in the DB cluster are compatible with IAM\. Check the compatibility requirements in [Availability for IAM database authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\. 

1. Choose **Modify**\.

1. In the **Database authentication** section, choose **Password and IAM database authentication** to enable IAM database authentication\.

1. Choose **Continue**\.

1. To apply the changes immediately, choose **Immediately** in the **Scheduling of modifications** section\.

1. Choose **Modify cluster**\.

## AWS CLI<a name="UsingWithRDS.IAMDBAuth.Enabling.CLI"></a>

To create a new DB cluster with IAM authentication by using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\. Specify the `--enable-iam-database-authentication` option\.

To update an existing DB cluster to have or not have IAM authentication, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. Specify either the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

**Note**  
You can only enable IAM authentication if all DB instances in the DB cluster are compatible with IAM\. Check the compatibility requirements in [Availability for IAM database authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\. 

By default, Aurora performs the modification during the next maintenance window\. If you want to override this and enable IAM DB authentication as soon as possible, use the `--apply-immediately` parameter\. 

If you are restoring a DB cluster, use one of the following AWS CLI commands:
+ `[restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)`
+ `[restore\-db\-cluster\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)`

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

## RDS API<a name="UsingWithRDS.IAMDBAuth.Enabling.API"></a>

To create a new DB instance with IAM authentication by using the API, use the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)\. Set the `EnableIAMDatabaseAuthentication` parameter to `true`\.

To update an existing DB cluster to have IAM authentication, use the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\. Set the `EnableIAMDatabaseAuthentication` parameter to `true` to enable IAM authentication, or `false` to disable it\.

**Note**  
You can only enable IAM authentication if all DB instances in the DB cluster are compatible with IAM\. Check the compatibility requirements in [Availability for IAM database authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\. 

If you are restoring a DB cluster, use one of the following API operations:
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `EnableIAMDatabaseAuthentication` parameter to `true` to enable IAM authentication, or `false` to disable it\.