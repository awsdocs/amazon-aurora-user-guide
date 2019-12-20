# Upgrading an Aurora PostgreSQL DB Cluster Engine Version<a name="USER_UpgradeDBInstance.Upgrading"></a>

Amazon Aurora provides newer versions of each supported database engine so you can keep your DB cluster up\-to\-date\. Newer versions can include bug fixes, security enhancements, and other improvements for the database engine\. When Amazon Aurora supports a new version of a database engine, you can choose how and when to upgrade your database DB clusters\.

There are two kinds of upgrades: major version upgrades and minor version upgrades\. In general, a *major engine version upgrade* can introduce changes that are not compatible with existing applications\. In contrast, a *minor version upgrade* includes only changes that are backward\-compatible with existing applications\.

**Note**  
Aurora PostgreSQL does not currently support in\-place major version upgrades\. To migrate a database from one major version to another you can use dump and load tools such as the PostgreSQL utilities [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) and [pg\_restore](https://www.postgresql.org/docs/current/app-pgrestore.html)\.

The version numbering sequence is specific to each database engine\. For example, Aurora PostgreSQL 9\.6 and 10\.5 are major engine versions and upgrading from any 9\.6 version to any 10\.x version is a major version upgrade\. Aurora PostgreSQL version 9\.6\.8 and 9\.6\.9 are minor versions and upgrading from 9\.6\.8 to 9\.6\.9 is a minor version upgrade\. To determine the version of an Aurora DB cluster, follow the instructions in [Amazon Aurora Updates](Aurora.Updates.md)\.

**Note**  
A PostgreSQL engine upgrade doesn't upgrade any PostgreSQL extensions\. For more information, see [Upgrading PostgreSQL Extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)\. 

**Topics**
+ [Manually Upgrading the Minor Engine Version](#USER_UpgradeDBInstance.Upgrading.Manual)
+ [Automatically Upgrading the Minor Engine Version](#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)
+ [Upgrading PostgreSQL Extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)

## Manually Upgrading the Minor Engine Version<a name="USER_UpgradeDBInstance.Upgrading.Manual"></a>

To perform a minor version upgrade of an Aurora PostgreSQL DB cluster, use the following instructions for the AWS Management Console, the AWS CLI, or the RDS API\. 

### Console<a name="USER_UpgradeDBInstance.Upgrading.Manual.Console"></a>

**To upgrade the engine version of a DB cluster by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster that you want to upgrade\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. For **DB engine version**, choose the new version\.

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify Cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="USER_UpgradeDBInstance.Upgrading.Manual.CLI"></a>

To upgrade the engine version of a DB cluster, use the CLI [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\. Specify the following parameters: 
+ `--db-cluster-identifier` – the name of the DB cluster\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. For information about valid engine versions, use the AWS CLI [ describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.
+ `--no-apply-immediately` – apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --engine-version new_version \
4.     --no-apply-immediately
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --engine-version new_version ^
4.     --no-apply-immediately
```

### RDS API<a name="USER_UpgradeDBInstance.Upgrading.Manual.API"></a>

To upgrade the engine version of a DB cluster, use the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) action\. Specify the following parameters: 
+ `DBClusterIdentifier` – the name of the DB cluster, for example *`mydbcluster`*\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. For information about valid engine versions, use the [ DescribeDBEngineVersions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBEngineVersions.html) operation\.
+ `ApplyImmediately` – whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. 

## Automatically Upgrading the Minor Engine Version<a name="USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades"></a>

A *minor engine version* is an update to a DB engine version within a major engine version\. For example, a major engine version might be 9\.6 with the minor engine versions 9\.6\.11 and 9\.6\.12 within it\. 

If you want Amazon Aurora to upgrade the DB engine version of a database automatically, you can enable auto minor version upgrades for the database\. When a minor engine version is designated as the preferred minor engine version, each database that meets both of the following conditions is upgraded to the minor engine version automatically:
+ The database is running a minor version of the DB engine that is lower than the preferred minor engine version\.
+ The database has auto minor version upgrade enabled\.

You can control whether auto minor version upgrade is enabled for a DB instance when you perform the following tasks:
+ [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)
+ [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)
**Note**  
Modify the writer DB instance to control whether auto minor version upgrade is enabled for the entire DB cluster\.
+ [Restoring a DB cluster from a snapshot](USER_RestoreFromSnapshot.md)
**Note**  
When you restore a DB cluster from a snapshot, you can control whether auto minor version upgrade is enabled for the DB cluster only when you use the console\.
+ [Restoring a DB cluster to a specific time](USER_PIT.md)
**Note**  
When you restore a DB cluster to a specific time, you can control whether auto minor version upgrade is enabled for the DB cluster only when you use the console\.

When you perform these tasks, you can control whether auto minor version upgrade is enabled for the DB cluster in the following ways:
+ Using the console, set the **Auto minor version upgrade** option\.
+ Using the AWS CLI, set the `--auto-minor-version-upgrade|--no-auto-minor-version-upgrade` option\.
+ Using the RDS API, set the `AutoMinorVersionUpgrade` parameter\.

To determine whether a maintenance update, such as a DB engine version upgrade, is available for your DB cluster, you can use the console, AWS CLI, or RDS API\. You can also upgrade the DB engine version manually and adjust the maintenance window\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Upgrading PostgreSQL Extensions<a name="USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades"></a>

A PostgreSQL engine upgrade doesn't upgrade any PostgreSQL extensions\. To update an extension after an engine upgrade, use the `ALTER EXTENSION UPDATE` command\. 

**Note**  
If you are running the `PostGIS` extension in your Amazon RDS PostgreSQL DB instance, make sure that you follow the [PostGIS upgrade instructions](https://postgis.net/docs/postgis_installation.html#upgrading) in the PostGIS documentation before you upgrade the extension\. 

To upgrade an extension, use the following command\. 

```
ALTER EXTENSION extension_name UPDATE TO 'new_version';
```

To list your currently installed extensions, use the PostgreSQL [pg\_extension](https://www.postgresql.org/docs/current/catalog-pg-extension.html) catalog in the following command:

```
SELECT * FROM pg_extension;
```

To view a list of the specific extension versions that are available for your installation, use the PostgreSQL [ pg\_available\_extension\_versions](https://www.postgresql.org/docs/current/view-pg-available-extension-versions.html) view in the following command: 

```
SELECT * FROM pg_available_extension_versions;
```