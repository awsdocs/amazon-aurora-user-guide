# Maintaining an Amazon Aurora DB Cluster<a name="USER_UpgradeDBInstance.Maintenance"></a>

Periodically, Amazon RDS performs maintenance on Amazon RDS resources\. Maintenance most often involves updates to the DB cluster's underlying hardware, underlying operating system \(OS\), or database engine version\. Updates to the operating system most often occur for security issues and should be done as soon as possible\. 

Some maintenance items require that Amazon RDS take your DB cluster offline for a short time\. Maintenance items that require a resource to be offline include required operating system or database patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once every few months\) and seldom requires more than a fraction of your maintenance window\. 

You can view whether a maintenance update is available for your DB cluster by using the RDS console, the AWS CLI, or the Amazon RDS API\. If an update is available, it is indicated in the **Maintenance** column for the DB cluster on the Amazon RDS console, as shown following\. 

![\[Offline patch available\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/offlinepatchavailable.png)

If no maintenance update is available for a DB cluster, the column value is **none** for it\.

If a maintenance update is available for a DB cluster, the following column values are possible:
+ **required** – The maintenance action will be applied to the resource and can't be deferred indefinitely\.
+ **available** – The maintenance action is available, but it will not be applied to the resource automatically\. You can apply it manually\.
+ **next window** – The maintenance action will be applied to the resource during the next maintenance window\.
+ **In progress** – The maintenance action is in the process of being applied to the resource\.

If an update is available, you can take one of the actions: 
+ If the maintenance value is **next window**, defer the maintenance items by choosing **Defer upgrade** from **Actions**\. You can't defer a maintenance action if it has already started\.
+ Apply the maintenance items immediately\.
+ Schedule the maintenance items to start during your next maintenance window\.
+ Take no action\.

**Note**  
Certain OS updates are marked as **required**\. If you defer a required update, you get a notice from Amazon RDS indicating when the update will be performed\. Other updates are marked as **available**, and these you can defer indefinitely\.

To take an action, choose the DB cluster to show its details, then choose **Maintenance & backups**\. The pending maintenance items appear\.

![\[Pending maintenance items\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/offlinepatchavailabledetails.png)

The maintenance window determines when pending operations start, but doesn't limit the total run time of these operations\. Maintenance operations aren't guaranteed to finish before the maintenance window ends, and can continue beyond the specified end time\. For more information, see [The Amazon RDS Maintenance Window](#Concepts.DBMaintenance)\. 

For information about updates to Amazon Aurora engines and instructions for upgrading and patching them, see [Database Engine Updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md) and [Database Engine Updates for Amazon Aurora PostgreSQL ](AuroraPostgreSQL.Updates.md)\.

## Applying Updates for a DB Cluster<a name="USER_UpgradeDBInstance.OSUpgrades"></a>

With Amazon RDS, you can choose when to apply maintenance operations\. You can decide when Amazon RDS applies updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\. 

### Console<a name="USER_UpgradeDBInstance.OSUpgrades.Console"></a>

**To manage an update for a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that has a required update\. 

1. For **Actions**, choose one of the following:
   + **Upgrade now**
   + **Upgrade at next window**
**Note**  
If you choose **Upgrade at next window** and later want to delay the update, you can choose **Defer upgrade**\. You can't defer a maintenance action if it has already started\.  
To cancel a maintenance action, modify the DB instance and disable **Auto minor version upgrade**\.

### AWS CLI<a name="USER_UpgradeDBInstance.OSUpgrades.CLI"></a>

To apply a pending update to a DB cluster, use the [apply\-pending\-maintenance\-action](https://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds apply-pending-maintenance-action \
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db \
    --apply-action system-update \
    --opt-in-type immediate
```
For Windows:  

```
aws rds apply-pending-maintenance-action ^
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db ^
    --apply-action system-update ^
    --opt-in-type immediate
```

**Note**  
To defer a maintenance action, specify `undo-opt-in` for `--opt-in-type`\. You can't specify `undo-opt-in` for `--opt-in-type` if the maintenance action has already started\.  
To cancel a maintenance action, run the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command and specify `--no-auto-minor-version-upgrade`\.

To return a list of resources that have at least one pending update, use the [describe\-pending\-maintenance\-actions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-pending-maintenance-actions \
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```
For Windows:  

```
aws rds describe-pending-maintenance-actions ^
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```

You can also return a list of resources for a DB cluster by specifying the `--filters` parameter of the `describe-pending-maintenance-actions` AWS CLI command\. The format for the `--filters` command is `Name=filter-name,Value=resource-id,...`\.

The following are the accepted values for the `Name` parameter of a filter:
+ `db-instance-id` – Accepts a list of DB instance identifiers or Amazon Resource Names \(ARNs\)\. The returned list only includes pending maintenance actions for the DB instances identified by these identifiers or ARNs\.
+ `db-cluster-id` – Accepts a list of DB cluster identifiers or ARNs for Amazon Aurora\. The returned list only includes pending maintenance actions for the DB clusters identified by these identifiers or ARNs\.

For example, the following example returns the pending maintenance actions for the `sample-cluster1` and `sample-cluster2` DB clusters\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-pending-maintenance-actions \
	--filters Name=db-cluster-id,Values=sample-cluster1,sample-cluster2
```
For Windows:  

```
aws rds describe-pending-maintenance-actions ^
	--filters Name=db-cluster-id,Values=sample-cluster1,sample-cluster2
```

### RDS API<a name="USER_UpgradeDBInstance.OSUpgrades.API"></a>

To apply an update to a DB cluster, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) operation\.

To return a list of resources that have at least one pending update, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html) operation\.

## The Amazon RDS Maintenance Window<a name="Concepts.DBMaintenance"></a>

Every DB cluster has a weekly maintenance window during which any system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occur, in the event either are requested or required\. If a maintenance event is scheduled for a given week, it is initiated during the 30\-minute maintenance window you identify\. Most maintenance events also complete during the 30\-minute maintenance window, although larger maintenance events may take more than 30 minutes to complete\. 

The 30\-minute maintenance window is selected at random from an 8\-hour block of time per region\. If you don't specify a preferred maintenance window when you create the DB cluster, then Amazon RDS assigns a 30\-minute maintenance window on a randomly selected day of the week\. 

RDS will consume some of the resources on your DB cluster while maintenance is being applied\. You might observe a minimal effect on performance\. For a DB instance, on rare occasions, a Multi\-AZ failover might be required for a maintenance update to complete\. 

Following, you can find the time blocks for each region from which default maintenance windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.Maintenance.html)

## Adjusting the Preferred DB Cluster Maintenance Window<a name="AdjustingTheMaintenanceWindow.Aurora"></a>

The Aurora DB cluster maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. Your DB cluster is unavailable during this time only if the updates that are being applied require an outage\. The outage is for the minimum amount of time required to make the necessary updates\. 

### Console<a name="AdjustingTheMaintenanceWindow.Aurora.CON"></a>

**To adjust the preferred DB cluster maintenance window**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster for which you want to change the maintenance window\.

1. For **Actions**, choose **Modify cluster**\.

1. In the **Maintenance** section, update the maintenance window\.

1. Choose **Continue**\.

   On the confirmation page, review your changes\.

1. To apply the changes to the maintenance window immediately, choose **Apply immediately**\. 

1.  Choose **Modify cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="AdjustingTheMaintenanceWindow.Aurora.CLI"></a>

To adjust the preferred DB cluster maintenance window, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command with the following parameters:
+ `--db-cluster-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00–4:30 AM UTC\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
--db-cluster-identifier my-cluster \
--preferred-maintenance-window Tue:04:00-Tue:04:30
```
For Windows:  

```
aws rds modify-db-cluster ^
--db-cluster-identifier my-cluster ^
--preferred-maintenance-window Tue:04:00-Tue:04:30
```

### RDS API<a name="AdjustingTheMaintenanceWindow.Aurora.API"></a>

To adjust the preferred DB cluster maintenance window, use the Amazon RDS [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation with the following parameters:
+ `DBClusterIdentifier = my-cluster`
+ `PreferredMaintenanceWindow = Tue:04:00-Tue:04:30`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00–4:30 AM UTC\.  

```
 1. https://rds.us-west-2.amazonaws.com/
 2. ?Action=ModifyDBCluster
 3. &DBClusterIdentifier=my-cluster
 4. &PreferredMaintenanceWindow=Tue:04:00-Tue:04:30
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-10-31
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140725/us-east-1/rds/aws4_request
10. &X-Amz-Date=20161017T161457Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=d6d1c65c2e94f5800ab411a3f7336625820b103713b6c063430900514e21d784
```

## Choosing the Frequency of Aurora MySQL Maintenance Updates<a name="Aurora.Maintenance.LTS"></a>

 You can control whether Aurora MySQL upgrades happen frequently or rarely for each DB cluster\. The best choice depends on your usage of Aurora MySQL and the priorities for your applications that run on Aurora, For information about the Aurora MySQL long\-term stability \(LTS\) releases that require less frequent upgrades, see [Aurora MySQL Long\-Term Support \(LTS\) Releases](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LTS)\. 

 You might choose to upgrade an Aurora MySQL cluster rarely if some or all of the following conditions apply: 
+  Your testing cycle for your application takes a long time for each update to the Aurora MySQL database engine\. 
+  You have many DB clusters or many applications all running on the same Aurora MySQL version\. You prefer to upgrade all of your DB clusters and associated applications at the same time\. 
+  You use both Aurora MySQL Amazon RDS MySQL, and you prefer to keep the Aurora MySQL clusters and RDS MySQL DB instances compatible with the same level of MySQL\. 
+  Your Aurora MySQL application is in production or is otherwise business\-critical\. You can't afford downtime for upgrades outside of rare occurrences for critical patches\. 
+  Your Aurora MySQL application isn't limited by performance issues or feature gaps that are addressed in subsequent Aurora MySQL versions\. 

 If the preceding factors apply to your situation, you can limit the number of forced upgrades for an Aurora MySQL DB cluster\. You do so by choosing a specific Aurora MySQL version known as the "Long\-Term Support" \(LTS\) version when you create or upgrade that DB cluster\. Doing so minimizes the number of upgrade cycles, testing cycles, and upgrade\-related outages for that DB cluster\. 

 You might choose to upgrade an Aurora MySQL cluster frequently if some or all of the following conditions apply: 
+  The testing cycle for your application is straightforward and brief\. 
+  Your application is still in the development stage\. 
+  Your database environment uses a variety of Aurora MySQL versions, or Aurora MySQL and Amazon RDS MySQL versions\. Each Aurora MySQL cluster has its own upgrade cycle\. 
+  You are waiting for specific performance or feature improvements before you increase your usage of Aurora MySQL\. 

 If the preceding factors apply to your situation, you can enable Aurora to apply important upgrades more frequently by upgrading an Aurora MySQL DB cluster to a more recent Aurora MySQL version than the LTS version\. Doing so makes the latest performance enhancements, bug fixes, and features available to you more quickly\. 