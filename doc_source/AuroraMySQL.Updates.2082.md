# Aurora MySQL database engine updates 2020\-08\-28 \(version 2\.08\.2\)<a name="AuroraMySQL.Updates.2082"></a>

**Version:** 2\.08\.2

Aurora MySQL 2\.08\.2 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, and 2\.08\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.08\.2\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.08\.2\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.08\.2; however, you can restore its snapshot to Aurora MySQL 2\.08\.2\. See [Restoring from a DB cluster snapshot](USER_RestoreFromSnapshot.md) for more information about restoring snapshots\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\.

## Improvements<a name="AuroraMySQL.Updates.2082.Improvements"></a>

 **Critical fixes:** 
+  Fixed an issue that might cause an unplanned outage and affect database availability\. 

 **Availability fixes:** 
+ Fixed an issue where the Aurora MySQL database could restart if it is a binlog replica and replicates a DDL event over the mysql `time_zone` table\. 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2082.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2082.Compatibility"></a>

This Aurora MySQL version is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

This Aurora MySQL version does not currently support the following MySQL 5\.7 features:
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement