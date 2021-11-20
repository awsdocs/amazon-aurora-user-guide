# Upgrading the minor version or patch level of an Aurora MySQL DB cluster<a name="AuroraMySQL.Updates.Patching"></a>

 You can use the following methods to upgrade the minor version of a DB cluster or to patch a DB cluster: 
+  [Upgrading Aurora MySQL by modifying the engine version](#AuroraMySQL.Updates.Patching.ModifyEngineVersion) \(for Aurora MySQL 1\.19\.0 and higher, or 2\.03\.2 and higher\) 
+  [Enabling automatic upgrades between minor Aurora MySQL versions](#AuroraMySQL.Updates.AMVU) 
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

## Enabling automatic upgrades between minor Aurora MySQL versions<a name="AuroraMySQL.Updates.AMVU"></a><a name="amvu"></a>

 For an Amazon Aurora MySQL DB cluster, you can specify that Aurora upgrades the DB cluster automatically to new minor versions as those versions are released\. You do so by enabling the automatic minor version upgrade property of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\. 

 The automatic upgrades occur during the maintenance window for the database\. 

**Important**  
 Until August 2020, you could specify this setting for a DB instance that was part of an Aurora MySQL DB cluster, but the setting had no effect\. Now, the setting does apply to Aurora MySQL\. If you have clusters created before August 2020, check whether the DB instances in the cluster already had the **Enable auto minor version upgrade** setting enabled\. If so, confirm that this setting is still appropriate and change it if not\. Aurora only performs the automatic upgrade if all DB instances in your cluster have this setting enabled\. 

 Automatic minor version upgrade applies also to clusters running the LTS version for Aurora MySQL 1\.x or 2\.x\. To prevent those clusters from being automatically upgraded, make sure to turn off the **Enable auto minor version upgrade** setting\. 

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

## Using zero\-downtime patching<a name="AuroraMySQL.Updates.ZDP"></a><a name="zdp"></a>

 Performing upgrades for Aurora MySQL DB clusters involves the possibility of an outage when the database is shut down and while it's being upgraded\. By default, if you start the upgrade while the database is busy, you lose all the connections and transactions that the DB cluster is processing\. If you wait until the database is idle to perform the upgrade, you might have to wait a long time\. 

 The zero\-downtime patching \(ZDP\) feature attempts, on a best\-effort basis, to preserve client connections through an Aurora MySQL upgrade\. If ZDP completes successfully, application sessions are preserved and the database engine restarts while the upgrade is in progress\. The database engine restart can cause a drop in throughput lasting for a few seconds to approximately one minute\. 

 ZDP is available in Aurora MySQL 2\.07\.2 and higher 2\.07 versions, and 2\.10\.0 and higher, compatible with MySQL 5\.7, and 3\.01\.0 and higher, compatible with MySQL 8\.0\. 

 In Aurora MySQL version 2, ZDP only applies to Aurora MySQL DB instances that use the `db.t2` or `db.t3` instance classes\. In Aurora MySQL version 3, ZDP applies to all instance classes\. 

 You can see metrics of important attributes during ZDP in the MySQL error log\. You can also see information about when Aurora MySQL uses ZDP or chooses not use use ZDP on the **Events** page in the AWS Management Console\. 

 In Aurora MySQL 2\.10 and higher, Aurora can perform a zero\-downtime patch when binary log replication is enabled\. Aurora MySQL automatically drops the connection to the binlog target during a ZDP operation\. Aurora MySQL automatically reconnects to the binlog target and resumes replication after the restart finishes\. 

 ZDP also works in combination with the reboot enhancements in Aurora MySQL 2\.10 and higher\. Patching the writer DB instance automatically patches readers at the same time\. After performing the patch, Aurora restores the connections on both the writer and reader DB instances\. Before Aurora MySQL 2\.10, ZDP applies only to the writer DB instance of a cluster\. 

 ZDP might not complete successfully under the following conditions: 
+  Long\-running queries or transactions are in progress\. If Aurora can perform ZDP in this case, any open transactions are canceled\. 
+  Open Secure Socket Layer \(SSL\) connections exist\. 
+  Temporary tables or table locks are in use, for example while data definition language \(DDL\) statements run\. If Aurora can perform ZDP in this case, any open transactions are canceled\. 
+  Pending parameter changes exist\. 

 If no suitable time window for performing ZDP becomes available because of one or more of these conditions, patching reverts to the standard behavior\. 

 Although connections remain intact following a successful ZDP operation, some variables and features are reinitialized\. The following kinds of information aren't preserved through a restart caused by zero\-downtime patching: 
+  Global variables\. Aurora restores session variables, but it doesn't restore global variables after the restart\. 
+  Status variables\. In particular, the uptime value reported by the engine status is reset after a restart that uses the ZDR or ZDP mechanisms\. 
+  `LAST_INSERT_ID`\.  
+  In\-memory `auto_increment` state for tables\. The in\-memory auto\-increment state is reinitialized\. For more information about auto\-increment values, see [MySQL Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/innodb-auto-increment-handling.html#innodb-auto-increment-initialization)\. 
+  Diagnostic information from `INFORMATION_SCHEMA` and `PERFORMANCE_SCHEMA` tables\. This diagnostic information also appears in the output of commands such as `SHOW PROFILE` and `SHOW PROFILES`\.  

 The following activities related to zero\-downtime restart are reported on the **Events** page: 
+  Attempting to upgrade the database with zero downtime\. 
+  Attempt to upgrade the database with zero downtime finished\. The event reports how long the process took\. The event also reports how many connections were preserved during the restart and how many connections were dropped\. You can consult the database error log to see more details about what happened during the restart\. 

 The following table summarizes how ZDP works for upgrading from and to specific Aurora MySQL versions\. The instance class of the DB instance also affects whether Aurora uses the ZDP mechanism\. 


|  Original version  |  Upgraded version  |  Does ZDP apply?  | 
| --- | --- | --- | 
|   Aurora MySQL 1\.\*   |   Any   |   No   | 
|   Aurora MySQL 2\.\*, before 2\.07\.2   |   Any   |   No   | 
|   Aurora MySQL 2\.07\.2, 2\.07\.3   |   2\.07\.4 and higher 2\.07 versions, 2\.10\.\*   |   Yes, on the writer instance for T2 and T3 instance classes only\. Aurora only performs ZDP if a quiet point is found before a timeout occurs\. After the timeout, Aurora performs a regular restart\.   | 
|   2\.07\.4 and higher 2\.07 versions   |   2\.10\.\*   |   Yes, on the writer instance for T2 and T3 instances only\. Aurora rolls back transactions for active and idle transactions\. Connections using SSL, temporary tables, table locks, or user locks are disconnected\. Aurora might restart the engine and drop all connections if the engine takes too long to start after ZDP finishes\.   | 

## Alternative blue\-green upgrade technique<a name="AuroraMySQL.Upgrading.BlueGreen"></a>

 Blog post: [Performing major version upgrades for Aurora MySQL with minimum downtime](http://aws.amazon.com/blogs/database/performing-major-version-upgrades-for-amazon-aurora-mysql-with-minimum-downtime/)\. 