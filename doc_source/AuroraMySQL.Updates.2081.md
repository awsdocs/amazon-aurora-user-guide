# Aurora MySQL Database Engine Updates 2020\-06\-18 \(Version 2\.08\.1\)<a name="AuroraMySQL.Updates.2081"></a>

**Version:** 2\.08\.1

Aurora MySQL 2\.08\.1 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, and 2\.08\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.08\.1\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.08\.1\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.08\.1; however, you can restore its snapshot to Aurora MySQL 2\.08\.1\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2081.Improvements"></a>

 **New features:** 
+  Global database write forwarding\. In an Aurora global database, now you can perform certain write operations, such as DML statements, while connected to a secondary cluster\. The write operations are forwarded to the primary cluster, and any changes are replicated back to the secondary clusters\. For more information, see [Write Forwarding for Secondary AWS Regions with an Aurora Global Database](aurora-global-database-write-forwarding.md)\. 

 **General stability fixes:** 
+  Fixed an issue where restoring a cluster from Amazon S3 to Aurora MySQL version 2\.08\.0 took longer than expected if the S3 backup didn't include the `mysql.host` table\. 