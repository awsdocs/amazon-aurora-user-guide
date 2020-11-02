# Upgrading and patching Amazon Aurora MySQL DB clusters<a name="AuroraMySQL.Updates.Patching"></a>

 You can use the following methods to upgrade the minor version of a DB cluster or to patch a DB cluster: 
+  [Upgrading Aurora MySQL by modifying the engine version](#AuroraMySQL.Updates.Patching.ModifyEngineVersion) \(for Aurora MySQL 1\.19\.0 and higher, or 2\.03\.2 and higher\) 
+  [Enabling automatic upgrades between minor Aurora MySQL versions](#AuroraMySQL.Updates.AMVU) \(for Aurora MySQL 2\.x\) 
+  [Upgrading Aurora MySQL by applying pending maintenance to an Aurora MySQL DB cluster](#AuroraMySQL.Updates.PendingMaintenance) \(before Aurora MySQL 1\.19\.0 or 2\.03\.2\) 

 For information about how zero\-downtime patching can reduce interruptions during the upgrade process, see [Using zero\-downtime patching](#AuroraMySQL.Updates.ZDP)\. 

## Upgrading Aurora MySQL by modifying the engine version<a name="AuroraMySQL.Updates.Patching.ModifyEngineVersion"></a>

 Upgrading the minor version of an Aurora MySQL cluster applies additional fixes and new features to an existing cluster\. You can do this type of upgrade for clusters that are running Amazon Aurora MySQL version 1\.19\.0 and higher, or 2\.03\.2 and higher\. 

 This kind of upgrade applies to Aurora MySQL clusters where the original version and the upgraded version are both in the Aurora MySQL 1\.x series, or both in the Aurora MySQL 2\.x series\. The process is fast and straightforward because it doesn't involve any conversion for the Aurora MySQL metadata or reorganization of your table data\. 

 You perform this kind of upgrade by modifying the engine version of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\. If your cluster is running Aurora MySQL 1\.x, choose a higher 1\.x version\. If your cluster is running Aurora MySQL 2\.x, choose a higher 2\.x version\. 

 **To modify the engine version of a DB cluster** 
+  **By using the console** – Modify the properties of your cluster\. In the **Modify DB cluster** window, change the Aurora MySQL engine version in the **DB engine version** box\. If you aren't familiar with the general procedure for modifying a cluster, follow the instructions at [Modifying the DB cluster by using the console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\. 
+  **By using the AWS CLI** – Call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command, and specify the name of your DB cluster for the `--db-cluster-identifier` option and the engine version for the `--engine-version` option\. 

   For example, to upgrade to Aurora MySQL version 2\.03\.2, set the `--engine-version` option to `5.7.mysql_aurora.2.03.2`\. Specify the `--apply-immediately` option to immediately update the engine version for your DB cluster\. 
+  **By using the RDS API** – Call the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation, and specify the name of your DB cluster for the `DBClusterIdentifier` parameter and the engine version for the `EngineVersion` parameter\. Set the `ApplyImmediately` parameter to `true` to immediately update the engine version for your DB cluster\. 

## Enabling automatic upgrades between minor Aurora MySQL versions<a name="AuroraMySQL.Updates.AMVU"></a>

 For an Amazon Aurora MySQL DB cluster that's running Aurora MySQL version 2, you can specify that Aurora upgrades the DB cluster automatically to new minor versions as those versions are released\. You do so by enabling the automatic minor version upgrade property of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\. 

 The automatic upgrades occur during the maintenance window for the database\. 

**Important**  
 Until August 2020, you could specify this setting for a DB instance that was part of an Aurora MySQL DB cluster, but the setting had no effect\. Now, the setting does apply to Aurora MySQL\. If you have clusters created before August 2020, check whether the DB instances in the cluster already had the **Enable auto minor version upgrade** setting enabled\. If so, confirm that this setting is still appropriate and change it if not\. Aurora only performs the automatic upgrade if all DB instances in your cluster have this setting enabled\. 

 Currently, automatic minor version upgrades change the engine version for Aurora MySQL 1\.x clusters to 1\.22\.2, and for Aurora MySQL 2\.x clusters to 2\.07\.2\. 

 Automatic minor version upgrade doesn't apply to clusters running the LTS version for Aurora MySQL 1\.x or 2\.x\. Aurora won't automatically upgrade clusters running 2\.04\.9, 1\.19\.6, or other patch levels of those minor versions\. 

 Automatic minor version upgrade doesn't apply to the following kinds of Aurora MySQL clusters: 
+  Multi\-master clusters\. 
+  Clusters that are part of an Aurora global database\. 
+  Clusters that have cross\-Region replicas\. 

 If any of the DB instances in a cluster don't have the auto minor version upgrade setting turned on, Aurora doesn't automatically upgrade any of the instances in that cluster\. Make sure to keep that setting consistent for all the DB instances in the cluster\. 

 The outage duration varies depending on workload, cluster size, the amount of binary log data, and if Aurora can use the zero\-downtime patching \(ZDP\) feature\. Aurora restarts the database cluster, so you might experience a short period of unavailability before resuming use of your cluster\. In particular, the amount of binary log data affects recovery time\. The DB instance processes the binary log data during recovery\. Thus, a high volume of binary log data increases recovery time\. 

 **To enable automatic minor version upgrades for an Aurora MySQL DB cluster** 

1.  Follow the general procedure to modify the DB instances in your cluster, as described in [Modify a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\. Repeat this procedure for each DB instance in your cluster\. 

1.  Do the following to enable automatic minor version upgrades for your cluster: 
+  **By using the console** – Complete the following steps: 

  1.  Sign in to the Amazon RDS console\. choose **Databases**, and find the DB cluster where you want to turn automatic minor version upgrade on or off\. 

  1.  Choose each DB instance in the DB cluster that you want to modify\. Apply the following change for each DB instance in sequence: 

     1.  Choose **Modify**\. 

     1.  Choose the **Enable auto minor version upgrade** setting\. This setting is part of the **Maintenance** section\. 

     1.  Choose **Continue** and check the summary of modifications\. 

     1.  \(Optional\) Choose **Apply immediately** to apply the changes immediately\. 

     1.  On the confirmation page, choose **Modify DB instance**\. 
+  **By using the AWS CLI** – Call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command\. Specify the name of your DB instance for the `--db-instance-identifier` option and `true` for the `--auto-minor-version-upgrade` option\. Optionally, specify the `--apply-immediately` option to immediately enable this setting for your DB instance\. Run a separate `modify-db-instance` command for each DB instance in the cluster\. 
+  **By using the RDS API** – Call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation and specify the name of your DB cluster for the `DBInstanceIdentifier` parameter and `true` for the `AutoMinorVersionUpgrade` parameter\. Optionally, set the `ApplyImmediately` parameter to `true` to immediately enable this setting for your DB instance\. Call a separate `ModifyDBInstance` operation for each DB instance in the cluster\. 

 You can use a CLI command such as the following to check the status of the **Enable auto minor version upgrade** for all of the DB instances in your Aurora MySQL clusters\. 

```
aws rds describe-db-instances \
--query '*[].{DBClusterIdentifier:DBClusterIdentifier,DBInstanceIdentifier:DBInstanceIdentifier,AutoMinorVersionUpgrade:AutoMinorVersionUpgrade}'
```

 That command produces output similar to the following\. 

```
[
{
    "DBInstanceIdentifier": "db-t2-medium-instance",
    "DBClusterIdentifier": "cluster-57-2020-06-03-6411",
    "AutoMinorVersionUpgrade": true
},
{
    "DBInstanceIdentifier": "db-t2-small-original-size",
    "DBClusterIdentifier": "cluster-57-2020-06-03-6411",
    "AutoMinorVersionUpgrade": false
},
{
    "DBInstanceIdentifier": "instance-2020-04-11-5110",
    "DBClusterIdentifier": "tpch100g",
    "AutoMinorVersionUpgrade": false
},
{
    "DBInstanceIdentifier": "instance-2020-05-01-2332",
    "DBClusterIdentifier": "cluster-57-2020-05-01-4615",
    "AutoMinorVersionUpgrade": true
},
... output omitted ...
```

## Upgrading Aurora MySQL by applying pending maintenance to an Aurora MySQL DB cluster<a name="AuroraMySQL.Updates.PendingMaintenance"></a>

 When upgrading to Aurora MySQL version 1\.x versions, new database engine minor versions and patches show as an **available** maintenance upgrade for your DB cluster\. You can upgrade or patch the database version of your DB cluster by applying the available maintenance action\. We recommend applying the update on a nonproduction DB cluster first, so that you can see how changes in the new version affect your instances and applications\. 

 **To apply pending maintenance actions** 
+  **By using the console** – Complete the following steps: 

  1.  Sign in to the Amazon RDS console, choose **Databases**, and choose the DB cluster that shows the **available** maintenance upgrade\. 

  1.  For **Actions**, choose **Upgrade now** to immediately update the database version for your DB cluster, or **Upgrade at next window** to update the database version for your DB cluster during the next DB cluster maintenance window\. 
+  **By using the AWS CLI** – Call the [apply\-pending\-maintenance\-action](https://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command, and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `--resource-id` option and `system-update` for the `--apply-action` option\. Set the `--opt-in-type` option to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your DB cluster during the next cluster maintenance window\. 
+  **By using the RDS API** – Call the [ApplyPendingMaintenanceAction](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) API operation, and specify the ARN for your DB cluster for the `ResourceId` parameter and `system-update` for the `ApplyAction` parameter\. Set the `OptInType` parameter to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your instance during the next cluster maintenance window\. 

 For more information on how Amazon RDS manages database and operating system updates, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 If your current Aurora MySQL version is 1\.14\.x but lower than 1\.14\.4, you can upgrade only to 1\.14\.4 \(which supports db\.r4 instance classes\)\. Also, to upgrade from 1\.14\.x to a higher minor Aurora MySQL version, such as 1\.17, the 1\.14\.x version must be 1\.14\.4\. 

## Using zero\-downtime patching<a name="AuroraMySQL.Updates.ZDP"></a>

 The zero\-downtime patching \(ZDP\) feature attempts, on a best\-effort basis, to preserve client connections through an engine patch\. If ZDP completes successfully, application sessions are preserved and the database engine restarts while patching\. The database engine restart can cause a drop in throughput lasting approximately 5 seconds\. 

 ZDP is available in Aurora MySQL 1\.13 and later, compatible with MySQL 5\.6\. It's also available in Aurora MySQL 2\.07 and later, compatible with MySQL 5\.7\. 

 ZDP might not complete successfully under the following conditions: 
+  Long\-running queries or transactions are in progress\. If Aurora can perform ZDP in this case, any open transactions are canceled\. 
+  Binary logging is enabled or binary log replication is in\-progress\. 
+  Open Secure Socket Layer \(SSL\) connections exist\. 
+  Temporary tables or table locks are in use, for example while data definition language \(DDL\) statements run\. If Aurora can perform ZDP in this case, any open transactions are canceled\. 
+  Pending parameter changes exist\. 

 Starting in Aurora MySQL 1\.19 and 2\.07, the ZDP mechanism is improved\. These improvements make ZDP more likely to succeed when there are long\-running transactions, binary logging, table locks, or temporary tables\. 

 If no suitable time window for performing ZDP becomes available because of one or more of these conditions, patching reverts to the standard behavior\. 

**Note**  
 ZDP applies only to the primary instance of a DB cluster\. ZDP isn't applicable to Aurora Replicas\.   
 Prepared statements don't prevent ZDP, but they aren't preserved after ZDP completes\. 