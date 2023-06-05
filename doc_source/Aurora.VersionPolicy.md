# Amazon Aurora versions<a name="Aurora.VersionPolicy"></a>

Amazon Aurora reuses code and maintains compatibility with the underlying MySQL and PostgreSQL DB engines\. However, Aurora has its own version numbers, release cycle, time line for version deprecation, and so on\. The following section explains the common points and differences\. This information can help you to decide such things as which version to choose and how to verify which features and fixes are available in each version\. It can also help you to decide how often to upgrade and how to plan your upgrade process\. 

**Topics**
+ [Relational databases that are available on Aurora](#Aurora.VersionPolicy.Engines)
+ [Differences in version numbers between community databases and Aurora](#Aurora.VersionPolicy.VersionNumberMapping)
+ [Amazon Aurora major versions](#Aurora.VersionPolicy.MajorVersions)
+ [Amazon Aurora minor versions](#Aurora.VersionPolicy.MinorVersions)
+ [Amazon Aurora patch versions](#Aurora.VersionPolicy.PatchVersions)
+ [Learning what's new in each Amazon Aurora version](#Aurora.VersionPolicy.ReleaseNotes)
+ [Specifying the Amazon Aurora database version for your database cluster](#Aurora.VersionPolicy.ChoosingVersion)
+ [Default Amazon Aurora versions](#Aurora.VersionPolicy.DefaultVersions)
+ [Automatic minor version upgrades](#Aurora.VersionPolicy.AMVU)
+ [How long Amazon Aurora major versions remain available](#Aurora.VersionPolicy.MajorVersionLifetime)
+ [How often Amazon Aurora minor versions are released](#Aurora.VersionPolicy.MinorVersionCadence)
+ [How long Amazon Aurora minor versions remain available](#Aurora.VersionPolicy.MinorVersionLifetime)
+ [Long\-term support for selected Amazon Aurora minor versions](#Aurora.VersionPolicy.LTS)
+ [Manually controlling if and when your database cluster is upgraded to new versions](#Aurora.VersionPolicy.ManualUpgrades)
+ [Required Amazon Aurora upgrades](#Aurora.VersionPolicy.RequiredUpgrades)
+ [Testing your DB cluster with a new Aurora version before upgrading](#Aurora.VersionPolicy.PreupgradeTesting)

## Relational databases that are available on Aurora<a name="Aurora.VersionPolicy.Engines"></a>

 The following relational databases are available on Aurora: 
+  Amazon Aurora MySQL\-Compatible Edition\. For usage information, see [Working with Amazon Aurora MySQL](Aurora.AuroraMySQL.md)\. For a detailed list of available versions, see [Database engine updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md)\. 
+  Amazon Aurora PostgreSQL\-Compatible Edition\. For usage information, see [Working with Amazon Aurora PostgreSQL](Aurora.AuroraPostgreSQL.md)\. For a detailed list of available versions, see [Amazon Aurora PostgreSQL updates](AuroraPostgreSQL.Updates.md)\. 

## Differences in version numbers between community databases and Aurora<a name="Aurora.VersionPolicy.VersionNumberMapping"></a>

 Each Amazon Aurora version is compatible with a specific community database version of either MySQL or PostgreSQL\. You can find the community version of your database using the `version` function and the Aurora version using the `aurora_version` function\. 

 Examples for Aurora MySQL and Aurora PostgreSQL are shown following\. 

```
mysql> select version();
+------------------+
|   version()      |
+------------------+
|  5.7.12          | 
+------------------+

mysql> select aurora_version(), @@aurora_version;
+------------------+------------------+
| aurora_version() | @@aurora_version |
+------------------+------------------+
| 2.08.1           | 2.08.1           |
+------------------+------------------+
```

```
postgres=> select version();
-----------------------------------------------------------------------------
PostgreSQL 11.7 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 4.9.3, 64-bit
(1 row)

postgres=> select aurora_version();
aurora_version
----------------
3.2.2
```

 For more information, see [Checking Aurora MySQL versions using SQL](AuroraMySQL.Updates.Versions.md#AuroraMySQL.Updates.DBVersions) and [Identifying versions of Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.Versions.md)\. 

## Amazon Aurora major versions<a name="Aurora.VersionPolicy.MajorVersions"></a>

 Aurora versions use the `major.minor.patch` scheme\. An *Aurora major version* refers to the MySQL or PostgreSQL community major version that Aurora is compatible with\. The following example shows the mapping between community MySQL and PostgreSQL versions and the corresponding Aurora versions\. 


| Community major version | Aurora major version | 
| --- | --- | 
| MySQL 5\.6 \(deprecated\) | Aurora MySQL 1 \(deprecated\) | 
| MySQL 5\.7 | Aurora MySQL 2 | 
| MySQL 8\.0 | Aurora MySQL 3 | 
| PostgreSQL 9\.6 \(deprecated\) | Aurora PostgreSQL 1 \(deprecated\) | 
| PostgreSQL 10 \(deprecated\) | Aurora PostgreSQL 2 \(deprecated\)\. Applies to PostgreSQL 10\.17 and older versions only\. For version 10\.18 and higher versions, the Aurora version is the same as the major\.minor version of the PostgreSQL community version, with a third digit in the patch location\. | 
| PostgreSQL 11 | Aurora PostgreSQL 3\. Applies to PostgreSQL 11\.12 and older versions only\. For version 11\.13 and higher versions, the Aurora version is the same as the major\.minor version of the PostgreSQL community version, with a third digit in the patch location\. | 
| PostgreSQL 12 | Aurora PostgreSQL 4\. Applies to PostgreSQL 12\.7 and older versions only\. For version 12\.8 and higher versions, the Aurora version is the same as the major\.minor version of the PostgreSQL community version, with a third digit in the patch location\. | 
| PostgreSQL 13 | Aurora PostgreSQL 13\. For version 13\.3 and higher versions, the Aurora version is the same as the major\.minor version of the PostgreSQL community version, with a third digit in the patch location when patches to Aurora are released\. | 
| PostgreSQL 14 | Aurora PostgreSQL 14\.3 and higher\. The Aurora version is the same as the major\.minor version of the PostgreSQL community version, with a third digit in the patch location when patches to Aurora are released\. | 
| PostgreSQL 15 | Aurora PostgreSQL 15\.2 and higher\. The Aurora version is the same as the major\.minor version of the PostgreSQL community version, with a third digit in the patch location when patches to Aurora are released\. | 

## Amazon Aurora minor versions<a name="Aurora.VersionPolicy.MinorVersions"></a>

 Aurora versions use the `major.minor.patch` scheme\. An *Aurora minor version* provides incremental community and Aurora\-specific improvements to the service, for example new features and fixes\. 

 Aurora minor versions are always mapped to a specific community version\. However, some community versions might not have an Aurora equivalent\. 

## Amazon Aurora patch versions<a name="Aurora.VersionPolicy.PatchVersions"></a>

 Aurora versions use the `major.minor.patch` scheme\. An Aurora patch version includes important fixes added to a minor version after its initial release \(for example, Aurora MySQL 2\.10\.0, 2\.10\.1, \.\.\., 2\.10\.3\)\. While each new minor version provides new Aurora features, new patch versions within a specific minor version are primarily used to resolve important issues\. 

 For more information on patching, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Learning what's new in each Amazon Aurora version<a name="Aurora.VersionPolicy.ReleaseNotes"></a>

 Each new Aurora version comes with release notes that list the new features, fixes, other enhancements, and so on that apply to each version\. 

 For Aurora MySQL release notes, see [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html)\. For Aurora PostgreSQL release notes, see [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\. 

## Specifying the Amazon Aurora database version for your database cluster<a name="Aurora.VersionPolicy.ChoosingVersion"></a>

 You can specify any currently available version \(major and minor\) when creating a new DB cluster using the **Create database** operation in the AWS Management Console, the AWS CLI, or the `CreateDBCluster` API operation\. Not every Aurora database version is available in every AWS Region\. 

 To learn how to create Aurora clusters, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. To learn how to change the version of an existing Aurora cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\. 

## Default Amazon Aurora versions<a name="Aurora.VersionPolicy.DefaultVersions"></a>

 When a new Aurora minor version contains significant improvements compared to a previous one, it's marked as the default version for new DB clusters\. Typically, we release two default versions for each major version per year\. 

 We recommend that you keep your DB cluster upgraded to the most current default minor version, because that version contains the latest security and functionality fixes\. 

## Automatic minor version upgrades<a name="Aurora.VersionPolicy.AMVU"></a>

You can stay up to date with Aurora minor versions by turning on **Auto minor version upgrade** for every DB instance in the Aurora cluster\. Aurora only performs the automatic upgrade if all DB instances in your cluster have this setting turned on\. Auto minor version upgrades are performed to the default minor version\.

We typically schedule automatic upgrades twice a year for DB clusters that have the **Auto minor version upgrade** setting set to `Yes`\. These upgrades are started during the maintenance window that you specify for your cluster\. For more information, see [Enabling automatic upgrades between minor Aurora MySQL versions](AuroraMySQL.Updates.Patching.md#AuroraMySQL.Updates.AMVU) and [How to perform minor version upgrades and apply patches](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.Minor)\.

Automatic minor version upgrades are communicated in advance through an Amazon RDS DB cluster event with a category of `maintenance` and ID of `RDS-EVENT-0156`\. For more information, see [Amazon RDS event categories and event messages](USER_Events.Messages.md)\.

## How long Amazon Aurora major versions remain available<a name="Aurora.VersionPolicy.MajorVersionLifetime"></a>

Amazon Aurora major versions remain available at least until community end of life for the corresponding community version\. You can use the following dates to plan your testing and upgrade cycles\. These dates represent the earliest date that an upgrade to a newer version might be required\. If Amazon extends support for an Aurora version for longer than originally stated, we plan to update this table to reflect the later date\. 

<a name="aurora-version-minimum-eol"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.VersionPolicy.html)

Before we ask that you upgrade to a newer major version and to help you plan, we provide a reminder at least 12 months in advance\. We do so to communicate the detailed upgrade process\. Details include the timing of certain milestones, the impact on your DB clusters, and the actions that we recommend that you take\. We always recommend that you thoroughly test your applications with new database versions before performing a major version upgrade\.

After this 12\-month period, an automatic upgrade to the subsequent major version might be applied to any database cluster still running the older version\. If so, the upgrade is started during scheduled maintenance windows\.

## How often Amazon Aurora minor versions are released<a name="Aurora.VersionPolicy.MinorVersionCadence"></a>

In general, Amazon Aurora minor versions are released quarterly\. The release schedule might vary to pick up additional features or fixes\.

## How long Amazon Aurora minor versions remain available<a name="Aurora.VersionPolicy.MinorVersionLifetime"></a>

We intend to make each Amazon Aurora minor version of a particular major version available for at least 12 months\. At the end of this period, Aurora might apply an auto minor version upgrade to the subsequent default minor version\. Such an upgrade is started during the scheduled maintenance window for any cluster that is still running the older minor version\.

We might replace a minor version of a particular major version sooner than the usual 12\-month period if there are critical matters such as security issues, or if the major version has reached end of life\.

Before beginning automatic upgrades of minor versions that are approaching end of life, we generally provide a reminder three months in advance\. We do so to communicate the detailed upgrade process\. Details include the timing of certain milestones, the impact on your DB clusters, and the actions that we recommend that you take\. Notifications with less than three months notice are used when there are critical matters, such as security issues, that necessitate quicker action\.

If you don't have the **Auto minor version upgrade** setting enabled, you get a reminder but no RDS event notification\. Upgrades occur within a maintenance window after the mandatory upgrade deadline has passed\.

If you do have the **Auto minor version upgrade** setting enabled, you get a reminder and an Amazon RDS DB cluster event with a category of `maintenance` and ID of `RDS-EVENT-0156`\. Upgrades occur during the next maintenance window\.

For more information on auto minor version upgrades, see [ Automatic minor version upgrades for Aurora DB clusters](USER_UpgradeDBInstance.Maintenance.md#Aurora.Maintenance.AMVU)\.

## Long\-term support for selected Amazon Aurora minor versions<a name="Aurora.VersionPolicy.LTS"></a>

For each Aurora major version, certain minor versions are designated as long\-term\-support \(LTS\) versions and made available for at least three years\. That is, at least one minor version per major version is made available for longer than the typical 12 months\. We generally provide a reminder six months before the end of this period\. We do so to communicate the detailed upgrade process\. Details include the timing of certain milestones, the impact on your DB clusters, and the actions that we recommend that you take\. Notifications with less than six months notice are used when there are critical matters, such as security issues, that necessitate quicker action\.

LTS minor versions include only critical fixes \(through patch versions\)\. An LTS version doesn't include new features released after its introduction\. Once a year, DB clusters running on an LTS minor version are patched to the latest patch version of the LTS release\. We do this patching to help ensure that you benefit from cumulative security and stability fixes\. We might patch an LTS minor version more frequently if there are critical fixes, such as for security, that need to be applied\.

**Note**  
If you want to remain on an LTS minor version for the duration of its lifecycle, make sure to turn off **Auto minor version upgrade** for your DB instances\. To avoid automatically upgrading your DB cluster from the LTS minor version, set **Auto minor version upgrade** to `No` on any DB instance in your Aurora cluster\.

For the version numbers of all Aurora LTS versions, see [Aurora MySQL long\-term support \(LTS\) releases](AuroraMySQL.Updates.Versions.md#AuroraMySQL.Updates.LTS) and [Aurora PostgreSQL long\-term support \(LTS\) releases](AuroraPostgreSQL.Updates.LTS.md)\.

## Manually controlling if and when your database cluster is upgraded to new versions<a name="Aurora.VersionPolicy.ManualUpgrades"></a>

Auto minor version upgrades are performed to the default minor version\. We typically schedule automatic upgrades twice a year for DB clusters that have the **Auto minor version upgrade** setting set to `Yes`\. These upgrades are started during customer\-specified maintenance windows\. If you want to turn off automatic minor version upgrades, set **Auto minor version upgrade** to `No` on any DB instance within an Aurora cluster\. Aurora performs an automatic minor version upgrade only if all DB instances in your cluster have the setting turned on\.

Because major version upgrades involve some compatibility risk, they don't occur automatically\. You must initiate these, except in the case of a major version deprecation, as explained earlier\. We always recommend that you thoroughly test your applications with new database versions before performing a major version upgrade\.

For more information about upgrading a DB cluster to a new Aurora major version, see [Upgrading Amazon Aurora MySQL DB clusters](AuroraMySQL.Updates.Upgrading.md) and [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

## Required Amazon Aurora upgrades<a name="Aurora.VersionPolicy.RequiredUpgrades"></a>

For certain critical fixes, we might perform a managed upgrade to a newer patch level within the same minor version\. These required upgrades happen even if **Auto minor version upgrade** is turned off\. Before doing so, we communicate the detailed upgrade process\. Details include the timing of certain milestones, the impact on your DB clusters, and the actions that we recommend that you take\. Such managed upgrades are performed automatically\. Each such upgrade is started within the cluster maintenance window\.

## Testing your DB cluster with a new Aurora version before upgrading<a name="Aurora.VersionPolicy.PreupgradeTesting"></a>

You can test the upgrade process and how the new version works with your application and workload\. Use one of the following methods:
+ Clone your cluster using the Amazon Aurora fast database clone feature\. Perform the upgrade and any post\-upgrade testing on the new cluster\.
+ Restore from a cluster snapshot to create a new Aurora cluster\. You can create a cluster snapshot yourself from an existing Aurora cluster\. Aurora also automatically creates periodic snapshots for you for each of your clusters\. You can then initiate a version upgrade for the new cluster\. You can experiment on the upgraded copy of your cluster before deciding whether to upgrade your original cluster\.

For more information on these ways to create new clusters for testing, see [Cloning a volume for an Amazon Aurora DB cluster](Aurora.Managing.Clone.md) and [Creating a DB cluster snapshot](USER_CreateSnapshotCluster.md)\.