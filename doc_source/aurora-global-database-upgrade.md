# Upgrading an Amazon Aurora global database<a name="aurora-global-database-upgrade"></a>

Upgrading an Aurora global database follows the same procedures as upgrading Aurora DB clusters\. However, following are some important differences to take note of before you start the process\.

We recommend that you upgrade the primary and secondary DB clusters to the same version\. You can only perform a managed cross\-Region database failover on an Aurora global database if the primary and secondary DB clusters have the same major, minor, and patch level engine versions\. However, the patch levels can be different, depending on the minor engine version\. For more information, see [Patch level compatibility for managed cross\-Region failover](#aurora-global-database-upgrade.minor.incompatibility)\.

## Major version upgrades<a name="aurora-global-database-upgrade.major"></a>

When you perform a major version upgrade of an Amazon Aurora global database, you upgrade the global database cluster instead the individual clusters that it contains\.

To learn how to upgrade an Aurora PostgreSQL global database to a higher major version, see [Major upgrades for global databases](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.GlobalDB)\.

**Note**  
With an Aurora global database based on Aurora PostgreSQL, you can't perform a major version upgrade of the Aurora DB engine if the recovery point objective \(RPO\) feature is turned on\. For information about the RPO feature, see [Managing RPOs for Aurora PostgreSQL–based global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-manage-recovery)\.

To learn how to upgrade an Aurora MySQL global database to a higher major version, see [In\-place major upgrades for global databases](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Upgrading.GlobalDB)\.

**Note**  
With an Aurora global database based on Aurora MySQL, you can't perform an in\-place upgrade from Aurora MySQL version 2 to version 3 if the `lower_case_table_names` parameter is turned on\.  
To perform a major version upgrade to Aurora MySQL version 3 when using `lower_case_table_names`, use the following process:  
Remove all secondary Regions from the global cluster\. Follow the steps in [Removing a cluster from an Amazon Aurora global database](aurora-global-database-managing.md#aurora-global-database-detaching)\.
Upgrade the engine version of the primary Region to Aurora MySQL version 3\. Follow the steps in [How to perform an in\-place upgrade](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Upgrading.Procedure)\.
Add secondary Regions to the global cluster\. Follow the steps in [Adding an AWS Region to an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-attaching)\.
You can also use the snapshot restore method instead\. For more information, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.

## Minor version upgrades<a name="aurora-global-database-upgrade.minor"></a>

For a minor upgrade on an Aurora global database, you upgrade all of the secondary clusters before you upgrade the primary cluster\.

To learn how to upgrade an Aurora PostgreSQL global database to a higher minor version, see [How to perform minor version upgrades and apply patches](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.Minor)\. To learn how to upgrade an Aurora MySQL global database to a higher minor version, see [Upgrading Aurora MySQL by modifying the engine version](AuroraMySQL.Updates.Patching.md#AuroraMySQL.Updates.Patching.ModifyEngineVersion)\.

Before you perform the upgrade, review the following considerations:
+ A secondary cluster must have at least one DB instance to perform a minor upgrade\.
+ If you upgrade an Aurora MySQL global database to version 2\.11\.\*, you must upgrade your primary and secondary DB clusters to the exact same version, including the patch level\.
+ To support managed cross\-Region database failover, you must upgrade your primary and secondary DB clusters to the exact same version, including the patch level, depending on the engine version\. For more information, see [Patch level compatibility for managed cross\-Region failover](#aurora-global-database-upgrade.minor.incompatibility)\.

### Patch level compatibility for managed cross\-Region failover<a name="aurora-global-database-upgrade.minor.incompatibility"></a>

When you upgrade your Aurora global database to one of the following minor engine versions, you can perform a managed cross\-Region database failover even if the patch levels of your primary and secondary DB clusters don't match\. For minor engine versions lower than the ones on this list, you must upgrade your primary and secondary DB clusters to the same major, minor, and patch levels to perform a managed cross\-Region database failover\. Make sure to review the version information and the notes in the following table\.


| Database engine | Minor engine versions | Notes | 
| --- | --- | --- | 
| Aurora MySQL | No minor versions | With all minor versions, you can only perform a managed cross\-Region database failover if the patch levels of the primary and secondary DB clusters match\. | 
| Aurora PostgreSQL |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-upgrade.html)  | With the minor engine versions listed in the previous column, you can perform a managed cross\-Region database failover from a primary DB cluster with one patch level to a secondary DB cluster with a different patch level\.  With minor versions lower than these, you can only perform a managed cross\-Region database failover if the patch levels of the primary and secondary DB clusters match\. | 