# Database Engine Updates for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates"></a>

In the following topic, you can find version and update information specific to Amazon Aurora with PostgreSQL compatibility\. For more information about updates that apply generally to Aurora, see [Amazon Aurora Updates](Aurora.Updates.md)\.

**Note**  
For how to upgrade the Amazon Aurora PostgreSQL engine version, see [Upgrading the PostgreSQL DB Engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

**Topics**
+ [Identifying Your Version of Amazon Aurora PostgreSQL](#AuroraPostgreSQL.Updates.Versions)
+ [Engine Versions for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.20180305.md)
+ [Extensions and Modules for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Extensions.md)

## Identifying Your Version of Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.Versions"></a>

Amazon Aurora includes certain features that are general to Aurora and available to all Aurora DB clusters\. Aurora includes other features that are specific to a particular database engine that Aurora supports\. These features are available only to those Aurora DB clusters that use that database engine, such as Aurora PostgreSQL\.

An Aurora database has two version numbers, the Aurora version number and the database engine version number: 

**Aurora version number**  
For how to get the Aurora version number, see [Identifying Your Amazon Aurora Version](Aurora.Updates.md#Aurora.Updates.Versions)\.

**PostgreSQL engine version number**  
To get the database engine version number for an Aurora PostgreSQL DB instance, query for the `SERVER_VERSION` runtime parameter as shown following\.   

```
SHOW SERVER_VERSION;
```

To see which Aurora version number maps to which PostgreSQL engine version number, see [Engine Versions for Amazon Aurora PostgreSQL ](AuroraPostgreSQL.Updates.20180305.md)\.