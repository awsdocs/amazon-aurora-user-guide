# Aurora MySQL Serverless 5\.7 engine updates 2021\-07\-16 \(version 2\.08\.3\)<a name="AuroraMySQL.Updates.serverless_2_08_03"></a>

 Aurora Serverless 5\.7 is generally available\. It has the same features and bug fixes as Aurora MySQL 2\.08\.3\. 

You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL Serverless 5\.7\. By using a snapshot, you can also upgrade an Aurora MySQL\-5\.6 compatible Aurora Serverless v1 DB cluster to an Aurora MySQL\-5\.7 compatible Aurora Serverless v1 DB cluster\. To do so:
+ Create a snapshot from the Aurora MySQL 5\.6 Serverless  DB cluster\. To learn how, see [Creating a DB cluster snapshot](USER_CreateSnapshotCluster.md)\. 
+ Restore the snapshot to a new Aurora MySQL 5\.7 Serverless cluster\. For more information, see [Restoring an Aurora Serverless v1 DB cluster](aurora-serverless.restorefromsnapshot.md)\. 

Aurora Serverless v1 doesn't have its own version number\. It uses the number of the Aurora MySQL version that supports it to distinguish between Aurora MySQL 5\.7 and Aurora MySQL 5\.6 updates\. For more information, see [Aurora Serverless v1 and Aurora database engine versions](aurora-serverless.relnotes.md)\.

For general information about Aurora Serverless, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\.

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Aurora DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.Maintenance.html)\. 

 **Bug fixes:** 

 This Aurora Serverless release includes all bug fixes up to Aurora MySQL version 2\.08\.3\. For details, see [Aurora MySQL database engine updates 2020\-11\-12 \(version 2\.08\.3\)](AuroraMySQL.Updates.2083.md) and the release notes for previous Aurora MySQL versions\. 

 **Features:** 

 This Aurora Serverless release includes all new features up to Aurora MySQL version 2\.08\.3\. For details, see [Aurora MySQL database engine updates 2020\-11\-12 \(version 2\.08\.3\)](AuroraMySQL.Updates.2083.md) and the release notes for previous Aurora MySQL versions\. 