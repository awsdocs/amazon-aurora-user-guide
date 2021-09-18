# Aurora MySQL database engine updates 2020\-12\-11 \(version 2\.09\.1\)<a name="AuroraMySQL.Updates.2091"></a>

 **Version:** 2\.09\.1 

 Aurora MySQL 2\.09\.1 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. 

 You can upgrade an existing Aurora MySQL 2\.\* database cluster to Aurora MySQL 2\.09\.1\. For clusters running Aurora MySQL version 1, you can upgrade an existing Aurora MySQL 1\.23 or higher cluster directly to 2\.09\.1\. You can also restore a snapshot from any currently supported Aurora MySQL release into Aurora MySQL 2\.09\.1\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\. 

## Improvements<a name="AuroraMySQL.Updates.2091.Improvements"></a>

 **Security fixes:** 

 Fixes and other enhancements to fine\-tune handling in a managed environment\. Additional CVE fixes below: 
+  [CVE\-2020\-14567](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14567) 
+  [CVE\-2020\-14559](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14559) 
+  [CVE\-2020\-14553](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14553) 
+  [CVE\-2020\-14547](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14547) 
+  [CVE\-2020\-14540](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14540) 
+  [CVE\-2020\-2812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2812) 
+  [CVE\-2020\-2806](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2806) 
+  [CVE\-2020\-2780](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2780) 
+  [CVE\-2020\-2765](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2765) 
+  [CVE\-2020\-2763](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2763) 
+  [CVE\-2020\-2760](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2760) 
+  [CVE\-2020\-2579](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2579) 

 **Incompatible changes:** 

 This version introduces a permission change that affects the behavior of the `mysqldump` command\. Users must have the `PROCESS` privilege to access the `INFORMATION_SCHEMA.FILES` table\. To run the `mysqldump` command without any changes, grant the `PROCESS` privilege to the database user that the `mysqldump` command connects to\. You can also run the `mysqldump` command with the `--no-tablespaces` option\. With that option, the `mysqldump `output doesn't include any `CREATE LOGFILE GROUP` or `CREATE TABLESPACE` statements\. In that case, the `mysqldump` command doesn't access the `INFORMATION_SCHEMA.FILES` table, and you don't need to grant the `PROCESS` permission\. 

 **Availability improvements:** 
+  Fixed an issue that might cause a client session to hang when the database engine encounters an error while reading from or writing to the network\. 
+  Fixed a memory leak in dynamic resizing feature, introduced in 2\.09\.0\. 

 **Global databases:** 
+  Fixed multiple issues where a global database secondary Region's replicas might restart when upgraded to release 2\.09\.0 while the primary Region writer was on an older release version\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2091.Patches"></a>
+  **Replication:** Interleaved transactions could sometimes deadlock the slave applier when the transaction isolation level was set to [REPEATABLE READ](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read)\. \(Bug \#25040331\) 
+  For a table having a [TIMESTAMP](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) or [DATETIME](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) column having a default of [CURRENT\_TIMESTAMP](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_current-timestamp), the column could be initialized to `0000-00-00 00:00:00` if the table had a `BEFORE INSERT` trigger\. \(Bug \#25209512, Bug \#84077\) 
+  For an [INSERT](https://dev.mysql.com/doc/refman/5.7/en/insert.html) statement for which the `VALUES` list produced values for the second or later row using a subquery containing a join, the server could exit after failing to resolve the required privileges\. \(Bug \#23762382\) 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2091.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2091.Compatibility"></a>

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