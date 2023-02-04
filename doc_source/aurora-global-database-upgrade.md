# Upgrading an Amazon Aurora global database<a name="aurora-global-database-upgrade"></a>

Upgrading an Aurora global database follows the same procedures as upgrading Aurora DB clusters\. However, following are some important differences to take note of before you start the process\.

We recommend that you upgrade the primary and secondary DB clusters to the same version\. Aurora global databases can't fail over from a primary to a secondary DB cluster when the primary and secondary have different DB engine versions—whether major, minor, or patch versions\.

## Major version upgrades<a name="aurora-global-database-upgrade.major"></a>

When you perform a major version upgrade of an Amazon Aurora global database, you upgrade the global database cluster instead the individual clusters that it contains\.

To learn how to upgrade an Aurora PostgreSQL global database to a higher major version, see [Major upgrades for global databases](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.GlobalDB)\. To learn how to upgrade an Aurora MySQL global database to a higher major version, see [In\-place major upgrades for global databases](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Upgrading.GlobalDB)\.

**Note**  
With an Aurora global database based on Aurora MySQL, you can't use the in\-place upgrade method to upgrade the Aurora DB engine from version 2 to version 3\. Use the snapshot restore method instead\. For more information, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.  
With an Aurora global database based on Aurora PostgreSQL, you can't perform a major version upgrade of the Aurora DB engine if the recovery point objective \(RPO\) feature is turned on\.

## Minor version upgrades<a name="aurora-global-database-upgrade.minor"></a>

For a minor upgrade on an Aurora global database, you upgrade all of the secondary clusters before you upgrade the primary cluster\.

To learn how to upgrade an Aurora PostgreSQL global database to a higher minor version, see [How to perform minor version upgrades and apply patches](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.Minor)\. To learn how to upgrade an Aurora MySQL global database to a higher minor version, see [Upgrading Aurora MySQL by modifying the engine version](AuroraMySQL.Updates.Patching.md#AuroraMySQL.Updates.Patching.ModifyEngineVersion)\.

**Note**  
A secondary cluster must have at least one DB instance to perform a minor upgrade\.

### Version compatibility for failover<a name="aurora-global-database-upgrade.minor.incompatibility"></a>

When you upgrade your Aurora global database to one of the following minor engine versions, you can perform a managed cross\-Region database failover\. You can perform this failover regardless of the patch versions of your primary and secondary DB clusters\. For minor engine versions lower than the ones on this list, you must upgrade your primary and secondary DB clusters to the same major, minor, and patch levels to perform a managed cross\-Region database failover\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-upgrade.html)