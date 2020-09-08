# Aurora MySQL Database Engine Updates 2019\-11\-25 \(Version 1\.22\.0\)<a name="AuroraMySQL.Updates.1220"></a>

**Version:** 1\.22\.0

 Aurora MySQL 1\.22\.0 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, and 2\.07\.\*\. To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI or the RDS API\. You have the option to upgrade existing Aurora MySQL 1\.\* database clusters to Aurora MySQL 1\.22\.0\. 

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], Middle East \(Bahrain\) \[me\-south\-1\], and South America \(São Paulo\) \[sa\-east\-1\]\. There will be a separate announcement once it is made available\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.1220.Improvements"></a>

 **New features:** 
+  Aurora MySQL clusters now support the instance types r5\.8xlarge, r5\.16xlarge and r5\.24xlarge\. 
+  Binlog has new enhancements for improved commit time latency when very large transactions are involved\. 
+  Aurora MySQL now has a mechanism to minimize the time window during which events of a large transaction are written to binlog on commit\. This effectively prevents lengthy offline recovery incurred when database crashes occur during that time window\. This feature also fixes the issue where a large transaction blocks small transactions on binlog commit\. This feature is off by default and can be enabled by the service team if needed for your workload\. When enabled, it will be triggered when a transaction size is > 500MB\. 
+  Added support for the ANSI `READ COMMITTED` isolation level on the read replicas\. This isolation level enables long\-running queries on the read replica to execute without impacting the high throughput of writes on the writer node\. For more information, see [Aurora MySQL Isolation Levels](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Reference.html#AuroraMySQL.Reference.IsolationLevels)\. 
+  Global Databases now allow adding secondary read\-only replica regions for database clusters deployed in these AWS Regions: regions: US East \(N\. Virginia\) \[us\-east\-1\], US East \(Ohio\) \[us\-east\-2\], US West \(N\. California\) \[us\-west\-1\], US West \(Oregon\) \[us\-west\-2\], Europe \(Ireland\) \[eu\-west\-1\], Europe \(London\) \[eu\-west\-2\], Europe \(Paris\) \[eu\-west\-3\], Asia Pacific \(Tokyo\) \[ap\-northeast\-1\], Asia Pacific \(Seoul\) \[ap\-northeast\-2\], Asia Pacific \(Singapore\) \[ap\-southeast\-1\], Asia Pacific \(Sydney\) \[ap\-southeast\-2\], Canada \(Central\) \[ca\-central\-1\], Europe \(Frankfurt\) \[eu\-central\-1\], and Asia Pacific \(Mumbai\) \[ap\-south\-1\]\. 
+  The hot row contention feature is now generally available and does not require the Aurora lab mode setting to be ON\. This feature substantially improves throughput for workloads with many transactions contending for rows on the same page\. 
+  This version has updated timezone files to support the latest Brazil timezone update for new clusters\. 

 **Critical fixes:** 
+ [CVE\-2019\-2922](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2922)
+ [CVE\-2019\-2923](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2923)
+ [CVE\-2019\-2924](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2924)
+ [CVE\-2019\-2910](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2910)

 **High priority fixes:** 
+ [CVE\-2019\-2805](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2805)
+ [CVE\-2019\-2730](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2730)
+ [CVE\-2019\-2740](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2740)
+ [CVE\-2018\-3064](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3064)
+ [CVE\-2018\-3058](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3058)
+ [CVE\-2017\-3653](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3653)
+ [CVE\-2017\-3464](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3464)
+ [CVE\-2017\-3244](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3244)
+ [CVE\-2016\-5612](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-5612)
+ [CVE\-2016\-5439](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-5439)
+ [CVE\-2016\-0606](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-0606)
+ [CVE\-2015\-4904](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4904)
+ [CVE\-2015\-4879](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4879)
+ [CVE\-2015\-4864](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4864)
+ [CVE\-2015\-4830](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4830)
+ [CVE\-2015\-4826](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4826)
+ [CVE\-2015\-2620](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-2620)
+ [CVE\-2015\-0382](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-0382)
+ [CVE\-2015\-0381](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-0381)
+ [CVE\-2014\-6555](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6555)
+ [CVE\-2014\-4258](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-4258)
+ [CVE\-2014\-4260](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-4260)
+ [CVE\-2014\-2444](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-2444)
+ [CVE\-2014\-2436](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-2436)
+ [CVE\-2013\-5881](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-5881)
+ [CVE\-2014\-0393](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-0393)
+ [CVE\-2013\-5908](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-5908)
+ [CVE\-2013\-5807](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-5807)
+ [CVE\-2013\-3806](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-3806)
+ [CVE\-2013\-3811](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-3811)
+ [CVE\-2013\-3804](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-3804)
+ [CVE\-2013\-3807](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-3807)
+ [CVE\-2013\-2378](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-2378)
+ [CVE\-2013\-2375](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-2375)
+ [CVE\-2013\-1523](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-1523)
+ [CVE\-2013\-2381](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-2381)
+ [CVE\-2012\-5615](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2012-5615)
+ [CVE\-2014\-6489](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6489)
+  Fixed an issue in the DDL recovery component that resulted in prolonged database downtime\. Clusters that become unavailable after executing `TRUNCATE TABLE` query on a table with an `AUTO_INCREMENT` column should be updated\. 
+  Fixed an issue in the DDL recovery component that resulted in prolonged database downtime\. Clusters that become unavailable after executing `DROP TABLE` query on multiple tables in parallel should be updated\. 

 **General stability fixes:** 
+  Fixed an issue that caused read replicas to restart during a long\-running transaction\. Customers who encounter replica restarts that coincide with an accelerated drop in freeable memory should consider upgrading to this version\. 
+  Fixed an issue that incorrectly reported `ERROR 1836` when a nested query is executed against a temporary table on the read replica\. 
+  Fixed a parallel query abort error on an Aurora reader instance while a heavy write workload is running on the Aurora writer instance\. 
+  Fixed an issue that causes a database configured as a Binlog Master to restart while a heavy write workload is running\. 
+  Fixed an issue of prolonged unavailability while restarting the engine\. This addresses an issue in the buffer pool initialization\. This issue occurs rarely but can potentially impact any supported release\. 
+  Fixed an issue that generated inconsistent data in the `information_schema.replica_host_status` table\. 
+  Fixed a race condition between the parallel query and the standard execution paths that caused the Reader nodes to restart intermittently\. 
+  Improved stability of the database when the number of number of client connections exceeds the `max_connections` parameter value\. 
+  Improved stability of the reader instances by blocking unsupported DDL and `LOAD FROM S3` queries\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1220.Patches"></a>
+  Bug\#16346241 \- SERVER CRASH IN ITEM\_PARAM::QUERY\_VAL\_STR 
+  Bug\#17733850 \- NAME\_CONST\(\) CRASH IN ITEM\_NAME\_CONST::ITEM\_NAME\_CONST\(\) 
+  Bug \#20989615 \- INNODB AUTO\_INCREMENT PRODUCES SAME VALUE TWICE 
+  Bug \#20181776 \- ACCESS CONTROL DOESN'T MATCH MOST SPECIFIC HOST WHEN IT CONTAINS WILDCARD 
+  Bug \#27326796 \- MYSQL CRASH WITH INNODB ASSERTION FAILURE IN FILE PARS0PARS\.CC 
+  Bug \#20590013 \- IF YOU HAVE A FULLTEXT INDEX AND DROP IT YOU CAN NO LONGER PERFORM ONLINE DDL 