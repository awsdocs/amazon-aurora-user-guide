# Aurora MySQL Database Engine Updates 2019\-11\-25 \(Version 2\.07\.0\)<a name="AuroraMySQL.Updates.2070"></a>

**Version:** 2\.07\.0

Aurora MySQL 2\.07\.0 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, and 2\.07\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.07\.0\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.07\.0\. You cannot upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.07\.0; however, you can restore its snapshot to Aurora MySQL 2\.07\.0\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], Middle East \(Bahrain\) \[me\-south\-1\], and South America \(São Paulo\) \[sa\-east\-1\]\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2070.Improvements"></a>

 **New features:** 
+  Global Databases now allow adding secondary read\-only replica regions for database clusters deployed in these AWS Regions: regions: US East \(N\. Virginia\) \[us\-east\-1\], US East \(Ohio\) \[us\-east\-2\], US West \(N\. California\) \[us\-west\-1\], US West \(Oregon\) \[us\-west\-2\], Europe \(Ireland\) \[eu\-west\-1\], Europe \(London\) \[eu\-west\-2\], Europe \(Paris\) \[eu\-west\-3\], Asia Pacific \(Tokyo\) \[ap\-northeast\-1\], Asia Pacific \(Seoul\) \[ap\-northeast\-2\], Asia Pacific \(Singapore\) \[ap\-southeast\-1\], Asia Pacific \(Sydney\) \[ap\-southeast\-2\], Canada \(Central\) \[ca\-central\-1\], Europe \(Frankfurt\) \[eu\-central\-1\], and Asia Pacific \(Mumbai\) \[ap\-south\-1\]\. 
+  Amazon Aurora machine learning is a highly optimized integration between the Aurora MySQL database and AWS machine learning \(ML\) services\. Aurora machine learning allows developers to add a variety of ML\-based predictions to their database applications by invoking ML models using the familiar SQL programming language they already use for database development, without having to build custom integrations or learn separate tools\. For more information, see [Using Machine Learning \(ML\) Capabilities with Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-ml.html)\. 
+  Added support for the ANSI `READ COMMITTED` isolation level on the read replicas\. This isolation level enables long\-running queries on the read replica to execute without impacting the high throughput of writes on the writer node\. For more information, see [Aurora MySQL Isolation Levels](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Reference.html#AuroraMySQL.Reference.IsolationLevels)\. 

 **Critical fixes:** 
+ [CVE\-2019\-2922](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2922)
+ [CVE\-2019\-2923](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2923)
+ [CVE\-2019\-2924](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2924)
+ [CVE\-2019\-2910](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2910)

 **High\-priority fixes:** 
+  Fixed an issue in the DDL recovery that resulted in prolonged database downtime\. Clusters that become unavailable after executing multi\-table drop statement, for example `DROP TABLE t1, t2, t3`, should be updated to this version\. 
+  Fixed an issue in the DDL recovery that resulted in prolonged database downtime\. Clusters that become unavailable after executing `INPLACE ALTER TABLE` DDL statements should be updated to this version\. 

 **General stability fixes:** 
+  Fixed an issue that generated inconsistent data in the `information_schema.replica_host_status` table\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2070.Patches"></a>
+  Bug \#26251621: INCORRECT BEHAVIOR WITH TRIGGER AND GCOL 
+  Bug \#22574695: ASSERTION `\!TABLE \|\| \(\!TABLE\->READ\_SET \|\| BITMAP\_IS\_SET\(TABLE\->READ\_SET, FIEL 
+  Bug \#25966845: INSERT ON DUPLICATE KEY GENERATE A DEADLOCK 
+  Bug \#23070734: CONCURRENT TRUNCATE TABLES CAUSE STALL 
+  Bug \#26191879: FOREIGN KEY CASCADES USE EXCESSIVE MEMORY 
+  Bug \#20989615: INNODB AUTO\_INCREMENT PRODUCES SAME VALUE TWICE 

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2070.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2070.Compatibility"></a>

Aurora MySQL 2\.07\.0 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.07\.0 does not currently support the following MySQL 5\.7 features:
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