# Upgrading an Amazon Aurora global database<a name="aurora-global-database-upgrade"></a>

Upgrading an Aurora global database follows the same procedures as upgrading Aurora DB clusters\. However, following are some important differences to take note of before you start the process\.

## Major version upgrades<a name="aurora-global-database-upgrade.major"></a>

When you perform a major version upgrade of an Amazon Aurora global database, you upgrade the global database cluster instead the individual clusters that it contains\.

To learn how to upgrade an Aurora PostgreSQL global database to a higher major version, see [In\-place major upgrades for global databases](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.GlobalDB)\. To learn how to upgrade an Aurora MySQL global database to a higher major version, see [In\-place major upgrades for global databases](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Upgrading.GlobalDB)\.

**Note**  
With an Aurora global database based on Aurora PostgreSQL, you can't perform a major version upgrade of the Aurora DB engine if the recovery point objective \(RPO\) feature is turned on\.

## Minor version upgrades<a name="aurora-global-database-upgrade.minor"></a>

For a minor upgrade on an Aurora global database, you upgrade all of the secondary clusters before you upgrade the primary cluster\.

To learn how to upgrade an Aurora PostgreSQL global database to a higher minor version, see [Manually upgrading the Aurora PostgreSQL engine](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.Upgrading.Manual)\. To learn how to upgrade an Aurora MySQL global database to a higher minor version, see [Upgrading Aurora MySQL by modifying the engine version](AuroraMySQL.Updates.Patching.md#AuroraMySQL.Updates.Patching.ModifyEngineVersion)\.