# Upgrading the PostgreSQL DB engine for Aurora PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL"></a><a name="pgsql_upgrade"></a>

Amazon Aurora makes new versions of the PostgreSQL database engine available in AWS Regions only after extensive testing\. You can upgrade your Aurora PostgreSQL DB clusters to the new version when it's available in your Region\. 

Depending on the version of Aurora PostgreSQL that your DB cluster is currently running, an upgrade to the new release is either a minor upgrade or a major upgrade\. For example, upgrading an Aurora PostgreSQL 11\.15 DB cluster to Aurora PostgreSQL 13\.6 is a *major version upgrade*\. Upgrading an Aurora PostgreSQL 13\.3 DB cluster to Aurora PostgreSQL 13\.7 is a *minor version upgrade*\. In the following topics, you can find information about how to perform both types of upgrades\. 

**Contents**
+ [Overview of the Aurora PostgreSQL upgrade processes](#USER_UpgradeDBInstance.PostgreSQL.Overview)
+ [Getting a list of available versions in your AWS Region](#USER_UpgradeDBInstance.PostgreSQL.UpgradeVersion)
+ [How to perform a major version upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)
  + [Testing an upgrade of your production DB cluster to a new major version](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary)
  + [Upgrading the Aurora PostgreSQL engine to a new major version](#USER_UpgradeDBInstance.Upgrading.Manual)
    + [Major upgrades for global databases](#USER_UpgradeDBInstance.PostgreSQL.GlobalDB)
+ [How to perform minor version upgrades and apply patches](#USER_UpgradeDBInstance.PostgreSQL.Minor)
  + [Minor release upgrades and zero\-downtime patching](#USER_UpgradeDBInstance.PostgreSQL.Minor.zdp)
  + [Upgrading the Aurora PostgreSQL engine to a new minor version](#USER_UpgradeDBInstance.MinorUpgrade)
+ [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)

## Overview of the Aurora PostgreSQL upgrade processes<a name="USER_UpgradeDBInstance.PostgreSQL.Overview"></a>

The differences between major and minor version upgrades are as follows:

**Minor version upgrades and patches**  
Minor version upgrades and patches include only those changes that are backward\-compatible with existing applications\. Minor version upgrades and patches become available to you only after Aurora PostgreSQL tests and approves them\.   
Minor version upgrades can be applied for you automatically by Aurora\. When you create a new Aurora PostgreSQL DB cluster, the **Enable minor version upgrade** option is preselected\. Unless you turn off this option, minor version upgrades are applied automatically during your scheduled maintenance window\. For more information about the automatic minor version upgrade \(AMVU\) option and how to modify your Aurora DB cluster to use it, see [ Automatic minor version upgrades for Aurora DB clusters](USER_UpgradeDBInstance.Maintenance.md#Aurora.Maintenance.AMVU)\.  
If the automatic minor version upgrade option isn't set for your Aurora PostgreSQL DB cluster, your Aurora PostgreSQL isn't automatically upgraded to the new minor version\. Instead, when a new minor version is released in your AWS Region and your Aurora PostgreSQL DB cluster is running an older minor version, Aurora prompts you to upgrade\. It does so by adding a recommendation to the maintenance tasks for your cluster\.   
Patches aren't considered an upgrade, and they aren't applied automatically\. Aurora PostgreSQL prompts you to apply any patches by adding a recommendation to maintenance tasks for your Aurora PostgreSQL DB cluster\. For more information, see [How to perform minor version upgrades and apply patches](#USER_UpgradeDBInstance.PostgreSQL.Minor)\.   
Patches that resolve security or other critical issues are also added as maintenance tasks\. However, these patches are required\. Make sure to apply security patches to your Aurora PostgreSQL DB cluster when they become available in your pending maintenance tasks\.
The upgrade process involves the possibility of brief outages as each instance in the cluster is upgraded to the new version\. However, after Aurora PostgreSQL versions 14\.3\.3, 13\.7\.3, 12\.11\.3, 11\.16\.3, 10\.21\.3 and other higher releases of these minor versions and newer major versions, the upgrade process uses the zero\-downtime patching \(ZDP\) feature\. This feature minimizes outages, and in most cases completely eliminates them\. For more information, see [Minor release upgrades and zero\-downtime patching](#USER_UpgradeDBInstance.PostgreSQL.Minor.zdp)\.  
ZDP isn't supported in the following cases:  
+ When Aurora PostgreSQL DB clusters are configured as Aurora Serverless v1\.
+ During the upgrade of reader instances in Aurora global database\.
+ During OS patches and OS upgrades\.

**Major version upgrades**  
Unlike for minor version upgrades and patches, Aurora PostgreSQL doesn't have an automatic major version upgrade option\. New major PostgreSQL versions might contain database changes that aren't backward\-compatible with existing applications\. The new functionality can cause your existing applications to stop working correctly\.  
To prevent any issues, we strongly recommend that you follow the process outlined in [Testing an upgrade of your production DB cluster to a new major version](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary) before upgrading the DB instances in your Aurora PostgreSQL DB clusters\. First ensure that your applications can run on the new version by following that procedure\. Then you can manually upgrade your Aurora PostgreSQL DB cluster to the new version\.   
The upgrade process involves the possibility of brief outages as each instance in the cluster is upgraded to the new version\. The preliminary planning process also takes time\. We recommend that you always perform upgrade tasks during your cluster's maintenance window or when operations are minimal\. For more information, see [How to perform a major version upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\.

**Note**  
Both minor version upgrades and major version upgrades might involve brief outages\. For that reason, we recommend strongly that you perform or schedule upgrades during your maintenance window or during other periods of low utilization\.

Aurora PostgreSQL DB clusters occasionally require operating system updates\. These updates might include a newer version of glibc library\. During such updates, we recommend you to follow the guidelines as described in [Collations supported in Aurora PostgreSQL](PostgreSQL-Collations.md)\. 

## Getting a list of available versions in your AWS Region<a name="USER_UpgradeDBInstance.PostgreSQL.UpgradeVersion"></a>

You can get a list of all engine versions available as upgrade targets for your Aurora PostgreSQL DB cluster by querying your AWS Region using the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command, as follows\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine aurora-postgresql \
  --engine-version version-number \
  --query 'DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}' \
  --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine aurora-postgresql ^
  --engine-version version-number ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" ^
  --output text
```

For example, to identify the valid upgrade targets for an Aurora PostgreSQL version 12\.10 DB cluster, run the following AWS CLI command:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine aurora-postgresql \
  --engine-version 12.10 \
  --query 'DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}' \
  --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine aurora-postgresql ^
  --engine-version 12.10 ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" ^
  --output text
```

In this table, you can find both major and minor version upgrade targets that are available for various Aurora PostgreSQL DB versions\.  


| Current source version | Upgrade targets | 
| --- | --- | 
| 14\.7 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 14\.6 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 14\.5 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 14\.4 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 14\.3 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 13\.10 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 13\.9 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 13\.8 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 13\.7 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) |   |   |   |   |   |   |   |   |   |   | 
| 13\.6 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) |   |   |   |   |   |   |   |   |   | 
| 13\.5 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) |   |   |   |   |   |   |   |   | 
| 13\.4 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.135X) |   |   |   |   |   |   |   | 
| 13\.3 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.135X) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.134X) |   |   |   |   |   |   | 
| 12\.14 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 12\.13 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 12\.12 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) |   |   |   |   |   |   |   |   |   |   | 
| 12\.11 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) |   |   |   |   |   |   |   | 
| 12\.10 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) |   |   |   |   |   |   |   |   | 
| 12\.9 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.135X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) |   |   |   |   |   |   | 
| 12\.8 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.135X) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.134X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.129X) |   |   |   |   | 
| 12\.7 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.135X) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.134X) | [13\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.133X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.129X) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.128X) |   |   | 
| 11\.19 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   | 
| 11\.18 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.146X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) |   |   |   |   |   |   |   |   |   |   |   | 
| 11\.17 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.138X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1118X) |   |   |   |   |   |   |   |   |   | 
| 11\.16 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.145X) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.144X) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.139X) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.137X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1118X) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1117X) |   |   |   |   | 
| 11\.15 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.136X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1118X) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1117X) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1116X) |   |   |   |   |   |   | 
| 11\.14 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.135X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.129X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1118X) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1117X) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1116X) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1115X) |   |   |   |   | 
| 11\.13 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.134X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.129X) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.128X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1118X) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1117X) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1116X) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1115X) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1114X) |   |   | 
| 11\.9 | [15\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.152X) | [14\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.147X) | [13\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1310X) | [12\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1214X) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1213X) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1212X) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1211X) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1210X) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.129X) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.128X) | [12\.7](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.127X) | [11\.19](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1119X) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1118X) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1117X) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1116X) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1115X) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1114X) | [11\.13](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1113X) | [11\.12](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.1112X) | 

For any version that you're considering, always check the availability of your cluster's DB instance class\. For example, `db.r4` isn't supported for Aurora PostgreSQL 13\. If your Aurora PostgreSQL DB cluster currently uses a db\.r4 instance class, you need to move to db\.r5 before trying to upgrade\. For more information about DB instance classes, including which ones are Graviton2\-based and which ones are Intel\-based, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.

## How to perform a major version upgrade<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion"></a>

Major version upgrades might contain database changes that are not backward\-compatible with previous versions of the database\. New functionality in a new version can cause your existing applications to stop working correctly\. To avoid issues, Amazon Aurora doesn't apply major version upgrades automatically\. Rather, we recommend that you carefully plan for a major version upgrade by following these steps:

1. Choose the major version that you want from the list of available targets from those listed for your version in the table\. You can get a precise list of versions available in your AWS Region for your current version by using the AWS CLI\. For details, see [Getting a list of available versions in your AWS Region](#USER_UpgradeDBInstance.PostgreSQL.UpgradeVersion)\. 

1. Verify that your applications work as expected on a trial deployment of the new version\. For information about the complete process, see [Testing an upgrade of your production DB cluster to a new major version](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary)\.

1. After verifying that your applications work as expected on the trial deployment, you can upgrade your cluster\. For details, see [Upgrading the Aurora PostgreSQL engine to a new major version](#USER_UpgradeDBInstance.Upgrading.Manual)\.

**Note**  
You can perform a major version upgrade from Babelfish for Aurora PostgreSQL 13\-based versions starting from 13\.6 to Aurora PostgreSQL 14\-based versions starting from 14\.6\. Babelfish for Aurora PostgreSQL 13\.4 and 13\.5 don't support major version upgrade\.

You can get a list of engine versions available as major version upgrade targets for your Aurora PostgreSQL DB cluster by querying your AWS Region using the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command, as follows\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine aurora-postgresql \
  --engine-version version-number \
  --query 'DBEngineVersions[].ValidUpgradeTarget[?IsMajorVersionUpgrade == `true`].{EngineVersion:EngineVersion}' \
  --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine aurora-postgresql ^
  --engine-version version-number ^
  --query "DBEngineVersions[].ValidUpgradeTarget[?IsMajorVersionUpgrade == `true`].{EngineVersion:EngineVersion}" ^
  --output text
```

In some cases, the version that you want to upgrade to isn't a target for your current version\. In such cases, use the information in the [versions table](#versions-table) to perform minor version upgrades until your cluster is at a version that has your chosen target in its row of targets\.

### Testing an upgrade of your production DB cluster to a new major version<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary"></a>

Each new major version includes enhancements to the query optimizer that are designed to improve performance\. However, your workload might include queries that result in a worse performing plan in the new version\. That's why we recommend that you test and review performance before upgrading in production\. You can manage query plan stability across versions by using the Query Plan Management \(QPM\) extension, as detailed in [Ensuring plan stability after a major version upgrade](AuroraPostgreSQL.Optimize.BestPractice.md#AuroraPostgreSQL.Optimize.BestPractice.MajorVersionUpgrade)\. 

Before upgrading your production Aurora PostgreSQL DB clusters to a new major version, we strongly recommend that you test the upgrade to verify that all your applications work correctly:

1. Have a version\-compatible parameter group ready\.  

   If you are using a custom DB instance or DB cluster parameter group, you can choose from two options: 

   1. Specify the default DB instance, DB cluster parameter group, or both for the new DB engine version\. 

   1. Create your own custom parameter group for the new DB engine version\.

   If you associate a new DB instance or DB cluster parameter group as a part of the upgrade request, make sure to reboot the database after the upgrade completes to apply the parameters\. If a DB instance needs to be rebooted to apply the parameter group changes, the instance's parameter group status shows `pending-reboot`\. You can view an instance's parameter group status in the console or by using a CLI command such as [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) or [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)\.

1. Check for unsupported usage:
   + Commit or roll back all open prepared transactions before attempting an upgrade\. You can use the following query to verify that there are no open prepared transactions on your instance\. 

     ```
     SELECT count(*) FROM pg_catalog.pg_prepared_xacts;
     ```
   + Remove all uses of the *reg\** data types before attempting an upgrade\. Except for `regtype` and `regclass`, you can't upgrade the *reg\** data types\. The pg\_upgrade utility \(used by Amazon Aurora to do the upgrade\) can't persist this data type\. To learn more about this utility, see [pg\_upgrade](https://www.postgresql.org/docs/current/pgupgrade.html) in the PostgreSQL documentation\.

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
   + If you are upgrading an Aurora PostgreSQL version 10\.18 or higher DB cluster that has the `pgRouting` extension installed, drop the extension before upgrading to version 12\.4 or higher\.

     If you are upgrading an Aurora PostgreSQL 10\.x version that has the extension `pg_repack` version 1\.4\.3 installed, drop the extension before upgrading to any higher version\.

1. Check for template1 and template0 databases\.

   For a successful upgrade, template 1 and template 0 databases must exist and should be listed as a template\. To check on this, use the following command: 

   ```
   SELECT datname, datistemplate FROM pg_database; 
                           
   datname    | datistemplate
   -----------+---------------
   template0  | t
   rdsadmin   | f
   template1  | t
   postgres   | f
   ```

   In the command output, the `datistemplate` value for template1 and template0 databases should be `t`\.

1. Drop logical replication slots\. 

   The upgrade process can't proceed if the Aurora PostgreSQL DB cluster is using any logical replication slots\. Logical replication slots are typically used for short\-term data migration tasks, such as migrating data using AWS DMS or for replicating tables from the database to data lakes, BI tools, or other targets\. Before upgrading, make sure that you know the purpose of any logical replication slots that exist, and confirm that it's okay to delete them\. You can check for logical replication slots using the following query:

   ```
   SELECT * FROM pg_replication_slots;
   ```

   If logical replication slots are still being used, you shouldn't delete them, and you can't proceed with the upgrade\. However, if the logical replication slots aren't needed, you can delete them using the following SQL:

   ```
   SELECT pg_drop_replication_slot(slot_name);
   ```

   Logical replication scenarios that use the `pglogical` extension also need to have slots dropped from the publisher node for a successful major version upgrade on that node\. However, you can restart the replication process from the subscriber node after the upgrade\. For more information, see [Reestablishing logical replication after a major upgrade](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pglogical.recover-replication-after-upgrade)\.

1. Perform a backup\.

   The upgrade process creates a DB cluster snapshot of your DB cluster during upgrading\. If you also want to do a manual backup before the upgrade process, see [Creating a DB cluster snapshot](USER_CreateSnapshotCluster.md) for more information\.

1. Upgrade certain extensions to the latest available version before performing the major version upgrade\. The extensions to update include the following: 
   + `pgRouting`
   + `postgis_raster`
   + `postgis_tiger_geocoder`
   + `postgis_topology`
   + `address_standardizer`
   + `address_standardizer_data_us`

   Run the following command for each extension that's currently installed\.

   ```
   ALTER EXTENSION PostgreSQL-extension UPDATE TO 'new-version';
   ```

   For more information, see [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)\. To learn more about upgrading PostGIS, see [Step 6: Upgrade the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)\. 

1. If you're upgrading to version 11\.x, drop the extensions that it doesn't support before performing the major version upgrade\. The extensions to drop include:
   + `chkpass`
   + `tsearch2` 

1. Drop `unknown` data types, depending on your target version\.

   PostgreSQL version 10 doesn't support the `unknown` data type\. If a version 9\.6 database uses the `unknown` data type, an upgrade to version 10 shows an error message such as the following\. 

   ```
   Database instance is in a state that cannot be upgraded: PreUpgrade checks failed: 
   The instance could not be upgraded because the 'unknown' data type is used in user tables. 
   Please remove all usages of the 'unknown' data type and try again."
   ```

   To find the `unknown` data type in your database so that you can remove such columns or change them to supported data types, use the following SQL code for each database\.

   ```
   SELECT n.nspname, c.relname, a.attname
       FROM pg_catalog.pg_class c,
       pg_catalog.pg_namespace n,
       pg_catalog.pg_attribute a
       WHERE c.oid = a.attrelid AND NOT a.attisdropped AND
       a.atttypid = 'pg_catalog.unknown'::pg_catalog.regtype AND
       c.relkind IN ('r','m','c') AND
       c.relnamespace = n.oid AND
       n.nspname !~ '^pg_temp_' AND
       n.nspname !~ '^pg_toast_temp_' AND n.nspname NOT IN ('pg_catalog', 'information_schema');
   ```

1. Perform a dry\-run upgrade\.

   We highly recommend testing a major version upgrade on a duplicate of your production database before trying the upgrade on your production database\. You can monitor the execution plans on the duplicate test instance for any possible execution plan regressions and to evaluate its performance\. To create a duplicate test instance, you can either restore your database from a recent snapshot or clone your database\. For more information, see [Restoring from a snapshot](aurora-restore-snapshot.md#aurora-restore-snapshot.Restoring) or [Cloning a volume for an Amazon Aurora DB cluster](Aurora.Managing.Clone.md)\.

   For more information, see [Upgrading the Aurora PostgreSQL engine to a new major version](#USER_UpgradeDBInstance.Upgrading.Manual)\. 

1. Upgrade your production instance\.

   When your dry\-run major version upgrade is successful, you should be able to upgrade your production database with confidence\. For more information, see [Upgrading the Aurora PostgreSQL engine to a new major version](#USER_UpgradeDBInstance.Upgrading.Manual)\. 

   
**Note**  
During the upgrade process, Aurora PostgreSQL takes a DB cluster snapshot if the cluster's backup retention period is greater than 0\. You can't do a point\-in\-time restore of your cluster during this process\. Later, you can perform a point\-in\-time restore to times before the upgrade began and after the automatic snapshot of your instance has completed\. However, you can't perform a point\-in\-time restore to a previous minor version\. 

   For information about an upgrade in progress, you can use Amazon RDS to view two logs that the pg\_upgrade utility produces\. These are `pg_upgrade_internal.log` and `pg_upgrade_server.log`\. Amazon Aurora appends a timestamp to the file name for these logs\. You can view these logs as you can any other log\. For more information, see [Monitoring Amazon Aurora log files](USER_LogAccess.md)\.

1. Upgrade PostgreSQL extensions\. The PostgreSQL upgrade process doesn't upgrade any PostgreSQL extensions\. For more information, see [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)\.

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
+ We recommend that you test your application on the upgraded database with a similar workload to verify that everything works as expected\. After the upgrade is verified, you can delete this test instance\.

### Upgrading the Aurora PostgreSQL engine to a new major version<a name="USER_UpgradeDBInstance.Upgrading.Manual"></a>

When you initiate the upgrade process to a new major version, Aurora PostgreSQL takes a snapshot of the Aurora DB cluster before it makes any changes to your cluster\. This snapshot is created for major version upgrades only, not minor version upgrades\. When the upgrade process completes, you can find this snapshot among the manual snapshots listed under **Snapshots** in the RDS console\. The snapshot name includes `preupgrade` as its prefix, the name of your Aurora PostgreSQL DB cluster, the source version, the target version, and the date and timestamp, as shown in the following example\. 

```
preupgrade-docs-lab-apg-global-db-12-8-to-13-6-2022-05-19-00-19
```

After the upgrade completes, you can use the snapshot that Aurora created and stored in your manual snapshot list to restore the DB cluster to its previous version, if necessary\. 

**Tip**  
In general, snapshots provide many ways to restore your Aurora DB cluster to various points in time\. To learn more, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md) and [Restoring a DB cluster to a specified time](aurora-pitr.md)\. However, Aurora PostgreSQL doesn't support using a snapshot to restore to a previous minor version\. 

During the major version upgrade process, Aurora allocates a volume and clones the source Aurora PostgreSQL DB cluster\. If the upgrade fails for any reason, Aurora PostgreSQL uses the clone to roll back the upgrade\. After more than 15 clones of a source volume are allocated, subsequent clones become full copies and take longer\. This can cause the upgrade process also to take longer\. If Aurora PostgreSQL rolls back the upgrade, be aware of the following:
+ You might see billing entries and metrics for both the original volume and the cloned volume allocated during the upgrade\. Aurora PostgreSQL cleans up the extra volume after the cluster backup retention window is beyond the time of the upgrade\. 
+ The next cross\-Region snapshot copy from this cluster will be a full copy instead of an incremental copy\.

To safely upgrade the DB instances that make up your cluster, Aurora PostgreSQL uses the pg\_upgrade utility\. After the writer upgrade completes, each reader instance experiences a brief outage while it's upgraded to the new major version\. To learn more about this PostgreSQL utility, see [pg\_upgrade](https://www.postgresql.org/docs/current/pgupgrade.html) in the PostgreSQL documentation\. 

You can upgrade your Aurora PostgreSQL DB cluster to a new version by using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="USER_UpgradeDBInstance.Upgrading.Manual.Console"></a>

**To upgrade the engine version of a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster that you want to upgrade\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. For **Engine version**, choose the new version\.

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify Cluster** to save your changes\. 

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

#### AWS CLI<a name="USER_UpgradeDBInstance.Upgrading.Manual.CLI"></a>

To upgrade the engine version of a DB cluster, use the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\. Specify the following parameters: 
+ `--db-cluster-identifier` – The name of the DB cluster\. 
+ `--engine-version` – The version number of the database engine to upgrade to\. For information about valid engine versions, use the AWS CLI [ describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.
+ `--allow-major-version-upgrade` – A required flag when the `--engine-version` parameter is a different major version than the DB cluster's current major version\.
+ `--no-apply-immediately` – Apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. 

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

#### RDS API<a name="USER_UpgradeDBInstance.Upgrading.Manual.API"></a>

To upgrade the engine version of a DB cluster, use the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation\. Specify the following parameters: 
+ `DBClusterIdentifier` – The name of the DB cluster, for example *`mydbcluster`*\. 
+ `EngineVersion` – The version number of the database engine to upgrade to\. For information about valid engine versions, use the [ DescribeDBEngineVersions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBEngineVersions.html) operation\.
+ `AllowMajorVersionUpgrade` – A required flag when the `EngineVersion` parameter is a different major version than the DB cluster's current major version\.
+ `ApplyImmediately` – Whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. 

#### Major upgrades for global databases<a name="USER_UpgradeDBInstance.PostgreSQL.GlobalDB"></a>

For an Aurora global database cluster, the upgrade process upgrades all DB clusters that make up your Aurora global database at the same time\. It does so to ensure that each runs the same Aurora PostgreSQL version\. It also ensures that any changes to system tables, data file formats, and so on, are automatically replicated to all secondary clusters\.

To upgrade a global database cluster to a new major version of Aurora PostgreSQL, we recommend that you test your applications on the upgraded version, as detailed in [Testing an upgrade of your production DB cluster to a new major version](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary)\. Be sure to prepare your DB cluster parameter group and DB parameter group settings for each AWS Region in your Aurora global database before the upgrade as detailed in [step 1.](#step-1) of [Testing an upgrade of your production DB cluster to a new major version](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary)\. 

If your Aurora PostgreSQL global database cluster has a recovery point objective \(RPO\) set for its `rds.global_db_rpo` parameter, make sure to reset the parameter before upgrading\. The major version upgrade process doesn't work if the RPO is turned on\. By default, this parameter is turned off\. For more information about Aurora PostgreSQL global databases and RPO, see [Managing RPOs for Aurora PostgreSQL–based global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-manage-recovery)\.

If you verify that your applications can run as expected on the trial deployment of the new version, you can start the upgrade process\. To do so, see [Upgrading the Aurora PostgreSQL engine to a new major version](#USER_UpgradeDBInstance.Upgrading.Manual)\. Be sure to choose the top\-level item from the **Databases** list in the RDS console, **Global database**, as shown in the following image\.

![\[Console image showing an Aurora global database, an Aurora Serverless DB cluster, and another Aurora PostgreSQL DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-database-plus-other.png)

As with any modification, you can confirm that you want the process to proceed when prompted\.

![\[Console image showing prompt to confirm the upgrade process for an Aurora PostgreSQL DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-apg-upgrade-2.png)

Rather than using the console, you can start the upgrade process by using the AWS CLI or the RDS API\. As with the console, you operate on the Aurora global database cluster rather than any of its constituents, as follows:
+ Use the [modify\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-global-cluster.html) AWS CLI command to start the upgrade for your Aurora global database by using the AWS CLI\. 
+ Use the [ModifyGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyGlobalCluster.html) API to start the upgrade\. 

## How to perform minor version upgrades and apply patches<a name="USER_UpgradeDBInstance.PostgreSQL.Minor"></a>

Minor version upgrades and patches become available in AWS Regions only after rigorous testing\. Before releasing upgrades and patches, Aurora PostgreSQL tests to ensure that known security issues, bugs, and other issues that emerge after the release of the minor community version don't disrupt overall Aurora PostgreSQL fleet stability\. 

As Aurora PostgreSQL makes new minor versions available, the instances that make up your Aurora PostgreSQL DB cluster can be automatically upgraded during your specified maintenance window\. For this to happen, your Aurora PostgreSQL DB cluster must have the **Enable auto minor version upgrade** option turned on\. All DB instances that make up your Aurora PostgreSQL DB cluster must have the automatic minor version upgrade \(AMVU\) option turned on so that the minor upgrade to be applied throughout the cluster\. 

**Tip**  
Make sure that the **Enable auto minor version upgrade** option is turned on for all PostgreSQL DB instances that make up your Aurora PostgreSQL DB cluster\. This option must be turned on for every instance in the DB cluster to work\. For information on how to set **Auto minor version upgrade**, and how the setting works when applied at the cluster and instance levels, see [ Automatic minor version upgrades for Aurora DB clusters](USER_UpgradeDBInstance.Maintenance.md#Aurora.Maintenance.AMVU)\.

You can check the value of the **Enable auto minor version upgrade** option for all your Aurora PostgreSQL DB clusters by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command with the following query\.

```
aws rds describe-db-instances \
  --query '*[].{DBClusterIdentifier:DBClusterIdentifier,DBInstanceIdentifier:DBInstanceIdentifier,AutoMinorVersionUpgrade:AutoMinorVersionUpgrade}'
```

This query returns a list of all Aurora DB clusters and their instances with a `true` or `false` value for the status of the `AutoMinorVersionUpgrade` setting\. The command as shown assumes that you have your AWS CLI configured to target your default AWS Region\. 

For more information about the AMVU option and how to modify your Aurora DB cluster to use it, see [ Automatic minor version upgrades for Aurora DB clusters](USER_UpgradeDBInstance.Maintenance.md#Aurora.Maintenance.AMVU)\. 

You can upgrade your Aurora PostgreSQL DB clusters to new minor versions either by responding to maintenance tasks, or by modifying the cluster to use the new version\. 

You can identify any available upgrades or patches for your Aurora PostgreSQL DB clusters by using the RDS console and opening the **Recommendations** menu\. There, you can find a list of various maintenance issues such as **Old minor versions**\. Depending on your production environment, you can choose to **Schedule** the upgrade or take immediate action, by choosing **Apply now**, as shown following\.

![\[Console image showing Recommendation to upgrade to a newer minor version.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-maintenance-upgrade-minor.png)

To learn more about how to maintain an Aurora DB cluster, including how to manually apply patches and minor version upgrades, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

### Minor release upgrades and zero\-downtime patching<a name="USER_UpgradeDBInstance.PostgreSQL.Minor.zdp"></a>

Upgrading an Aurora PostgreSQL DB cluster involves the possibility of an outage\. During the upgrade process, the database is shut down as it's being upgraded\. If you start the upgrade while the database is busy, you lose all connections and transactions that the DB cluster is processing\. If you wait until the database is idle to perform the upgrade, you might have to wait a long time\.

The zero\-downtime patching \(ZDP\) feature improves the upgrading process\. With ZDP, both minor version upgrades and patches can be applied with minimal impact to your Aurora PostgreSQL DB cluster\. ZDP is used when applying patches or newer minor version upgrades to Aurora PostgreSQL versions 14\.3\.3, 13\.7\.3, 12\.11\.3, 11\.16\.3, 10\.21\.3 and other higher releases of these minor versions and newer major versions\. That is, upgrading to new minor versions from any of these releases onward uses ZDP\. 

**Note**  
ZDP isn't supported for Aurora PostgreSQL DB clusters that are configured as Aurora Serverless v1, or Aurora global databases\. ZDP isn't supported during OS patches and OS upgrades\.

ZDP works by preserving current client connections to your Aurora PostgreSQL DB cluster throughout the Aurora PostgreSQL upgrade process\. However, in the following cases, connections will be dropped for ZDP to complete:
+ Long running query or transactions are in progress\.
+ Data definition language \(DDL\) statements are running\.
+ Temporary tables or table locks are in use\.
+ All sessions are listening on notification channels\.
+ A cursor in ‘WITH HOLD’ status is in use\.
+ TLSv1\.3 or TLSv1\.1 connections are in use\.

When ZDP completes successfully, application sessions are preserved except sessions of dropped connections, and the database engine restarts while the upgrade is still under way\. Although the database engine restart can cause a drop in throughput, that typically lasts only for a few seconds or at most, approximately one minute\. 

In some cases, zero\-downtime patching \(ZDP\) might not succeed\. For example, parameter changes that are in a `pending` state on your Aurora PostgreSQL DB cluster or its instances interfere with ZDP\.

You can find metrics and events for ZDP operations in **Events** page in the console\. The events include the start of the ZDP upgrade and completion of the upgrade\. In this event you can find how long the process took, and the numbers of preserved and dropped connections that occurred during the restart\. You can find details in your database error log\. 

### Upgrading the Aurora PostgreSQL engine to a new minor version<a name="USER_UpgradeDBInstance.MinorUpgrade"></a>

 You can upgrade your Aurora PostgreSQL DB cluster to a new minor version by using the console, the AWS CLI, or the RDS API\. Before performing the upgrade, we recommend that you follow the same best practice that we recommend for major version upgrades\. As with new major versions, new minor versions can also have optimizer improvements, such as fixes, that can cause query plan regressions\. To ensure plan stability, we recommend that you use the Query Plan Management \(QPM\) extension as detailed in [Ensuring plan stability after a major version upgrade](AuroraPostgreSQL.Optimize.BestPractice.md#AuroraPostgreSQL.Optimize.BestPractice.MajorVersionUpgrade)\. 

#### Console<a name="USER_UpgradeDBInstance.MinorUpgrade.Console"></a>

**To upgrade the engine version of your Aurora PostgreSQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster that you want to upgrade\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. For **Engine version**, choose the new version\.

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify Cluster** to save your changes\. 

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

#### AWS CLI<a name="USER_UpgradeDBInstance.MinorUpgrade.CLI"></a>

To upgrade the engine version of a DB cluster, use the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command with the following parameters:
+ `--db-cluster-identifier` – The name of your Aurora PostgreSQL DB cluster\. 
+ `--engine-version` – The version number of the database engine to upgrade to\. For information about valid engine versions, use the AWS CLI [ describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.
+ `--no-apply-immediately` – Apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately` instead\. 

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier mydbcluster \
    --engine-version new_version \
    --no-apply-immediately
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier mydbcluster ^
    --engine-version new_version ^
    --no-apply-immediately
```

#### RDS API<a name="USER_UpgradeDBInstance.MinorUpgrade.API"></a>

To upgrade the engine version of a DB cluster, use the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation\. Specify the following parameters: 
+ `DBClusterIdentifier` – The name of the DB cluster, for example *`mydbcluster`*\. 
+ `EngineVersion` – The version number of the database engine to upgrade to\. For information about valid engine versions, use the [ DescribeDBEngineVersions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBEngineVersions.html) operation\.
+ `ApplyImmediately` – Whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. 

## Upgrading PostgreSQL extensions<a name="USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades"></a>

Upgrading your Aurora PostgreSQL DB cluster to a new major or minor version doesn't upgrade the PostgreSQL extensions at the same time\. For most extensions, you upgrade the extension after the major or minor version upgrade completes\. However, in some cases, you upgrade the extension before you upgrade the Aurora PostgreSQL DB engine\. For more information, see the [list of extensions to update](#upgrade-extensions) in [Testing an upgrade of your production DB cluster to a new major version](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Upgrade.preliminary)\.

Installing PostgreSQL extensions requires `rds_superuser` privileges\. Typically, an `rds_superuser` delegates permissions over specific extensions to relevant users \(roles\), to facilitate the management of a given extension\. That means that the task of upgrading all the extensions in your Aurora PostgreSQL DB cluster might involve many different users \(roles\)\. Keep this in mind especially if you want to automate the upgrade process by using scripts\. For more information about PostgreSQL privileges and roles, see [Security with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Security.md)\. 

**Note**  
For information about upgrading the PostGIS extension version, see [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md) \( [Step 6: Upgrade the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)\)\. 

To update an extension after an engine upgrade, use the `ALTER EXTENSION UPDATE` command\.

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