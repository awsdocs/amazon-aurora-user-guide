# Database Engine Updates for Amazon Aurora MySQL<a name="AuroraMySQL.Updates"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, as well as the type of update\. 

 Updates are applied to all instances in a DB cluster at the same time\. An update requires a database restart on all instances in a DB cluster, so you experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

## Aurora MySQL Versions<a name="AuroraMySQL.Updates.Versions"></a>

Although Aurora with MySQL compatibility is compatible with the MySQL database engines, Aurora MySQL includes features that are specific to Aurora MySQL and only available to Aurora MySQL DB clusters\.

Aurora versions use the format *<major version>*\.*<minor version>*\. *<patch version>*\. You can get the version of your Aurora instance by querying for the `AURORA_VERSION` system variable\. To get the Aurora version, use one of the following queries\.

```
select AURORA_VERSION();
```

```
select @@aurora_version;
```

## Aurora MySQL Engine Versions<a name="AuroraMySQL.Updates.EngineVersions"></a>

Starting with Aurora MySQL 2\.03\.2, Aurora engine versions have the following syntax\.

```
<mysql-major-version>.mysql_aurora.<aurora-mysql-version>            
```

For example, the engine version for Aurora MySQL 2\.03\.2 is the following\.

```
5.7.mysql_aurora.2.03.2            
```

**Note**  
For Aurora MySQL 2\.x, the engine version for Aurora MySQL version 2\.03\.1 and lower is `5.7.12`\. Currently, the engine version for all Aurora MySQL 1\.x versions is `5.6.10a`\.

 You specify the Aurora MySQL engine version in some AWS CLI commands and RDS API operations\. For example, you specify the `--engine-version` option when you run the AWS CLI commands [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. You specify the `EngineVersion` parameter when you run the RDS API operations [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\. 

 Prior to Aurora MySQL 2\.03\.2, the engine version displayed by the AWS Management Console remained the same even after you upgraded the engine in the cluster\. In Aurora MySQL 2\.03\.2 and higher, the engine version in the AWS Management Console also includes the Aurora version\. Upgrading the cluster changes the displayed value\. For Aurora clusters managed through AWS CloudFormation, this change in the `EngineVersion` setting can trigger actions by AWS CloudFormation\. For information about how AWS CloudFormation treats changes to the `EngineVersion` setting, see [the AWS CloudFormation documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbcluster.html)\. 

## Database Upgrades and Patches for Amazon Aurora MySQL<a name="AuroraMySQL.Updates.Patching"></a>

There are two ways to upgrade the minor version of a DB cluster or patch a DB cluster:
+ [Modifying the Engine Version](#AuroraMySQL.Updates.Patching.ModifyEngineVersion) \(upgrading to version 2\.03\.2 and higher only\)
+ [Applying Pending Maintenance to an Aurora MySQL DB Cluster](#AuroraMySQL.Updates.PendingMaintenance)

### Modifying the Engine Version<a name="AuroraMySQL.Updates.Patching.ModifyEngineVersion"></a>

When upgrading to Amazon Aurora MySQL version 2\.03\.2 and higher, you upgrade the minor version of a DB cluster\. You do so by modifying the engine version of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\.

**To modify the engine version of a DB cluster**
+ **By using the Amazon RDS console** – Complete the following steps: 

  1. Sign in to the Amazon RDS console and choose **Databases**\.

  1. Choose the DB cluster that you want to modify\.

  1. Choose **Modify**\.

  1. Change the Aurora MySQL engine version in the **DB engine version** box\.

  1. Choose **Continue** and check the summary of modifications\.

  1. \(Optional\) Choose **Apply immediately** to apply the changes immediately\.

  1. On the confirmation page, choose **Modify cluster**\.
+ **By using the AWS CLI** – Call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command and specify the name of your DB cluster for the `--db-cluster-identifier` option and the engine version for the `--engine-version` option\. 

   For example, to upgrade to Aurora MySQL version 2\.03\.2, set the `--engine-version` option to `5.7.mysql_aurora.2.03.2`\. Specify the `--apply-immediately` option to immediately update the engine version for your DB cluster\. 
+ **By using the Amazon RDS API** – Call the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation and specify the name of your DB cluster for the `DBClusterIdentifier` parameter and the engine version for the `EngineVersion` parameter\. Set the `ApplyImmediately` parameter to `true` to immediately update the engine version for your DB cluster\.

### Applying Pending Maintenance to an Aurora MySQL DB Cluster<a name="AuroraMySQL.Updates.PendingMaintenance"></a>

When upgrading to Aurora MySQL version 1\.x versions, new database engine minor versions and patches show as an **available** maintenance upgrade for your DB cluster\. You can upgrade or patch the database version of your DB cluster by applying the available maintenance action\. We recommend applying the update on a nonproduction DB cluster first, so that you can see how changes in the new version affect your instances and applications\.

**To apply pending maintenance actions**
+ **By using the Amazon RDS console** – Complete the following steps:

  1. Sign in to the Amazon RDS console and choose **Databases**\.

  1. Choose the DB cluster that shows an **available** maintenance upgrade\.

  1. For **Actions**, choose **Upgrade now** to immediately update the database version for your DB cluster, or **Upgrade at next window** to update the database version for your DB cluster during the next DB cluster maintenance window\.
+ **By using the AWS CLI** – Call the [apply\-pending\-maintenance\-action](https://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `--resource-id` option and `system-update` for the `--apply-action` option\. Set the `--opt-in-type` option to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the Amazon RDS API** – Call the [ApplyPendingMaintenanceAction](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) API operation and specify the ARN for your DB cluster for the `ResourceId` parameter and `system-update` for the `ApplyAction` parameter\. Set the `OptInType` parameter to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your instance during the next cluster maintenance window\.

For more information on how Amazon RDS manages database and operating system updates, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
If your current Aurora MySQL version is 1\.14\.x, but it is lower than 1\.14\.4, you can upgrade only to 1\.14\.4 \(which supports db\.r4 instance classes\)\. Also, to upgrade from 1\.14\.x to a higher minor Aurora MySQL version, such as 1\.17, the 1\.14\.x version must be 1\.14\.4\.

## Related Topics<a name="AuroraMySQL.Updates.Related"></a>
+ [Database Engine Updates for Amazon Aurora MySQL 2\.0](AuroraMySQL.Updates.20Updates.md)
+ [Database Engine Updates for Amazon Aurora MySQL 1\.1](AuroraMySQL.Updates.11Updates.md)
+ [MySQL Bugs Fixed by Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.MySQLBugs.md)
+ [Aurora Lab Mode Features](AuroraMySQL.Updates.LabMode.md#AuroraMySQL.Updates.LabModeFeatures)