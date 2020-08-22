# Aurora MySQL Database Engine Updates 2020\-03\-05 \(Version 1\.20\.1\)<a name="AuroraMySQL.Updates.1201"></a>

**Version:** 1\.20\.1

Aurora MySQL 1\.20\.1 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\* and 2\.07\.\*\. You can restore the snapshot of an Aurora MySQL 1\.\* database into Aurora MySQL 1\.20\.1\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the RDS Console, the AWS CLI, or the Amazon RDS API\. 

**Note**  
 This version is currently not available in the following regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\]\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1201.Improvements"></a>

 **High priority fixes:** 
+  Fixed an issue of intermittent connection failures after certificate rotation\. 
+  Fixed an issue related to connection close concurrency that would result in a failover under heavy workload\. 

 **General stability fixes:** 
+  Fixed a crash during execution of a complex query involving multi\-table joins and aggregation that uses intermediate tables internally\. 