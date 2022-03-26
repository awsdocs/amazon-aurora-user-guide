# Database engine updates for Amazon Aurora MySQL<a name="AuroraMySQL.Updates"></a><a name="mysql_relnotes"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, as well as the type of update\. 

 Updates are applied to all instances in a DB cluster at the same time\. An update requires a database restart on all instances in a DB cluster, so you experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

For details about the Aurora MySQL versions that are supported by Amazon Aurora, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html)\.

 Following, you can learn how to choose the right Aurora MySQL version for your cluster, how to specify the version when you create or upgrade a cluster, and the procedures to upgrade a cluster from one version to another with minimal interruption\. 

**Topics**
+ [Aurora MySQL version numbers and special versions](AuroraMySQL.Updates.Versions.md)
+ [Preparing for Amazon Aurora MySQL\-Compatible Edition version 1 end of life](Aurora.MySQL56.EOL.md)
+ [Upgrading Amazon Aurora MySQL DB clusters](AuroraMySQL.Updates.Upgrading.md)
+ [Database engine updates for Amazon Aurora MySQL version 3](AuroraMySQL.Updates.30Updates-moved.md)
+ [Database engine updates for Amazon Aurora MySQL version 2](AuroraMySQL.Updates.20Updates-moved.md)
+ [Database engine updates for Amazon Aurora MySQL version 1](AuroraMySQL.Updates.11Updates-moved.md)
+ [Database engine updates for Aurora MySQL Serverless clusters](AuroraMySQL.Updates.ServerlessUpdates-moved.md)
+ [MySQL bugs fixed by Aurora MySQL database engine updates](AuroraMySQL.Updates.MySQLBugs-moved.md)
+ [Security vulnerabilities fixed in Amazon Aurora MySQL](AuroraMySQL.CVE_list-moved.md)