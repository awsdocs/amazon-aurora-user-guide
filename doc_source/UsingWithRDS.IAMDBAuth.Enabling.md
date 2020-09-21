# Enabling and Disabling IAM Database Authentication<a name="UsingWithRDS.IAMDBAuth.Enabling"></a>

By default, IAM database authentication is disabled on DB clusters\. You can enable IAM database authentication \(or disable it again\) using the AWS Management Console, AWS CLI, or the API\.

## Console<a name="UsingWithRDS.IAMDBAuth.Enabling.Console"></a>

To create a new DB cluster with IAM authentication by using the console, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

Each creation workflow has a **Configure Advanced Settings** page, where you can enable IAM database authentication\. In that page's **Database Options** section, choose **Yes** for **Enable IAM DB Authentication**\.

**To enable or disable IAM authentication for an existing DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to modify\.
**Note**  
 Make sure that all affected DB instances are compatible with IAM authentication\. Check the compatibility requirements in [Availability for IAM Database Authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\. For an Aurora DB cluster, you can only enable IAM authentication if all DB instances in the cluster are compatible with IAM\. 

1. Choose **Modify**\.

1. In the **Database options** section, for **IAM DB authentication** choose **Enable IAM DB authentication** or **Disable**, and then choose **Continue**\.

1. To apply the changes immediately, choose **Apply immediately**\.

1. Choose **Modify cluster**\.

**To restore a DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot that you want to restore, and then choose **Restore Snapshot** for **Actions**\.

1. In the **Settings** section, enter an identifier for the DB instance for **DB Instance Identifier**\.

1. In the **Database options** section, for **IAM DB authentication**, choose **Enable IAM DB authentication** or **Disable**\.

1. Choose **Restore DB Instance**\.

## AWS CLI<a name="UsingWithRDS.IAMDBAuth.Enabling.CLI"></a>

To create a new DB cluster with IAM authentication by using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\. Specify the `--enable-iam-database-authentication` option\.

To update an existing DB cluster to have or not have IAM authentication, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. Specify either the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

**Note**  
 Make sure that all affected DB instances are compatible with IAM authentication\. Check the compatibility requirements in [Availability for IAM Database Authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\. For an Aurora DB cluster, you can only enable IAM authentication if all DB instances in the cluster are compatible with IAM\. 

By default, Aurora performs the modification during the next maintenance window\. If you want to override this and enable IAM DB authentication as soon as possible, use the `--apply-immediately` parameter\. 

If you are restoring a DB cluster, use one of the following AWS CLI commands:
+ `[restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)`
+ `[restore\-db\-cluster\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)`

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

## RDS API<a name="UsingWithRDS.IAMDBAuth.Enabling.API"></a>

To create a new DB instance with IAM authentication by using the API, use the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)\. Set the `EnableIAMDatabaseAuthentication` parameter to `true`\.

To update an existing DB cluster to have IAM authentication, use the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\. Set the `EnableIAMDatabaseAuthentication` parameter to `true` to enable IAM authentication, or `false` to disable it\.

**Note**  
 Make sure that all affected DB instances are compatible with IAM authentication\. Check the compatibility requirements in [Availability for IAM Database Authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\. For an Aurora DB cluster, you can only enable IAM authentication if all DB instances in the cluster are compatible with IAM\. 

If you are restoring a DB cluster, use one of the following API operations:
+ [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)
+ [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html)

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `EnableIAMDatabaseAuthentication` parameter to `true` to enable IAM authentication, or `false` to disable it\.