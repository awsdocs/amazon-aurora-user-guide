# Upgrading the minor version or patch level of an Aurora MySQL DB cluster<a name="AuroraMySQL.Updates.Patching"></a>

 You can use the following methods to upgrade the minor version of a DB cluster or to patch a DB cluster: 
+ [Upgrading Aurora MySQL by modifying the engine version](#AuroraMySQL.Updates.Patching.ModifyEngineVersion) \(for Aurora MySQL version 2 and 3\)
+ [Enabling automatic upgrades between minor Aurora MySQL versions](#AuroraMySQL.Updates.AMVU)

 For information about how zero\-downtime patching can reduce interruptions during the upgrade process, see [Using zero\-downtime patching](#AuroraMySQL.Updates.ZDP)\. 

## Upgrading Aurora MySQL by modifying the engine version<a name="AuroraMySQL.Updates.Patching.ModifyEngineVersion"></a>

Upgrading the minor version of an Aurora MySQL cluster applies additional fixes and new features to an existing cluster\.

This kind of upgrade applies to Aurora MySQL clusters where the original version and the upgraded version are both in the Aurora MySQL 2\.x series\. The process is fast and straightforward because it doesn't involve any conversion for the Aurora MySQL metadata or reorganization of your table data\.

You perform this kind of upgrade by modifying the engine version of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\. If your cluster is running Aurora MySQL 2\.x, choose a higher 2\.x version\.

If you're performing a minor upgrade on an Aurora global database, upgrade all of the secondary clusters before you upgrade the primary cluster\.

**Note**  
To perform a minor version upgrade to Aurora MySQL version 3\.03\.\*, use the following process:  
Remove all secondary Regions from the global cluster\. Follow the steps in [Removing a cluster from an Amazon Aurora global database](aurora-global-database-managing.md#aurora-global-database-detaching)\.
Upgrade the engine version of the primary Region to 3\.03\.\*\. Follow the steps in [To modify the engine version of a DB cluster](#modify-db-cluster-engine-version)\.
Add secondary Regions to the global cluster\. Follow the steps in [Adding an AWS Region to an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-attaching)\.

 **To modify the engine version of a DB cluster** 
+ **By using the console** – Modify the properties of your cluster\. In the **Modify DB cluster** window, change the Aurora MySQL engine version in the **DB engine version** box\. If you aren't familiar with the general procedure for modifying a cluster, follow the instructions at [Modifying the DB cluster by using the console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\.
+ **By using the AWS CLI** – Call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command, and specify the name of your DB cluster for the `--db-cluster-identifier` option and the engine version for the `--engine-version` option\.

  For example, to upgrade to Aurora MySQL version 2\.11\.1, set the `--engine-version` option to `5.7.mysql_aurora.2.11.1`\. Specify the `--apply-immediately` option to immediately update the engine version for your DB cluster\.
+ **By using the RDS API** – Call the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation, and specify the name of your DB cluster for the `DBClusterIdentifier` parameter and the engine version for the `EngineVersion` parameter\. Set the `ApplyImmediately` parameter to `true` to immediately update the engine version for your DB cluster\.

## Enabling automatic upgrades between minor Aurora MySQL versions<a name="AuroraMySQL.Updates.AMVU"></a><a name="amvu"></a>

 For an Amazon Aurora MySQL DB cluster, you can specify that Aurora upgrades the DB cluster automatically to new minor versions\. You do so by using the automatic minor version upgrade property of the DB cluster using the AWS Management Console, AWS CLI, or the RDS API\. 

 The automatic upgrades occur during the maintenance window for the database\. 

 Automatic minor version upgrade doesn't apply to the following kinds of Aurora MySQL clusters: 
+ Clusters that are part of an Aurora global database
+ Clusters that have cross\-Region replicas

 The outage duration varies depending on workload, cluster size, the amount of binary log data, and if Aurora can use the zero\-downtime patching \(ZDP\) feature\. Aurora restarts the database cluster, so you might experience a short period of unavailability before resuming use of your cluster\. In particular, the amount of binary log data affects recovery time\. The DB instance processes the binary log data during recovery\. Thus, a high volume of binary log data increases recovery time\. 

**Note**  
Aurora only performs the automatic upgrade if all DB instances in your cluster have this setting turned on\. The setting for automatic minor version upgrade at the DB cluster level doesn't override the settings for the DB instances in the cluster\.  
Auto minor version upgrades are performed to the default minor version\.

 **To enable automatic minor version upgrades for an Aurora MySQL DB cluster** 

1.  Follow the general procedure to modify the DB instances in your cluster, as described in [Modifying a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\. Repeat this procedure for each DB instance in your cluster\. 

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

 You can use a CLI command such as the following to check the status of the **Enable auto minor version upgrade** setting for all of the DB instances in your Aurora MySQL clusters\. 

```
aws rds describe-db-instances \
  --query '*[].{DBClusterIdentifier:DBClusterIdentifier,DBInstanceIdentifier:DBInstanceIdentifier,AutoMinorVersionUpgrade:AutoMinorVersionUpgrade}'
```

That command produces output similar to the following:

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

In this example, **Enable auto minor version upgrade** is turned off for the DB cluster `cluster-57-2020-06-03-6411`, because it's turned off for one of the DB instances in the cluster\.

## Using zero\-downtime patching<a name="AuroraMySQL.Updates.ZDP"></a><a name="zdp"></a>

 Performing upgrades for Aurora MySQL DB clusters involves the possibility of an outage when the database is shut down and while it's being upgraded\. By default, if you start the upgrade while the database is busy, you lose all the connections and transactions that the DB cluster is processing\. If you wait until the database is idle to perform the upgrade, you might have to wait a long time\. 

 The zero\-downtime patching \(ZDP\) feature attempts, on a best\-effort basis, to preserve client connections through an Aurora MySQL upgrade\. If ZDP completes successfully, application sessions are preserved and the database engine restarts while the upgrade is in progress\. The database engine restart can cause a drop in throughput lasting for a few seconds to approximately one minute\. 

The following table shows the Aurora MySQL versions and DB instance classes where ZDP is available\.


| Version | db\.r\* instance classes | db\.t\* instance classes | db\.x\* instance classes | db\.serverless instance class | 
| --- | --- | --- | --- | --- | 
| 2\.07\.2 and higher 2\.07 versions | No | Yes | No | N/A | 
| 2\.10\.0 and higher 2\.10 versions | No | No | No | N/A | 
| 2\.11\.0 and higher 2\.11 versions | Yes | Yes | Yes | N/A | 
| 3\.01\.0 and 3\.01\.1 | Yes | Yes | Yes | N/A | 
| 3\.02\.0 and higher 3\.\.02 versions | Yes | Yes | Yes | Yes | 
| 3\.03\.0 | No | No | No | No | 

**Note**  
We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more details on the T instance classes, see [Using T instance classes for development and testing](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.T2Medium)\.

 You can see metrics of important attributes during ZDP in the MySQL error log\. You can also see information about when Aurora MySQL uses ZDP or chooses not to use ZDP on the **Events** page in the AWS Management Console\. 

In Aurora MySQL version 2\.10 and higher and version 3, Aurora can perform a zero\-downtime patch whether or not binary log replication is enabled\. If binary log replication is enabled, Aurora MySQL automatically drops the connection to the binlog target during a ZDP operation\. Aurora MySQL automatically reconnects to the binlog target and resumes replication after the restart finishes\.

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

The following table summarizes how ZDP works for upgrading from and to specific Aurora MySQL 1\.x and 2\.x versions\. The instance class of the DB instance also affects whether Aurora uses the ZDP mechanism\.


|  Original version  |  Upgraded version  |  Does ZDP apply?  | 
| --- | --- | --- | 
|  Aurora MySQL 1\.\*  |  Any  |  No  | 
|  Aurora MySQL 2\.\*, before 2\.07\.2  |  Any  |  No  | 
|  Aurora MySQL 2\.07\.2, 2\.07\.3  |  2\.07\.4 and higher 2\.07 versions, 2\.10\.\*  |  Yes, on the writer instance for T2 and T3 instance classes only\. Aurora only performs ZDP if a quiet point is found before a timeout occurs\. After the timeout, Aurora performs a regular restart\.  | 
|  Aurora MySQL 2\.07\.4 and higher 2\.07 versions  |  2\.10\.\*  |  Yes, on the writer instance for T2 and T3 instances only\. Aurora rolls back transactions for active and idle transactions\. Connections using SSL, temporary tables, table locks, or user locks are disconnected\. Aurora might restart the engine and drop all connections if the engine takes too long to start after ZDP finishes\.  | 
|  Aurora MySQL 2\.10\.\*  | 2\.10\.\* |  Yes, on the writer instance for `db.t*` and `db.r*` instances\. Aurora rolls back transactions for active and idle transactions\. Connections using SSL, temporary tables, table locks, or user locks are disconnected\. Aurora might restart the engine and drop all connections if the engine takes too long to start after ZDP finishes\.  | 

## Alternative blue\-green upgrade technique<a name="AuroraMySQL.UpgradingMinor.BlueGreen"></a>

In some situations, your top priority is to perform an immediate switchover from the old cluster to an upgraded one\. In such situations, you can use a multistep process that runs the old and new clusters side\-by\-side\. Here, you replicate data from the old cluster to the new one until you are ready for the new cluster to take over\. For details, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.