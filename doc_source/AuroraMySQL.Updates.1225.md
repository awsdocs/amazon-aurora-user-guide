# Aurora MySQL database engine updates 2021\-06\-03 \(version 1\.22\.5\)<a name="AuroraMySQL.Updates.1225"></a><a name="1225"></a><a name="1.22.5"></a>

 **Version:** 1\.22\.5 

 Aurora MySQL 1\.22\.5 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\. 

 Currently supported Aurora MySQL releases for upgrade to 1\.22\.5 are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, and 1\.22\.\*\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the RDS Console, the AWS CLI, or the Amazon RDS API\. 

**Note**  
 This version is designated as a long\-term support \(LTS\) release\. For more information, see [Aurora MySQL long\-term support \(LTS\) releases](AuroraMySQL.Updates.Versions.md#AuroraMySQL.Updates.LTS)\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.1225.Improvements"></a>

 **Availability improvements:** 
+  Resolved an issue that could cause the database to stall, and subsequently restart or fail over due to a concurrency conflict between internal cleanup threads\. 
+  Resolved an issue that could cause the cluster to become unavailable if the database restarted while holding XA transactions in prepared state, and then restarted again before those transactions were committed or rolled back\. Prior to this fix, you can address the issue by restoring the cluster to a point in time before the first restart\. 
+  Resolved an issue that could cause the InnoDB purge to become blocked if the database restarts while processing a DDL statement\. As a result, the InnoDB history list length would grow and the cluster storage volume would keep growing until it fills up, making the database unavailable\. Prior to this fix, you can mitigate the issue by restarting the database again to unblock purge\. 