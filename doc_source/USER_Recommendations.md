# Using Amazon Aurora Recommendations<a name="USER_Recommendations"></a>

Amazon Aurora provides automated recommendations for database resources, such as DB instances, DB clusters, and DB cluster parameter groups\. These recommendations provide best practice guidance by analyzing DB cluster configuration,  DB instance configuration, usage, and performance data\.

You can find examples of these recommendations in the following table\.


| Type | Description | Recommendation | Additional Information | 
| --- | --- | --- | --- | 
|  Nondefault custom memory parameters  |  Your DB parameter group sets memory parameters that diverge too much from the default values\.  |  Settings that diverge too much from the default values can cause poor performance and errors\. We recommend setting custom memory parameters to their default values in the DB parameter group used by the DB instance\.  |  [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  Change buffering enabled for a MySQL DB instance  |  Your DB parameter group has change buffering enabled\.  |  Change buffering allows a MySQL DB instance to defer some writes necessary to maintain secondary indexes\. This configuration can improve performance slightly, but it can create a large delay in crash recovery\. During crash recovery, the secondary index must be brought up to date\. So, the benefits of change buffering are outweighed by the potentially very long crash recovery events\. We recommend disabling change buffering\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](https://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  Logging to table  |  Your DB parameter group sets logging output to `TABLE`\.  |  Setting logging output to `TABLE` uses more storage than setting this parameter to `FILE`\. To avoid reaching the storage limit, we recommend setting the logging output parameter to `FILE`\.  |  [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)  | 
|  DB cluster with one DB instance  |  Your DB cluster only contains one DB instance\.  |  For improved performance and availability, we recommend adding another DB instance with the same DB instance class in a different Availability Zone\.  |  [High Availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  DB cluster in one Availability Zone  |  Your DB cluster has all of its DB instances in the same Availability Zone\.  |  For improved availability, we recommend adding another DB instance with the same DB instance class in a different Availability Zone\.  |  [High Availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  DB cluster outdated  |  Your DB cluster is running an older engine version\.  |  We recommend that you keep your DB cluster at the most current minor version because it includes the latest security and functionality fixes\. Unlike major version upgrades, minor version upgrades include only changes that are backward\-compatible with previous minor versions \(of the same major version\) of the DB engine\. We recommend that you upgrade to a recent engine version  |  [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)  | 
|  DB cluster with different parameter groups  |  Your DB cluster has different DB parameter groups assigned to its DB instances\.  |  Using different parameter groups can cause incompatibilities between the DB instances\. To avoid problems and for easier maintenance, we recommend using the same parameter group for all of the DB instances in the DB cluster\.  |  [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  DB cluster with different DB instance classes  |  Your DB cluster has DB instances that use different DB instance classes\.  |  Using different DB instance classes for DB instances can cause problems\. For example, performance might suffer if a less powerful DB instance class is promoted to replace a more powerful DB instance class\. To avoid problems and for easier maintenance, we recommend using the same DB instance class for all of the DB instances in the DB cluster\.  |  [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)  | 

Amazon Aurora generates recommendations for a resource when the resource is created or modified\. Amazon Aurora also periodically scans your resources and generates recommendations\.

## Responding to Amazon Aurora Recommendations<a name="USER_Recommendations.Responding"></a>

You can find recommendations in the AWS Management Console\. You can perform the recommended action immediately, schedule it for the next maintenance window, or dismiss it\.

**To respond to Amazon Aurora recommendations**

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

1. Manage your active recommendations:

   1. Choose **Active** and open one or more sections to view the recommendations in them\.

   1. Choose one or more recommendations and choose **Apply now** \(to apply them immediately\), **Schedule** \(to apply them in next maintenance window\), or **Dismiss**\.

      If the **Apply now** button appears for a recommendation but is unavailable \(grayed out\), the DB instance is not available\. You can apply recommendations immediately only if the DB instance status is **available**\. For example, you can't apply recommendations immediately to the DB instance if its status is **modifying**\. In this case, wait for the DB instance to be available and then apply the recommendation\.

      If the **Apply now** button doesn't appear for a recommendation, you can't apply the recommendation using the **Recommendations** page\. You can modify the DB instance to apply the recommendation manually\.

      For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.
**Note**  
When you choose **Apply now**, a brief DB instance outage might result\.