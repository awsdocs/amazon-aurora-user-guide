# Amazon Aurora MySQL lab mode<a name="AuroraMySQL.Updates.LabMode"></a><a name="labmode"></a>

Aurora lab mode is used to enable Aurora features that are available in the current Aurora database version, but are not enabled by default\. While Aurora lab mode features are not recommended for use in production DB clusters, you can use Aurora lab mode to enable these features for DB clusters in your development and test environments\. For more information about Aurora features available when Aurora lab mode is enabled, see [Aurora lab mode features](#AuroraMySQL.Updates.LabModeFeatures)\.

The `aurora_lab_mode` parameter is an instance\-level parameter that is in the default parameter group\. The parameter is set to `0` \(disabled\) in the default parameter group\. To enable Aurora lab mode, create a custom parameter group, set the `aurora_lab_mode` parameter to `1` \(enabled\) in the custom parameter group, and modify one or more DB instances in your Aurora cluster to use the custom parameter group\. Then connect to the appropriate instance endpoint to try the lab mode features\. For information on modifying a DB parameter group, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. For information on parameter groups and Amazon Aurora, see [Aurora MySQL configuration parameters](AuroraMySQL.Reference.ParameterGroups.md)\.

## Aurora lab mode features<a name="AuroraMySQL.Updates.LabModeFeatures"></a>

The following table lists the Aurora features currently available when Aurora lab mode is enabled\. You must enable Aurora lab mode before any of these features can be used\.


| Feature | Description | 
| --- | --- | 
|  Scan Batching  |  Aurora MySQL scan batching speeds up in\-memory, scan\-oriented queries significantly\. The feature boosts the performance of table full scans, index full scans, and index range scans by batch processing\.  | 
|  Hash Joins  |  This feature can improve query performance when you need to join a large amount of data by using an equijoin\. You can use this feature without lab mode in Aurora MySQL version 2\. For more information about using this feature, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.  | 
|  Fast DDL  |  This feature allows you to run an `ALTER TABLE tbl_name ADD COLUMN col_name column_definition` operation nearly instantaneously\. The operation completes without requiring the table to be copied and without materially impacting other DML statements\. Since it does not consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance classes\. Fast DDL is currently only supported for adding a nullable column, without a default value, at the end of a table\. For more information about using this feature, see [Altering tables in Amazon Aurora using Fast DDL](AuroraMySQL.Managing.FastDDL.md)\.  | 