# Aurora MySQL Database Engine Updates 2020\-04\-17 \(Version 2\.07\.2\)<a name="AuroraMySQL.Updates.2072"></a>

**Version:** 2\.07\.2

Aurora MySQL 2\.07\.2 is generally available\. Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, and 2\.07\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.07\.2\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.07\.2\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.07\.2; however, you can restore its snapshot to Aurora MySQL 2\.07\.2\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  
 This version is currently not available in the following AWS Region: \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\]\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.2072.Improvements"></a>

 **High priority fixes:** 
+  Fixed an issue that caused cloning to take longer on some database clusters with high write loads\. 
+  Fixed an issue that could cause queries on a reader DB instance with execution plans using secondary indexes to return uncommitted data\. The issue is limited to data affected by data manipulation language \(DML\) operations that modify primary or secondary index key columns\. 

 **General improvements:** 
+  Fixed an issue that resulted in a slow restore of an Aurora 1\.x DB cluster containing FTS \(Full Text Search\) indexes to an Aurora 2\.x DB cluster\. 
+  Fixed an issue that caused slower restores of an Aurora 1\.x database snapshot containing partitioned tables with special characters in table names to an Aurora 2\.x DB cluster\. 
+  Fixed an issue that caused errors when querying slow query logs and general logs in reader DB instances\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2072.Patches"></a>
+  Bug \#23104498: Fixed an issue in Performance Schema in reporting total memory used\. \([https://github.com/mysql/mysql-server/commit/20b6840df5452f47313c6f9a6ca075bfbc00a96b](https://github.com/mysql/mysql-server/commit/20b6840df5452f47313c6f9a6ca075bfbc00a96b)\) 
+  Bug \#22551677: Fixed an issue in Performance Schema that could lead to the database engine crashing when attempting to take it offline\. \([https://github.com/mysql/mysql-server/commit/05e2386eccd32b6b444b900c9f8a87a1d8d531e9](https://github.com/mysql/mysql-server/commit/05e2386eccd32b6b444b900c9f8a87a1d8d531e9)\) 
+  Bug \#23550835, Bug \#23298025, Bug \#81464: Fixed an issue in Performance Schema that causes a database engine crash due to exceeding the capacity of an internal buffer\. \([https://github.com/mysql/mysql-server/commit/b4287f93857bf2f99b18fd06f555bbe5b12debfc](https://github.com/mysql/mysql-server/commit/b4287f93857bf2f99b18fd06f555bbe5b12debfc)\) 