# Amazon Aurora MySQL Database Engine Updates<a name="AuroraMySQL.Updates"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, as well as the type of update\. Updates are applied to all instances in a DB cluster simultaneously\. An update requires a database restart on all instances in a DB cluster, so you will experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

## Amazon Aurora Versions<a name="AuroraMySQL.Updates.Versions"></a>

Although Amazon Aurora is compatible with the MySQL and PostgreSQL database engines, Aurora includes features that are specific to Amazon Aurora and only available to Aurora DB clusters\. Aurora versions use the format <major version>\.<minor version>\.<patch version>\. You can get the version of your Aurora instance by querying for the `AURORA_VERSION` system variable\. To get the Amazon Aurora version, use one of the following queries\.


| Database Engine | Queries | 
| --- | --- | 
|   MySQL   |  <pre>select AURORA_VERSION();</pre>  | 
|   MySQL   |  <pre>select @@aurora_version;</pre>  | 
|   PostgreSQL   |  <pre>SELECT AURORA_VERSION();</pre>  | 

## Related Topics<a name="AuroraMySQL.Updates.Related"></a>
+ [Amazon Aurora MySQL 2\.0 Database Engine Updates ](AuroraMySQL.Updates.20Updates.md)
+ [Amazon Aurora MySQL 1\.1 Database Engine Updates ](AuroraMySQL.Updates.11Updates.md)
+ [MySQL Bugs Fixed by Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.MySQLBugs.md)
+ [Aurora Lab Mode Features](AuroraMySQL.Updates.LabMode.md#AuroraMySQL.Updates.LabModeFeatures)