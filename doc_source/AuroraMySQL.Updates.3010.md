# Aurora MySQL database engine updates 2021\-11\-18 \(version 3\.01\.0, compatible with MySQL 8\.0\.23\)<a name="AuroraMySQL.Updates.3010"></a><a name="3010"></a><a name="3.01.0"></a>

 **Version:** 3\.01\.0 

 Aurora MySQL 3\.01\.0 is generally available\. Aurora MySQL 3\.01 versions are compatible with MySQL 8\.0\.23, Aurora MySQL 2\.x versions are compatible with MySQL 5\.7, and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\. 

 For details of new features in Aurora MySQL version 3 and differences between Aurora MySQL version 3 and Aurora MySQL version 2 or community MySQL 8\.0, see [Comparison of Aurora MySQL version 2 and Aurora MySQL version 3](AuroraMySQL.MySQL80.md#Aurora.AuroraMySQL.Compare-v2-v3) and [Comparison of Aurora MySQL version 3 and community MySQL 8\.0](AuroraMySQL.MySQL80.md#Aurora.AuroraMySQL.Compare-80-v3)\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, 2\.09\.\*, 2\.10\.\*, and 3\.01\.\*\. 

 You can restore a snapshot from any currently supported Aurora MySQL version 2 cluster into Aurora MySQL 3\.01\.0\. 

 For information on planning an upgrade to Aurora MySQL version 3, see [Upgrade planning for Aurora MySQL version 3](AuroraMySQL.MySQL80.md#AuroraMySQL.mysql80-planning)\. For the upgrade procedure itself, see [Upgrading to Aurora MySQL version 3](AuroraMySQL.MySQL80.md#AuroraMySQL.mysql80-upgrade-procedure)\. For general information about Aurora MySQL upgrades, see [Upgrading Amazon Aurora MySQL DB clusters](AuroraMySQL.Updates.Upgrading.md)\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the Amazon RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.3010.Improvements"></a>

 Aurora MySQL version 3\.01\.0 is generally compatible with community MySQL 8\.0\.23\. This version includes the security fixes for Common Vulnerabilities and Exposures \(CVE\) issues as of community MySQL 8\.0\.23\. 

 Aurora MySQL version 3\.01\.0 contains all the Aurora\-specific bug fixes through Aurora MySQL version 2\.10\.0\. 

 For details of new features in Aurora MySQL version 3, see [Features from community MySQL 8\.0](AuroraMySQL.MySQL80.md#AuroraMySQL.8.0-features-community) and [New parallel query optimizations](AuroraMySQL.MySQL80.md#AuroraMySQL.8.0-features-pq)\. 