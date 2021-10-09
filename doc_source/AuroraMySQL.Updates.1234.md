# Aurora MySQL database engine updates 2021\-09\-30 \(version 1\.23\.4\)<a name="AuroraMySQL.Updates.1234"></a><a name="1234"></a><a name="1.23.4"></a>

**Version:** 1\.23\.4

Aurora MySQL 1\.23\.4 is generally available\. Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases for upgrade to 1\.23\.4 are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, and 1\.23\.\*\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the RDS Console, the AWS CLI, or the Amazon RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.1234.Improvements"></a>

 **General improvements:** 
+  Fixed an issue that can cause high CPU consumption on the reader instances due to excessive logging of informational messages in internal diagnostic log files\. 

 **High\-priority fixes:** 
+ [CVE\-2021\-2307](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2307)
+ [CVE\-2021\-2226](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2226)
+ [CVE\-2021\-2160](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2160)
+ [CVE\-2021\-2154](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2154)
+ [CVE\-2021\-2060](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2060)
+ [CVE\-2021\-2032](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2032)
+ [CVE\-2021\-2001](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2001)