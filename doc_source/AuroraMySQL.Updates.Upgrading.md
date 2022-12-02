# Upgrading Amazon Aurora MySQL DB clusters<a name="AuroraMySQL.Updates.Upgrading"></a><a name="mysql_upgrade"></a>

 You can upgrade an Aurora MySQL DB cluster to get bug fixes, new Aurora MySQL features, or to change to an entirely new version of the underlying database engine\. The following sections show how\. 

**Note**  
 The type of upgrade that you do depends on how much downtime you can afford for your cluster, how much verification testing you plan to do, how important the specific bug fixes or new features are for your use case, and whether you plan to do frequent small upgrades or occasional upgrades that skip several intermediate versions\. For each upgrade, you can change the major version, the minor version, and the patch level for your cluster\. If you aren't familiar with the distinction between Aurora MySQL major versions, minor versions, and patch levels, you can read the background information at [Aurora MySQL version numbers and special versions](AuroraMySQL.Updates.Versions.md)\. 

**Tip**  
You can minimize the downtime required for DB cluster upgrade by using a blue/green deployment\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.

**Topics**
+ [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)
+ [Upgrading the major version of an Amazon Aurora MySQL DB cluster](AuroraMySQL.Updates.MajorVersionUpgrade.md)