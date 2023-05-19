# Aurora MySQL version numbers and special versions<a name="AuroraMySQL.Updates.Versions"></a>

 Although Aurora MySQL\-Compatible Edition is compatible with the MySQL database engines, Aurora MySQL includes features and bug fixes that are specific to particular Aurora MySQL versions\. Application developers can check the Aurora MySQL version in their applications by using SQL\. Database administrators can check and specify Aurora MySQL versions when creating or upgrading Aurora MySQL DB clusters and DB instances\. 

**Topics**
+ [Checking or specifying Aurora MySQL engine versions through AWS](#AuroraMySQL.Updates.EngineVersions)
+ [Checking Aurora MySQL versions using SQL](#AuroraMySQL.Updates.DBVersions)
+ [Aurora MySQL long\-term support \(LTS\) releases](#AuroraMySQL.Updates.LTS)

## Checking or specifying Aurora MySQL engine versions through AWS<a name="AuroraMySQL.Updates.EngineVersions"></a>

 When you perform administrative tasks using the AWS Management Console, AWS CLI, or RDS API, you specify the Aurora MySQL version in a descriptive alphanumeric format\. 

 Starting with Aurora MySQL version 2, Aurora engine versions have the following syntax\. 

```
mysql-major-version.mysql_aurora.aurora-mysql-version
```

 The `mysql-major-version-` portion is `5.7` or `8.0`\. This value represents the version of the client protocol and general level of MySQL feature support for the corresponding Aurora MySQL version\. 

 The `aurora-mysql-version` is a dotted value with three parts: the Aurora MySQL major version, the Aurora MySQL minor version, and the patch level\. The major version is `2` or `3`\. Those values represent Aurora MySQL compatible with MySQL 5\.7 or 8\.0, respectively\. The minor version represents the feature release within the 2\.x or 3\.x series\. The patch level begins at `0` for each minor version, and represents the set of subsequent bug fixes that apply to the minor version\. Occasionally, a new feature is incorporated into a minor version but not made visible immediately\. In these cases, the feature undergoes fine\-tuning and is made public in a later patch level\. 

All 2\.x Aurora MySQL engine versions are wire\-compatible with Community MySQL 5\.7\.12\. All 3\.x Aurora MySQL engine versions are wire\-compatible with MySQL 8\.0\.23 onwards\. You can refer to release notes of the specific 3\.x version to find the corresponding MySQL compatible version\.

 For example, the engine versions for Aurora MySQL 3\.02\.0 and 2\.03\.2 are the following\. 

```
8.0.mysql_aurora.3.02.0
5.7.mysql_aurora.2.03.2
```

**Note**  
There isn't a one\-to\-one correspondence between community MySQL versions and the Aurora MySQL 2\.x versions\. For Aurora MySQL version 3, there is a more direct mapping\. To check which bug fixes and new features are in a particular Aurora MySQL release, see [ Database engine updates for Amazon Aurora MySQL version 3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.30Updates.html) and [ Database engine updates for Amazon Aurora MySQL version 2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.20Updates.html) in the *Release Notes for Aurora MySQL*\. For a chronological list of new features and releases, see [Document history](WhatsNew.md)\. To check the minimum version required for a security\-related fix, see [ Security vulnerabilities fixed in Aurora MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.CVE_list.html)in the *Release Notes for Aurora MySQL*\.

You specify the Aurora MySQL engine version in some AWS CLI commands and RDS API operations\. For example, you specify the `--engine-version` option when you run the AWS CLI commands [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. You specify the `EngineVersion` parameter when you run the RDS API operations [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\.

In Aurora MySQL version 2 and higher, the engine version in the AWS Management Console also includes the Aurora version\. Upgrading the cluster changes the displayed value\. This change helps you to specify and check the precise Aurora MySQL versions, without the need to connect to the cluster or run any SQL commands\.

**Tip**  
For Aurora clusters managed through AWS CloudFormation, this change in the `EngineVersion` setting can trigger actions by AWS CloudFormation\. For information about how AWS CloudFormation treats changes to the `EngineVersion` setting, see [the AWS CloudFormation documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbcluster.html)\.

## Checking Aurora MySQL versions using SQL<a name="AuroraMySQL.Updates.DBVersions"></a>

 The Aurora version numbers that you can retrieve in your application using SQL queries use the format `<major version>.<minor version>.<patch version>`\. You can get this version number for any DB instance in your Aurora MySQL cluster by querying the `AURORA_VERSION` system variable\. To get this version number, use one of the following queries\. 

```
select aurora_version();
select @@aurora_version;
```

 Those queries produce output similar to the following\. 

```
mysql> select aurora_version(), @@aurora_version;
+------------------+------------------+
| aurora_version() | @@aurora_version |
+------------------+------------------+
| 2.11.1           | 2.11.1           |
+------------------+------------------+
```

 The version numbers that the console, CLI, and RDS API return by using the techniques described in [Checking or specifying Aurora MySQL engine versions through AWS](#AuroraMySQL.Updates.EngineVersions) are typically more descriptive\.

## Aurora MySQL long\-term support \(LTS\) releases<a name="AuroraMySQL.Updates.LTS"></a>

 Each new Aurora MySQL version remains available for a certain amount of time for you to use when you create or upgrade a DB cluster\. After this period, you must upgrade any clusters that use that version\. You can manually upgrade your cluster before the support period ends, or Aurora can automatically upgrade it for you when its Aurora MySQL version is no longer supported\. 

 Aurora designates certain Aurora MySQL versions as long\-term support \(LTS\) releases\. DB clusters that use LTS releases can stay on the same version longer and undergo fewer upgrade cycles than clusters that use non\-LTS releases\. Aurora supports each LTS release for at least three years after that release becomes available\. When a DB cluster that's on an LTS release is required to upgrade, Aurora upgrades it to the next LTS release\. That way, the cluster doesn't need to be upgraded again for a long time\. 

 During the lifetime of an Aurora MySQL LTS release, new patch levels introduce fixes to important issues\. The patch levels don't include any new features\. You can choose whether to apply such patches to DB clusters running the LTS release\. For certain critical fixes, Amazon might perform a managed upgrade to a patch level within the same LTS release\. Such managed upgrades are performed automatically within the cluster maintenance window\. 

 We recommend that you upgrade to the latest release, instead of using the LTS release, for most of your Aurora MySQL clusters\. Doing so takes advantage of Aurora as a managed service and gives you access to the latest features and bug fixes\. The LTS releases are intended for clusters with the following characteristics: 
+  You can't afford downtime on your Aurora MySQL application for upgrades outside of rare occurrences for critical patches\. 
+  The testing cycle for the cluster and associated applications takes a long time for each update to the Aurora MySQL database engine\. 
+  The database version for your Aurora MySQL cluster has all the DB engine features and bug fixes that your application needs\. 

The current LTS releases for Aurora MySQL are the following:
+ Aurora MySQL version 2\.07\.\*\. For more details about this version, see [ Aurora MySQL database engine updates 2023\-05\-04 \(version 2\.07\.9\)](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.2079.html) in the *Release Notes for Aurora MySQL*\.