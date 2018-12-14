# Amazon Aurora MySQL Database Engine Updates<a name="AuroraMySQL.Updates"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, as well as the type of update\. Updates are applied to all instances in a DB cluster simultaneously\. An update requires a database restart on all instances in a DB cluster, so you will experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

## Amazon Aurora Versions<a name="AuroraMySQL.Updates.Versions"></a>

Although Aurora with MySQL compatibility is compatible with the MySQL database engines, Aurora MySQL includes features that are specific to Aurora MySQL and only available to Aurora MySQL DB clusters\.

Aurora versions use the format *<major version>*\.*<minor version>*\. *<patch version>*\. You can get the version of your Aurora instance by querying for the `AURORA_VERSION` system variable\. To get the Amazon Aurora version, use one of the following queries\.

```
select AURORA_VERSION();
```

```
select @@aurora_version;
```

## Upgrading and Patching Amazon Aurora MySQL<a name="AuroraMySQL.Updates.Patching"></a>

To perform a major version upgrade, you can restore a snapshot of a DB cluster and specify a higher major engine version\. 

When a new minor version or patch version of the Aurora MySQL database engine is released, no automatic upgrade is scheduled\. New database engine releases show as an **available** maintenance upgrade for your DB cluster\. You can manually upgrade the database version for your DB cluster by applying the available maintenance action\. We encourage you to apply the update on a non\-production instance prior to a production upgrade, so that you can see how changes in the new version will affect your instances and applications\.

**To apply pending maintenance actions**
+ **By using the Amazon RDS Console** – Log on to the Amazon RDS console and choose **Clusters**\. Choose the DB cluster that shows an **available** maintenance upgrade\. Choose **Actions**\. Choose **Upgrade now** to immediately update the database version for your DB cluster, or **Upgrade at next window** to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the AWS CLI** – Call the [apply\-pending\-maintenance\-action](https://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `--resource-id` option and `system-update` for the `--apply-action` option\. Set the `--opt-in-type` option to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the Amazon RDS API** – Call the [ApplyPendingMaintenanceAction](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) API action and specify the ARN for your DB cluster for the `ResourceId` parameter and `system-update` for the `ApplyAction` parameter\. Set the `OptInType` parameter to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your instance during the next cluster maintenance window\. 

For more information on how Amazon RDS manages database and operating system updates, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
If your current Aurora MySQL version is 1\.14\.x, but it is lower than 1\.14\.4, you can only upgrade to 1\.14\.4 \(which supports db\.r4 instance classes\)\. Also, to upgrade from 1\.14\.x to a higher major Aurora MySQL version, such as 1\.17, the 1\.14\.x version must be 1\.14\.4\.

## Related Topics<a name="AuroraMySQL.Updates.Related"></a>
+ [Amazon Aurora MySQL 2\.0 Database Engine Updates ](AuroraMySQL.Updates.20Updates.md)
+ [Amazon Aurora MySQL 1\.1 Database Engine Updates ](AuroraMySQL.Updates.11Updates.md)
+ [MySQL Bugs Fixed by Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.MySQLBugs.md)
+ [Aurora Lab Mode Features](AuroraMySQL.Updates.LabMode.md#AuroraMySQL.Updates.LabModeFeatures)