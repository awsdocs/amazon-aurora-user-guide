# Working with Amazon Aurora MySQL<a name="Aurora.AuroraMySQL"></a>

Amazon Aurora MySQL is a fully managed, MySQL\-compatible, relational database engine that combines the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. Aurora MySQL is a drop\-in replacement for MySQL and makes it simple and cost\-effective to set up, operate, and scale your new and existing MySQL deployments, thus freeing you to focus on your business and applications\. Amazon RDS provides administration for Aurora by handling routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. Amazon RDS also provides push\-button migration tools to convert your existing Amazon RDS for MySQL applications to Aurora MySQL\.

**Topics**
+ [Overview of Amazon Aurora MySQL](Aurora.AuroraMySQL.Overview.md)
+ [Security with Amazon Aurora MySQL](AuroraMySQL.Security.md)
+ [Migrating Data to an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Migrating.md)
+ [Managing Amazon Aurora MySQL](AuroraMySQL.Managing.md)
+ [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)
+ [Using Advanced Auditing with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Auditing.md)
+ [Replication with Amazon Aurora MySQL](AuroraMySQL.Replication.md)
+ [Integrating Amazon Aurora MySQL with Other AWS Services](AuroraMySQL.Integrating.md)
+ [Upgrading and Patching Amazon Aurora MySQL](#AuroraMySQL.Updates.Patching)
+ [Amazon Aurora MySQL Lab Mode](AuroraMySQL.Updates.LabMode.md)
+ [Best Practices with Amazon Aurora MySQL](AuroraMySQL.BestPractices.md)
+ [Amazon Aurora MySQL Reference](AuroraMySQL.Reference.md)
+ [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)

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