# Amazon Aurora updates<a name="Aurora.Updates"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Amazon Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, and also the type of update\. Updates require a database restart, so you typically experience 20 to 30 seconds of downtime\. After this downtime, you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

**Note**  
The time required to reboot your DB instance depends on the crash recovery process, database activity at the time of reboot, and the behavior of your specific DB engine\. To improve the reboot time, we recommend that you reduce database activity as much as possible during the reboot process\. Reducing database activity reduces rollback activity for in\-transit transactions\. 

Following, you can find information on general updates to Amazon Aurora\. Some of the updates applied to Amazon Aurora are specific to a database engine supported by Aurora\. For more information about database engine updates for Aurora, see the following table\.


| Database engine | Updates | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Database engine updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md)  | 
|  Amazon Aurora PostgreSQL  |  See [Database engine updates for Amazon Aurora PostgreSQL ](AuroraPostgreSQL.Updates.md)  | 

## Identifying your Amazon Aurora version<a name="Aurora.Updates.Versions"></a>

Amazon Aurora includes certain features that are general to Aurora and available to all Aurora DB clusters\. Aurora includes other features that are specific to a particular database engine that Aurora supports\. These features are available only to those Aurora DB clusters that use that database engine, such as Aurora PostgreSQL\.

An Aurora DB instance provides two version numbers, the Aurora version number and the Aurora database engine version number\. Aurora version numbers use the following format\.

```
<major version>.<minor version>.<patch version>
```

To get the Aurora version number from an Aurora DB instance using a particular database engine, use one of the following queries\.


| Database engine | Queries | 
| --- | --- | 
|  Amazon Aurora MySQL  |  <pre>SELECT AURORA_VERSION();</pre> <pre>SHOW @@aurora_version;</pre>  | 
|  Amazon Aurora PostgreSQL  |  <pre>SELECT AURORA_VERSION();</pre>  | 