# Upgrading the PostgreSQL DB Engine for Aurora PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL"></a>

When Aurora PostgreSQL supports a new version of a database engine, you can upgrade your DB clusters to the new version\. There are two kinds of upgrades for PostgreSQL DB clusters: major version upgrades and minor version upgrades\. 

*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB cluster\. However, before you perform a major version upgrade, we recommend that you follow the steps described in [How to Perform a Major Version Upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. 

In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB cluster\. Or you can enable the **Auto minor version upgrade** option when creating or modifying a DB cluster\. Doing so means that your DB cluster is automatically upgraded after Aurora PostgreSQL tests and approves the new version\. For more details, see [Automatic Minor Version Upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Minor)\. For information about manually performing a minor version upgrade, see [Manually Upgrading the Aurora PostgreSQL Engine](#USER_UpgradeDBInstance.Upgrading.Manual)\.

Aurora DB clusters that are configured as logical replication publishers or subscribers can't undergo a major version upgrade\. For more information, see [Using PostgreSQL Logical Replication with Aurora](AuroraPostgreSQL.Replication.Logical.md)\.

You can do the following Aurora PostgreSQL major version upgrades:
+ Upgrade from version 9\.6 minor versions 9\.6\.9 and greater, to version 10 minor versions 10\.11 or greater\. 
+ Upgrade from version 10 minor versions 10\.7 and greater, to version 11 minor versions 11\.7 or greater\. 

For how to determine valid upgrade targets, see [Determining Which Engine Version to Upgrade to](#USER_UpgradeDBInstance.PostgreSQL.UpgradeVersion)\. 

**Topics**
+ [Overview of Upgrading Aurora PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Overview)
+ [PostgreSQL Version Numbers](#USER_UpgradeDBInstance.PostgreSQL.VersionID)
+ [Determining Which Engine Version to Upgrade to](#USER_UpgradeDBInstance.PostgreSQL.UpgradeVersion)
+ [How to Perform a Major Version Upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)
+ [Manually Upgrading the Aurora PostgreSQL Engine](#USER_UpgradeDBInstance.Upgrading.Manual)
+ [Automatic Minor Version Upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Minor)
+ [Upgrading PostgreSQL Extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)

## Overview of Upgrading Aurora PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Overview"></a>

Major version upgrades can contain database changes that are not backward\-compatible with previous versions of the database\. This functionality can cause your existing applications to stop working correctly\. As a result, Amazon Aurora doesn't apply major version upgrades automatically\. To perform a major version upgrade, you modify your DB cluster manually\. 

To safely upgrade your DB instances, Aurora PostgreSQL uses the pg\_upgrade utility described in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/pgupgrade.html)\. After the writer upgrade completes, each reader instance experiences a brief outage while it's upgraded to the new major version automatically\.

Aurora PostgreSQL takes a DB cluster snapshot before the upgrade begins\. If you want to return to a previous version after an upgrade is complete, you can restore the DB cluster from this snapshot created before the upgrade, or you can restore the DB cluster to a specific point in time before the upgrade started\. For more information, see [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md) or [Restoring a DB Cluster to a Specified Time](USER_PIT.md)\. 

During the major version upgrade process, a cloned volume is allocated\. If the upgrade fails for some reason, such as due to a schema incompatibility, Aurora PostgreSQL uses this clone to roll back the upgrade\. Note, when more than 15 clones of a source volume are allocated, subsequent clones become full copies and will take longer\. This can cause the upgrade process to take longer as well\. If Aurora PostgreSQL rolls back the upgrade, be aware of the following:
+ You may see billing entries and metrics for both the original volume and the cloned volume allocated during the upgrade\. Aurora PostgreSQL will clean up the extra volume after the cluster backup retention window is beyond the time of the upgrade\.
+ The next cross region snapshot copy from this cluster will be a full copy instead of an incremental copy\.

## PostgreSQL Version Numbers<a name="USER_UpgradeDBInstance.PostgreSQL.VersionID"></a>

To determine the current Aurora PostgreSQL version and PostgreSQL database engine version of a DB cluster, see [Identifying Your Version of Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.md#AuroraPostgreSQL.Updates.Versions)\.

The version numbering sequence for the PostgreSQL database engine is as follows: 
+ For PostgreSQL versions 10 and later, the engine version number is in the form *major\.minor*\. The major version number is the integer part of the version number\. The minor version number is the fractional part of the version number\. 

  A major version upgrade increases the integer part of the version number, such as upgrading from 10\.*minor* to 11\.*minor*\.
+ For PostgreSQL versions earlier than 10, the engine version number is in the form *major\.major\.minor*\. The major engine version number is both the integer and the first fractional part of the version number\. For example, 9\.6 is a major version\. The minor version number is the third part of the version number\. For example, for version 9\.6\.12, the 12 is the minor version number\.

  A major version upgrade increases the major part of the version number\. For example, an upgrade from *9\.6*\.12 to *10*\.7 is a major version upgrade, where *9\.6* and *10* are the major version numbers\.

## Determining Which Engine Version to Upgrade to<a name="USER_UpgradeDBInstance.PostgreSQL.UpgradeVersion"></a>

To determine which major engine version that you can upgrade your database to, use the [ `describe-db-engine-versions`](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) CLI command\. In the output, a `ValidUpgradeTarget` array contains the target versions\. If the `IsMajorVersionUpgrade` value is true, you can do a major version upgrade to the associated `EngineVersion`\. If the array is empty, you can't do a major version upgrade\. You first upgrade to a minor version that has a major version upgrade path\.

For example, the following command displays the major engine versions available for upgrading a DB cluster currently running the Aurora PostgreSQL engine version 9\.6\.12\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-db-engine-versions  --engine aurora-postgresql  --engine-version 9.6.12  \
   --query 'DBEngineVersions[].ValidUpgradeTarget[?IsMajorVersionUpgrade == `true`]'
```
For Windows:  

```
aws rds describe-db-engine-versions  --engine aurora-postgresql  --engine-version 9.6.12  ^
   --query "DBEngineVersions[].ValidUpgradeTarget[?IsMajorVersionUpgrade == `true`]"
```

## How to Perform a Major Version Upgrade<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion"></a>

Major version upgrades can contain database changes that are not backward\-compatible with previous versions of the database\. This functionality can cause your existing applications to stop working correctly\. As a result, Amazon Aurora doesn't apply major version upgrades automatically\. To perform a major version upgrade, you modify your DB cluster manually\. 

Before applying an upgrade to your production DB clusters, make sure that you thoroughly test any upgrade to verify that your applications work correctly\. 

We recommend the following process when upgrading an Aurora PostgreSQL DB cluster:

1. Have a version\-compatible parameter group ready\.

   If you are using a custom DB instance or DB cluster parameter group, you have two options: 
   + Specify the default DB instance, DB cluster parameter group, or both for the new DB engine version\. 
   + Create your own custom parameter group for the new DB engine version\.

   If you associate a new DB instance or DB cluster parameter group as a part of the upgrade request, make sure to reboot the database after the upgrade completes to apply the parameters\. If a DB instance needs to be rebooted to apply the parameter group changes, the instance's parameter group status shows `pending-reboot`\. You can view an instance's parameter group status in the console or by using a CLI command such as [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) or [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)\.

1. Check for unsupported usage:
   + Commit or roll back all open prepared transactions before attempting an upgrade\. You can use the following query to verify that there are no open prepared transactions on your instance\. 

     ```
     SELECT count(*) FROM pg_catalog.pg_prepared_xacts;
     ```
   + Remove all uses of the *reg\** data types before attempting an upgrade\. Except for `regtype` and `regclass`, you can't upgrade the *reg\** data types\. The pg\_upgrade utility can't persist this data type, which is used by Amazon Aurora to do the upgrade\. For more information about the pg\_upgrade utility, see the [PostgreSQL documentation](https://www.postgresql.org/docs/current/pgupgrade.html)\.

     To verify that there are no uses of unsupported *reg\** data types, use the following query for each database\. 

     ```
     SELECT count(*) FROM pg_catalog.pg_class c, pg_catalog.pg_namespace n, pg_catalog.pg_attribute a 
       WHERE c.oid = a.attrelid 
           AND NOT a.attisdropped 
           AND a.atttypid IN ('pg_catalog.regproc'::pg_catalog.regtype, 
                              'pg_catalog.regprocedure'::pg_catalog.regtype, 
                              'pg_catalog.regoper'::pg_catalog.regtype, 
                              'pg_catalog.regoperator'::pg_catalog.regtype, 
                              'pg_catalog.regconfig'::pg_catalog.regtype, 
                              'pg_catalog.regdictionary'::pg_catalog.regtype) 
           AND c.relnamespace = n.oid 
           AND n.nspname NOT IN ('pg_catalog', 'information_schema');
     ```

1. Perform a backup\.

   The upgrade process creates a DB cluster snapshot of your DB cluster during upgrading\. If you also want to do a manual backup before the upgrade process, see [Creating a DB Cluster Snapshot](USER_CreateSnapshotCluster.md) for more information\.

1. Upgrade certain extensions to the latest available version before performing the major version upgrade\. The extensions to update include the following:
   + `pgRouting`
   + `postGIS`

   Run the following command for each extension that you are using\. 

   ```
   ALTER EXTENSION PostgreSQL-extension UPDATE TO 'new-version'
   ```

   For more information, see [Upgrading PostgreSQL Extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)\.

1. If you're upgrading to version 11\.x, drop the extensions that it doesn't support before performing the major version upgrade\. The extensions to drop include:
   + `chkpass`
   + `tsearch2` 

1. Drop `unknown` data types, depending on your target version\.

   PostgreSQL version 10 stopped supporting the `unknown` data type\. If a version 9\.6 database uses the `unknown` data type, an upgrade to a version 10 shows an error message such as the following\. 

   ```
   Database instance is in a state that cannot be upgraded: PreUpgrade checks failed: 
   The instance could not be upgraded because the 'unknown' data type is used in user tables. 
   Please remove all usages of the 'unknown' data type and try again."
   ```

   To find the `unknown` data type in your database so you can remove the offending column or change it to a supported data type, use the following SQL code\.

   ```
   SELECT DISTINCT data_type FROM information_schema.columns WHERE data_type ILIKE 'unknown';
   ```

1. Perform a dry run upgrade\.

   We highly recommend testing a major version upgrade on a duplicate of your production database before trying the upgrade on your production database\. To create a duplicate test instance, you can either restore your database from a recent snapshot or clone your database\. For more information, see [Restoring from a Snapshot](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Restoring) or [Cloning an Aurora DB Cluster Volume](Aurora.Managing.Clone.md)\.

   For more information, see [Manually Upgrading the Aurora PostgreSQL Engine](#USER_UpgradeDBInstance.Upgrading.Manual)\. 

1. Upgrade your production instance\.

   When your dry\-run major version upgrade is successful, you should be able to upgrade your production database with confidence\. For more information, see [Manually Upgrading the Aurora PostgreSQL Engine](#USER_UpgradeDBInstance.Upgrading.Manual)\. 
**Note**  
During the upgrade process, you can't do a point\-in\-time restore of your cluster\. Aurora PostgreSQL takes a DB cluster snapshot during the upgrade process if your backup retention period is greater than 0\. You can perform a point\-in\-time restore to times before the upgrade began and after the automatic snapshot of your instance has completed\. 

   For information about an upgrade in progress, you can use Amazon RDS to view two logs that the pg\_upgrade utility produces\. These are `pg_upgrade_internal.log` and `pg_upgrade_server.log`\. Amazon Aurora appends a timestamp to the file name for these logs\. You can view these logs as you can any other log\. For more information, see [Amazon Aurora Database Log Files](USER_LogAccess.md)\.

1. Upgrade PostgreSQL extensions\. The PostgreSQL upgrade process doesn't upgrade any PostgreSQL extensions\. For more information, see [Upgrading PostgreSQL Extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)\.

After you complete a major version upgrade, we recommend the following:
+ Run the `ANALYZE` operation to refresh the `pg_statistic` table\.
+ If you upgraded to PostgreSQL version 10, run `REINDEX` on any hash indexes you have\. Hash indexes were changed in version 10 and must be rebuilt\. To locate invalid hash indexes, run the following SQL for each database that contains hash indexes\.

  ```
  SELECT idx.indrelid::regclass AS table_name, 
     idx.indexrelid::regclass AS index_name 
  FROM pg_catalog.pg_index idx
     JOIN pg_catalog.pg_class cls ON cls.oid = idx.indexrelid 
     JOIN pg_catalog.pg_am am ON am.oid = cls.relam 
  WHERE am.amname = 'hash' 
  AND NOT idx.indisvalid;
  ```
+ Consider testing your application on the upgraded database with a similar workload to verify that everything works as expected\. After the upgrade is verified, you can delete this test instance\.

## Manually Upgrading the Aurora PostgreSQL Engine<a name="USER_UpgradeDBInstance.Upgrading.Manual"></a>

To perform an upgrade of an Aurora PostgreSQL DB cluster, use the following instructions for the AWS Management Console, the AWS CLI, or the RDS API\. 

### Console<a name="USER_UpgradeDBInstance.Upgrading.Manual.Console"></a>

**To upgrade the engine version of a DB cluster by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster that you want to upgrade\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. For **DB engine version**, choose the new version\.

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify Cluster** to save your changes\. 

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

### AWS CLI<a name="USER_UpgradeDBInstance.Upgrading.Manual.CLI"></a>

To upgrade the engine version of a DB cluster, use the CLI [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\. Specify the following parameters: 
+ `--db-cluster-identifier` – the name of the DB cluster\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. For information about valid engine versions, use the AWS CLI [ describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.
+ `--allow-major-version-upgrade` – a required flag when the `--engine-version` parameter is a different major version than the DB cluster's current major version\.
+ `--no-apply-immediately` – apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. 

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --engine-version new_version \
4.     --allow-major-version-upgrade \
5.     --no-apply-immediately
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --engine-version new_version ^
4.     --allow-major-version-upgrade ^
5.     --no-apply-immediately
```

### RDS API<a name="USER_UpgradeDBInstance.Upgrading.Manual.API"></a>

To upgrade the engine version of a DB cluster, use the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation\. Specify the following parameters: 
+ `DBClusterIdentifier` – the name of the DB cluster, for example *`mydbcluster`*\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. For information about valid engine versions, use the [ DescribeDBEngineVersions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBEngineVersions.html) operation\.
+ `AllowMajorVersionUpgrade` – a required flag when the `EngineVersion` parameter is a different major version than the DB cluster's current major version\.
+ `ApplyImmediately` – whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. 

## Automatic Minor Version Upgrades for PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Minor"></a>

If you enable the **Auto minor version upgrade** option when creating or modifying a DB cluster, you can have your cluster automatically upgraded\.

For each PostgreSQL major version, one minor version is designated by Amazon Aurora as the automatic upgrade version\. After a minor version has been tested and approved by Amazon Aurora, the minor version upgrade occurs automatically during your maintenance window\. Aurora doesn't automatically set newer released minor versions as the automatic upgrade version\. Before Aurora designates a newer automatic upgrade version, several criteria are considered, such as the following:
+ Known security issues
+ Bugs in the PostgreSQL community version
+ Overall fleet stability since the minor version was released

You can use the following AWS CLI command and script to determine the current automatic upgrade minor versions\. 

```
aws rds describe-db-engine-versions --engine aurora-postgresql | grep -A 1 AutoUpgrade| grep -A 2 true |grep PostgreSQL | sort --unique | sed -e 's/"Description": "//g'
```

**Note**  
If no results are returned, there is no automatic minor version upgrade available and scheduled\.

A PostgreSQL DB instance is automatically upgraded during your maintenance window if the following criteria are met:
+ The DB cluster has the **Auto minor version upgrade** option enabled\.
+ The DB cluster is running a minor DB engine version that is less than the current automatic upgrade minor version\.

## Upgrading PostgreSQL Extensions<a name="USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades"></a>

A PostgreSQL engine upgrade doesn't upgrade any PostgreSQL extensions\. To update an extension after an engine upgrade, use the `ALTER EXTENSION UPDATE` command\. 

**Note**  
If you are running the `PostGIS` extension in your Amazon RDS PostgreSQL DB instance, make sure that you follow the [PostGIS upgrade instructions](https://postgis.net/docs/postgis_installation.html#upgrading) in the PostGIS documentation before you upgrade the extension\. 

To upgrade an extension, use the following command\. 

```
ALTER EXTENSION extension_name UPDATE TO 'new_version';
```

To list your currently installed extensions, use the PostgreSQL [pg\_extension](https://www.postgresql.org/docs/current/catalog-pg-extension.html) catalog in the following command\.

```
SELECT * FROM pg_extension;
```

To view a list of the specific extension versions that are available for your installation, use the PostgreSQL [ pg\_available\_extension\_versions](https://www.postgresql.org/docs/current/view-pg-available-extension-versions.html) view in the following command\. 

```
SELECT * FROM pg_available_extension_versions;
```