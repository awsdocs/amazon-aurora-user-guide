# Aurora MySQL Database Engine Updates 2019\-11\-11 \(Version 2\.05\.0\)<a name="AuroraMySQL.Updates.2050"></a>

**Version:** 2\.05\.0

Aurora MySQL 2\.05\.0 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\* and 2\.04\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.05\.0\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters, up to 2\.04\.6, to Aurora MySQL 2\.05\.0\. You cannot upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.05\.0; however, you can restore its snapshot to Aurora MySQL 2\.05\.0\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], Europe \(Stockholm\) \[eu\-north\-1\], and Middle East \(Bahrain\) \[me\-south\-1\]\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2050.Improvements"></a>

 **Critical fixes:** 
+ [CVE\-2018\-0734](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-0734)
+ [CVE\-2019\-2534](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2534)
+ [CVE\-2018\-3155](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3155)
+ [CVE\-2018\-2612](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2612)
+ [CVE\-2017\-3599](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3599)
+ [CVE\-2018\-3056](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3056)
+ [CVE\-2018\-2562](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2562)
+ [CVE\-2017\-3329](https://nvd.nist.gov/vuln/detail/CVE-2017-3329)
+ [CVE\-2018\-2696](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2696)
+  Fixed an issue where the events in current binlog file on the master were not replicated on the worker if the value of the parameter `sync_binlog` was not set to 1\. 

 **High\-priority fixes:** 
+  Customers with database size close to 64 TiB are strongly advised to upgrade to this version to avoid downtime due to stability bugs affecting volumes close to the Aurora storage limit\. 
+  The default value of the parameter `aurora_binlog_replication_max_yield_seconds` has been changed to zero to prevent an increase in replication lag in favor of foreground query performance on the binlog master\. 

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.2050.BugFixes"></a>
+  Bug\#23054591: PURGE BINARY LOGS TO is reading the whole binlog file and causing MySql to Stall 

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2050.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2050.Compatibility"></a>

Aurora MySQL 2\.05\.0 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.05\.0 does not currently support the following MySQL 5\.7 features:
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