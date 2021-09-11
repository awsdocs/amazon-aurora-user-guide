# Aurora MySQL database engine updates 2019\-12\-23 \(version 2\.07\.1\)<a name="AuroraMySQL.Updates.2071"></a>

**Version:** 2\.07\.1

Aurora MySQL 2\.07\.1 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, and 2\.07\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.07\.1\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.07\.1\. You cannot upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.07\.1; however, you can restore its snapshot to Aurora MySQL 2\.07\.1\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], and Middle East \(Bahrain\) \[me\-south\-1\]\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\.

## Improvements<a name="AuroraMySQL.Updates.2071.Improvements"></a>

 **High priority fixes:** 
+ Fixed a slow memory leak in Aurora specific database tracing and logging sub\-system that lowers the freeable memory\.

 **General Stability fixes:** 
+ Fixed a crash during execution of a complex query involving multi\-table joins and aggregation that use intermediate tables internally\.

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2071.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2071.Compatibility"></a>

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