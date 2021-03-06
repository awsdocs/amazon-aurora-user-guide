# Aurora MySQL database engine updates 2021\-03\-04 \(version 2\.07\.4\)<a name="AuroraMySQL.Updates.2074"></a>

**Version:** 2\.07\.4

Aurora MySQL 2\.07\.4 is generally available\. Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.07\.4\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.07\.4\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.07\.4; however, you can restore its snapshot to Aurora MySQL 2\.07\.4\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  
 This version is designated as a long\-term support \(LTS\) release\. For more information, see [Aurora MySQL long\-term support \(LTS\) releases](AuroraMySQL.Updates.Versions.md#AuroraMySQL.Updates.LTS)\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.2074.Improvements"></a>

 **Security fixes:** 
+ [CVE\-2020\-14812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14812)
+ [CVE\-2020\-14793](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14793)
+ [CVE\-2020\-14790](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14790)
+ [CVE\-2020\-14775](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14775)
+ [CVE\-2020\-14769](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14769)
+ [CVE\-2020\-14765](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14765)
+ [CVE\-2020\-14760](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14760)
+ [CVE\-2020\-14672](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14672)
+ [CVE\-2020\-1971](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1971)

 **Availability improvements:** 
+  Fixed an issue that could cause a client to hang in case of a network error while reading or writing a network packet\. 
+  Improved engine restart times in some cases after interrupted DDL\. 
+  Fixed an issue where a DDL or DML could cause engine restart during a page prefetch request\. 
+  Fixed an issue where a replica could restart while performing a reverse scan of a table/index on an Aurora Read Replica\. 
+  Fixed an issue in clone cluster operation that could cause the clone to take longer\. 
+  Fixed an issue that could cause a restart of a database when using parallel query optimization for geo\-spatial column\. 
+  Fixed an issue that caused a binlog replica to stop with an `HA_ERR_KEY_NOT_FOUND` error\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2074.Patches"></a>
+  Fixed an issue in the Full\-text ngram parser when dealing with tokens containing ' ' \(space\), '%', or ','\. Customers should rebuild their FTS indexes if using ngram parser\. \(Bug \#25873310\) 
+  Fixed an issue that could cause engine restart during query execution with nested SQL views\. \(Bug \#27214153, Bug \#26864199\) 