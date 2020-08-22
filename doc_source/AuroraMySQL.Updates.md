# Database Engine Updates for Amazon Aurora MySQL<a name="AuroraMySQL.Updates"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, as well as the type of update\. 

 Updates are applied to all instances in a DB cluster at the same time\. An update requires a database restart on all instances in a DB cluster, so you experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

## Aurora MySQL Versions<a name="AuroraMySQL.Updates.Versions"></a>

Although Aurora with MySQL compatibility is compatible with the MySQL database engines, Aurora MySQL includes features that are specific to Aurora MySQL and only available to Aurora MySQL DB clusters\.

Aurora versions use the format *<major version>*\.*<minor version>*\. *<patch version>*\. You can get the version of your Aurora instance by querying for the `AURORA_VERSION` system variable\. To get the Aurora version, use one of the following queries\.

```
select AURORA_VERSION();
select @@aurora_version;
```

## Aurora MySQL Engine Versions<a name="AuroraMySQL.Updates.EngineVersions"></a>

Starting with Aurora MySQL 2\.03\.2 and 1\.19\.0, Aurora engine versions have the following syntax\.

```
<mysql-major-version>.mysql_aurora.<aurora-mysql-version>
```

For example, the engine versions for Aurora MySQL 2\.03\.2 and 1\.19\.0 are the following\.

```
5.7.mysql_aurora.2.03.2
5.6.mysql_aurora.1.19.0
```

**Note**  
All 1\.x Aurora MySQL engine versions are wire\-compatible with Community MySQL 5\.6\.10a\. All 2\.x Aurora MySQL engine versions are wire\-compatible with Community MySQL 5\.7\.12\.  
For Aurora MySQL 2\.x, the engine version for Aurora MySQL version 2\.03\.1 and lower is `5.7.12`\. For Aurora MySQL 1\.x, the engine version for Aurora MySQL prior to 1\.19\.0 is `5.6.10a`\.

 You specify the Aurora MySQL engine version in some AWS CLI commands and RDS API operations\. For example, you specify the `--engine-version` option when you run the AWS CLI commands [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. You specify the `EngineVersion` parameter when you run the RDS API operations [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\. 

 The process to update the engine version prior to Aurora MySQL 1\.19\.0 and 2\.03\.2 is to Apply a Pending Maintenance Action in the Cluster\. This process does not change the Aurora MySQL engine version displayed by the AWS Management Console\. 

 Prior to Aurora MySQL 1\.19\.0 and 2\.03\.2, the engine version displayed by the AWS Management Console remained the same even after you upgraded the engine in the cluster\. In Aurora MySQL 1\.19\.0 and higher or 2\.03\.2 and higher, the engine version in the AWS Management Console also includes the Aurora version\. Upgrading the cluster changes the displayed value\. For Aurora clusters managed through AWS CloudFormation, this change in the `EngineVersion` setting can trigger actions by AWS CloudFormation\. For information about how AWS CloudFormation treats changes to the `EngineVersion` setting, see [the AWS CloudFormation documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbcluster.html)\. 

## Database Upgrades and Patches for Amazon Aurora MySQL<a name="AuroraMySQL.Updates.Patching"></a>

There are two ways to upgrade the minor version of a DB cluster or patch a DB cluster:
+ [Modifying the Engine Version](#AuroraMySQL.Updates.Patching.ModifyEngineVersion) \(when upgrading to version 1\.19\.0 and higher, or 2\.03\.2 and higher\)
+ [Applying Pending Maintenance to an Aurora MySQL DB Cluster](#AuroraMySQL.Updates.PendingMaintenance)

### Modifying the Engine Version<a name="AuroraMySQL.Updates.Patching.ModifyEngineVersion"></a>

When upgrading to Amazon Aurora MySQL version 1\.19\.0 and higher, or 2\.03\.2 and higher, you upgrade the minor version of a DB cluster\. You do so by modifying the engine version of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\.

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

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.ZDP"></a>

 The zero\-downtime patching \(ZDP\) feature attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. If ZDP completes successfully, application sessions are preserved and the database engine restarts while patching\. The database engine restart can cause a drop in throughput lasting approximately 5 seconds\. ZDP is available in Aurora MySQL 1\.13 and later, compatible with MySQL 5\.6\. It's also available in Aurora MySQL 2\.07 and later, compatible with MySQL 5\.7\. 

 ZDP might not complete successfully under the following conditions: 
+  Long\-running queries or transactions are in progress\. If Aurora can perform ZDP in this case, any open transactions are cancelled\. 
+  Binary logging is enabled or binary log replication is in\-progress\. 
+  Open SSL connections exist\. 
+  Temporary tables or table locks are in use, for example during DDL statements\. If Aurora can perform ZDP in this case, any open transactions are cancelled\. 
+  Pending parameter changes exist\. 

 Starting in Aurora MySQL 1\.19 and 2\.07, the ZDP mechanism is improved\. These improvements make ZDP more likely to succeed when there are long\-running transactions, binary logging, table locks, or temporary tables\. 

 If no suitable time window for performing ZDP becomes available because of one or more of these conditions, patching reverts to the standard behavior\. 

**Note**  
 ZDP applies only to the primary instance of a DB cluster\. ZDP isn't applicable to Aurora Replicas\. 
 Prepared statements don't prevent ZDP, but they aren't preserved after ZDP completes\. 

## Aurora MySQL Long\-Term Support \(LTS\) Releases<a name="AuroraMySQL.Updates.LTS"></a>

 Each new Aurora MySQL version remains available for a certain amount of time for you to use when you create or upgrade a DB cluster\. After this period, you must upgrade any clusters that use that version\. You can manually upgrade your cluster before the support period ends, or Aurora can automatically upgrade it for you when its Aurora MySQL version is no longer supported\. 

 Aurora designates certain Aurora MySQL versions as "long\-term support" \(LTS\) releases\. DB clusters that use LTS releases can stay on the same version longer and undergo fewer upgrade cycles than clusters that use non\-LTS releases\. Aurora supports each LTS release for at least one year after that release becomes available\. When a DB cluster that's on an LTS release is required to upgrade, Aurora upgrades it to the next LTS release\. That way, the cluster doesn't need to be upgraded again for a long time\. 

 During the lifetime of an Aurora MySQL LTS release, new patch levels introduce fixes to important issues\. The patch levels don't include any new features\. You can choose whether or not to apply such patches to DB clusters running the LTS release\. For certain critical fixes, Amazon might perform a managed upgrade to a patch level within the same LTS release\. Such managed upgrades are performed automatically within the cluster maintenance window\. 

 We recommend that you upgrade to the latest release, instead of using the LTS release, for most of your Aurora MySQL clusters\. Doing so takes advantage of Aurora as a managed service and gives you access to the latest features and bug fixes\. The LTS releases are intended for clusters that have the following requirements and business needs: 
+  You can't afford downtime on your Aurora MySQL application for upgrades outside of rare occurrences for critical patches\. 
+  The testing cycle for the cluster and associated applications takes a long time for each update to the Aurora MySQL database engine\. 
+  The database version for your Aurora MySQL cluster has all the DB engine features and bug fixes that your application needs\. 

 As of the publication date for this *Aurora User's Guide*, the current LTS releases for Aurora MySQL are: 
+  Aurora MySQL version 1\.19\.6\. For more details about this version, see [Aurora MySQL Database Engine Updates 2020\-03\-05 \(Version 1\.19\.6\)](AuroraMySQL.Updates.1196.md)\. 
+  Aurora MySQL version 2\.04\.9\. For more details about this version, see [Aurora MySQL Database Engine Updates 2020\-08\-14 \(Version 2\.04\.9\)](AuroraMySQL.Updates.2049.md)\. 

## Related Topics<a name="AuroraMySQL.Updates.Related"></a>
+ [Database Engine Updates for Amazon Aurora MySQL 2\.0](AuroraMySQL.Updates.20Updates.md)
+ [Database Engine Updates for Amazon Aurora MySQL 1\.1](AuroraMySQL.Updates.11Updates.md)
+ [Database Engine Updates for Aurora MySQL Serverless Clusters](AuroraMySQL.Updates.ServerlessUpdates.md)
+ [MySQL Bugs Fixed by Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.MySQLBugs.md)
+ [Aurora Lab Mode Features](AuroraMySQL.Updates.LabMode.md#AuroraMySQL.Updates.LabModeFeatures)
+ [Security Vulnerabilities Fixed in Amazon Aurora MySQL](AuroraMySQL.CVE_list.md)