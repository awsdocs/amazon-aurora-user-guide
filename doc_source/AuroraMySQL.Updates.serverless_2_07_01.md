# Aurora MySQL Serverless 5\.7 Engine Updates 2020\-06\-18 \(Version 2\.07\.1\)<a name="AuroraMySQL.Updates.serverless_2_07_01"></a>

 Aurora Serverless 5\.7 is generally available\. It has the same features and bug fixes as Aurora MySQL 2\.07\.1\. You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL Serverless 5\.7\. 

 For general information about Aurora Serverless, see [Using Amazon Aurora Serverless](aurora-serverless.md)\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Aurora DB Cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.Maintenance.html)\. 

 **Bug fixes:** 

 This Aurora Serverless release includes all bug fixes up to Aurora MySQL version 2\.07\.1\. For details, see [Aurora MySQL Database Engine Updates 2019\-12\-23 \(Version 2\.07\.1\)](AuroraMySQL.Updates.2071.md) and the release notes for previous Aurora MySQL versions\. 

 **Features:** 

 This Aurora Serverless release includes all new features up to Aurora MySQL version 2\.07\.1\. For details, see [Aurora MySQL Database Engine Updates 2019\-12\-23 \(Version 2\.07\.1\)](AuroraMySQL.Updates.2071.md) and the release notes for previous Aurora MySQL versions\. The following features are of particular interest for users of Aurora Serverless or Aurora MySQL with MySQL 5\.7 compatibility: 
+  Aurora MySQL Serverless now supports the hot row contention feature\. For more information, see [Aurora MySQL Database Engine Updates: 2016\-12\-14](AuroraMySQL.Updates.20161214.md)\. 
+  Aurora MySQL Serverless now supports the hash join feature\. To use this feature, you must specify the configuration setting `optimizer_switch='hash_join=on'`\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 
+  Aurora Serverless 5\.7 can use the Data API\. For more information, see [Using the Data API for Aurora Serverless](data-api.md)\. 
+  Aurora Serverless 5\.7 can use the query editor\. For more information, see [Using the Query Editor for Aurora Serverless](query-editor.md)\. 
+  Aurora Serverless 5\.7 supports the same JSON features as other Aurora MySQL versions that are compatible with MySQL 5\.7\. 