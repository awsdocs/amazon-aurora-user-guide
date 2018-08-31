# Upgrading and Patching Amazon Aurora MySQL<a name="AuroraMySQL.Updates.Patching"></a>

When a new minor version of the Amazon Aurora MySQL database engine is released, Amazon RDS schedules an automatic upgrade of the database engine for all Aurora DB clusters\. We announce automatic upgrades in the [Amazon RDS Community Forum](https://forums.aws.amazon.com/forum.jspa?forumID=60)\.

When a new patch version of the Aurora MySQL database engine is released, no automatic upgrade is required\. You can choose to upgrade and apply the patch, otherwise the patch will be applied during the next automatic upgrade for a minor version release\. 

Before automatic upgrade, new database engine releases show as an **available** maintenance upgrade for your DB cluster\. You can manually upgrade the database version for your DB cluster by applying the available maintenance action\. We encourage you to apply the update on a non\-production instance prior to the automatic upgrade, so that you can see how changes in the new version will affect your instances and applications\.

**To apply pending maintenance actions**
+ **By using the Amazon RDS Console** – Log on to the Amazon RDS console and choose **Clusters**\. Choose the DB cluster that shows an **available** maintenance upgrade\. Choose **Cluster Actions**\. Choose **Upgrade Now** to immediately update the database version for your DB cluster, or **Upgrade at Next Window** to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the AWS CLI** – Call the [apply\-pending\-maintenance\-action](http://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `--resource-id` option and `system-update` for the `--apply-action` option\. Set the `--opt-in-type` option to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the Amazon RDS API** – Call the [ApplyPendingMaintenanceAction](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) API action and specify the ARN for your DB cluster for the `ResourceId` parameter and `system-update` for the `ApplyAction` parameter\. Set the `OptInType` parameter to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your instance during the next cluster maintenance window\. 

For more information on how Amazon RDS manages database and operating system updates, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
If your current Aurora MySQL version is 1\.14\.x, but it is lower than 1\.14\.4, you can only upgrade to 1\.14\.4 \(which supports db\.r4 instance classes\)\. Also, to upgrade from 1\.14\.x to a higher major Aurora MySQL version, such as 1\.17, the 1\.14\.x version must be 1\.14\.4\.