# Aurora MySQL version numbers and special versions<a name="AuroraMySQL.Updates.Versions"></a>

 Although Aurora with MySQL compatibility is compatible with the MySQL database engines, Aurora MySQL includes features and bug fixes that are specific to particular Aurora MySQL versions\. Application developers can check the Aurora MySQL version in their applications by using SQL\. Database administrators can check and specify Aurora MySQL versions when creating or upgrading Aurora MySQL DB clusters and DB instances\. 

**Topics**
+ [Checking or specifying Aurora MySQL engine versions through AWS](#AuroraMySQL.Updates.EngineVersions)
+ [Checking Aurora MySQL versions using SQL](#AuroraMySQL.Updates.DBVersions)
+ [Aurora MySQL long\-term support \(LTS\) releases](#AuroraMySQL.Updates.LTS)
+ [Upgrade paths between 5\.6\-compatible and 5\.7\-compatible clusters](#AuroraMySQL.Updates.UpgradePaths)

## Checking or specifying Aurora MySQL engine versions through AWS<a name="AuroraMySQL.Updates.EngineVersions"></a>

 When you perform administrative tasks using the AWS Management Console, AWS CLI, or RDS API, you specify the Aurora MySQL version in a descriptive alphanumeric format\. 

 Starting with Aurora MySQL 2\.03\.2 and 1\.19\.0, Aurora engine versions have the following syntax\. 

```
mysql-major-version.mysql_aurora.aurora-mysql-version
```

 The `mysql-major-version-` portion is `5.6` or `5.7`\. This value represents the version of the client protocol and general level of MySQL feature support for the corresponding Aurora MySQL version\. 

 The `aurora-mysql-version` is a dotted value with three parts: the Aurora MySQL major version, the Aurora MySQL minor version, and the patch level\. The major version is `1` or `2`, representing Aurora MySQL compatible with MySQL 5\.6 or 5\.7 respectively\. The minor version represents the feature release within the 1\.x or 2\.x series\. The patch level begins at `0` for each minor version, and represents the set of subsequent bug fixes that apply to the minor version\. Occasionally, a new feature is incorporated into a minor version but not made visible immediately\. In these cases, the feature undergoes fine\-tuning and is made public in a later patch level\. 

 All 1\.x Aurora MySQL engine versions are wire\-compatible with Community MySQL 5\.6\.10a\. All 2\.x Aurora MySQL engine versions are wire\-compatible with Community MySQL 5\.7\.12\. 

 For example, the engine versions for Aurora MySQL 2\.03\.2 and 1\.19\.0 are the following\. 

```
5.7.mysql_aurora.2.03.2
5.6.mysql_aurora.1.19.0
```

**Note**  
 There isn't a one\-to\-one correspondence between community MySQL 5\.6 or 5\.7 versions and the Aurora MySQL 1\.x and 2\.x versions\. To check which bug fixes and new features are in a particular Aurora MySQL release, see [Database engine updates for Amazon Aurora MySQL 2\.0](AuroraMySQL.Updates.20Updates.md) and [Database engine updates for Amazon Aurora MySQL 1\.1](AuroraMySQL.Updates.11Updates.md)\. For a chronological list of new features and releases, see [Document history](WhatsNew.md)\. To check the minimum version required for a security\-related fix, see [Security vulnerabilities fixed in Amazon Aurora MySQL](AuroraMySQL.CVE_list.md)\. 

 For Aurora MySQL 2\.x, all versions 2\.03\.1 and lower are represented by the engine version `5.7.12`\. In the same way, all versions before 1\.19\.0 are represented by the engine version `5.6.10a`\. These older version designations don't include the `5.7.mysql_aurora` prefix\. When you specified `5.7.12` or `5.6.10a` while creating or modifying a cluster, you got the highest version before the 2\.03\.2 and 1\.19\.0 versions where the version numbering changed\. To determine the exact version number for those older versions, you used the SQL technique explained in [Checking Aurora MySQL versions using SQL](#AuroraMySQL.Updates.DBVersions)\. 

 You specify the Aurora MySQL engine version in some AWS CLI commands and RDS API operations\. For example, you specify the `--engine-version` option when you run the AWS CLI commands [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. You specify the `EngineVersion` parameter when you run the RDS API operations [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\. 

 In Aurora MySQL 1\.19\.0 and higher or 2\.03\.2 and higher, the engine version in the AWS Management Console also includes the Aurora version\. Upgrading the cluster changes the displayed value\. This change helps you to specify and check the precise Aurora MySQL versions, without the need to connect to the cluster or run any SQL commands\. 

**Tip**  
 For Aurora clusters managed through AWS CloudFormation, this change in the `EngineVersion` setting can trigger actions by AWS CloudFormation\. For information about how AWS CloudFormation treats changes to the `EngineVersion` setting, see [the AWS CloudFormation documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbcluster.html)\. 

 Before Aurora MySQL 1\.19\.0 and 2\.03\.2, the process to update the engine version is to use the **Apply a Pending Maintenance Action** option for the cluster\. This process doesn't change the Aurora MySQL engine version that the console displays\. For example, suppose that you see an Aurora MySQL cluster with a reported engine version of `5.6.10a` or `5.7.12`\. To find out the specific version, connect to the cluster and query the `AURORA_VERSION` system variable as described previously\. 

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
| 2.08.1           | 2.08.1           |
+------------------+------------------+
```

 The version numbers that the console, CLI, and RDS API return by using the techniques described in [Checking or specifying Aurora MySQL engine versions through AWS](#AuroraMySQL.Updates.EngineVersions) are typically more descriptive\. However, for versions before 2\.03\.2 and 1\.19, AWS always returns the version numbers `5.7.12` or `5.6.10a`\. For those older versions, use the SQL technique to check the precise version number\. 

## Aurora MySQL long\-term support \(LTS\) releases<a name="AuroraMySQL.Updates.LTS"></a>

 Each new Aurora MySQL version remains available for a certain amount of time for you to use when you create or upgrade a DB cluster\. After this period, you must upgrade any clusters that use that version\. You can manually upgrade your cluster before the support period ends, or Aurora can automatically upgrade it for you when its Aurora MySQL version is no longer supported\. 

 Aurora designates certain Aurora MySQL versions as long\-term support \(LTS\) releases\. DB clusters that use LTS releases can stay on the same version longer and undergo fewer upgrade cycles than clusters that use non\-LTS releases\. Aurora supports each LTS release for at least one year after that release becomes available\. When a DB cluster that's on an LTS release is required to upgrade, Aurora upgrades it to the next LTS release\. That way, the cluster doesn't need to be upgraded again for a long time\. 

 During the lifetime of an Aurora MySQL LTS release, new patch levels introduce fixes to important issues\. The patch levels don't include any new features\. You can choose whether to apply such patches to DB clusters running the LTS release\. For certain critical fixes, Amazon might perform a managed upgrade to a patch level within the same LTS release\. Such managed upgrades are performed automatically within the cluster maintenance window\. 

 We recommend that you upgrade to the latest release, instead of using the LTS release, for most of your Aurora MySQL clusters\. Doing so takes advantage of Aurora as a managed service and gives you access to the latest features and bug fixes\. The LTS releases are intended for clusters with the following characteristics: 
+  You can't afford downtime on your Aurora MySQL application for upgrades outside of rare occurrences for critical patches\. 
+  The testing cycle for the cluster and associated applications takes a long time for each update to the Aurora MySQL database engine\. 
+  The database version for your Aurora MySQL cluster has all the DB engine features and bug fixes that your application needs\. 

 The current LTS releases for Aurora MySQL are the following: 
+  Aurora MySQL version 1\.19\.6\. For more details about this version, see [Aurora MySQL database engine updates 2020\-03\-05 \(version 1\.19\.6\)](AuroraMySQL.Updates.1196.md)\. 
+  Aurora MySQL version 2\.04\.9\. For more details about this version, see [Aurora MySQL database engine updates 2020\-08\-14 \(version 2\.04\.9\)](AuroraMySQL.Updates.2049.md)\. 

## Upgrade paths between 5\.6\-compatible and 5\.7\-compatible clusters<a name="AuroraMySQL.Updates.UpgradePaths"></a>

 For most Aurora MySQL 1\.x and 2\.x versions, you can upgrade a MySQL 5\.6\-compatible cluster to any version of a MySQL 5\.7\-compatible cluster\. 

 However, if your cluster is running Aurora MySQL 1\.23 or higher, any upgrade to Aurora MySQL version 2\.x must be to Aurora MySQL 2\.09 or higher\. This restriction applies even when you upgrade by restoring a snapshot to create a new Aurora cluster\. Aurora MySQL 1\.23 includes improvements in Aurora storage\. For example, the maximum size of the cluster volume is larger in Aurora MySQL 1\.23 and later\. Aurora MySQL 2\.09 is the first 2\.x version that has the same storage enhancements\. 