# Database Engine Updates for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates"></a>

In the following topic, you can find version and update information specific to Amazon Aurora with PostgreSQL compatibility\. For more information about updates that apply generally to Aurora, see [Amazon Aurora Updates](Aurora.Updates.md)\.

**Topics**
+ [Identifying Your Version of Amazon Aurora PostgreSQL](#AuroraPostgreSQL.Updates.Versions)
+ [Upgrading the Amazon Aurora PostgreSQL Engine Version](#AuroraPostgreSQL.Updates.Patching)
+ [Database Engine Versions for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.20180305.md)

## Identifying Your Version of Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.Versions"></a>

Amazon Aurora includes certain features that are general to Aurora and available to all Aurora DB clusters\. Aurora includes other features that are specific to a particular database engine that Aurora supports\. These features are available only to those Aurora DB clusters that use that database engine, such as Aurora PostgreSQL\.

An Aurora DB instance provides two version numbers, the Aurora version number and the Aurora database engine version number\. For more information about the Aurora version number, see [Amazon Aurora Versions](Aurora.Updates.md#Aurora.Updates.Versions)\.

You can get the Aurora database engine version number for an Aurora PostgreSQL DB instance by querying for the `SERVER_VERSION` runtime parameter\. To get the Aurora database engine version number, use the following query\.

```
SHOW SERVER_VERSION;
```

## Upgrading the Amazon Aurora PostgreSQL Engine Version<a name="AuroraPostgreSQL.Updates.Patching"></a>

For more information about upgrading the Amazon Aurora PostgreSQL engine version, see [Upgrading an Aurora PostgreSQL DB Cluster Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.