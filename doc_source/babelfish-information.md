# Babelfish version updates<a name="babelfish-information"></a>

Babelfish is an option available with Aurora PostgreSQL version 13\.4 and higher releases\. Updates to Babelfish become available with certain new releases of the Aurora PostgreSQL database engine\. For more information, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\. 

**Note**  
Babelfish DB clusters running on any version of Aurora PostgreSQL 13 can't be upgraded to Aurora PostgreSQL 14\.3, 14\.4, and 14\.5\. 

For a list of supported functionality across different Babelfish releases, see [Supported functionality in Babelfish by version](babelfish-compatibility.supported-functionality-table.md)\. 

For a list of currently unsupported functionality, see [Unsupported functionality in Babelfish](babelfish-compatibility.tsql.limitations-unsupported.md)\.

You can use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command to get a list of Aurora PostgreSQL versions in your AWS Region that support Babelfish, as shown in the following example\. 

For Linux, macOS, or Unix:

```
$ aws rds describe-db-engine-versions --region us-east-1 \
    --engine aurora-postgresql \
    --query '*[]|[?SupportsBabelfish==`true`].[EngineVersion]' \
    --output text
13.4
13.5
13.6
13.7
13.8
14.3
14.4
14.5
14.6
```

For more information, see [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) in the *AWS CLI Command Reference*\.

In the following topics, you can learn how to identify the version of Babelfish running on your Aurora PostgreSQL DB cluster, and how to upgrade to a new version\.

**Contents**
+ [Identifying your version of Babelfish](#babelfish-information-identify-version)
+ [Upgrading your Babelfish cluster to a new version](#babelfish-information-upgrading)
  + [Upgrading Babelfish to a new minor version](#babelfish-information-upgrading-minor)
  + [Upgrading Babelfish to a new major version](#babelfish-information-upgrading-major)
    + [Before upgrading Babelfish to a new major version](#babelfish-information-upgrading-preliminary)
    + [After upgrading to a new major version](#babelfish-information-upgrading-post-upgrade)
    + [Example: Upgrading the Babelfish DB cluster to a major release](#babelfish-information-upgrading-example)

## Identifying your version of Babelfish<a name="babelfish-information-identify-version"></a>

You can query Babelfish to find details about the Babelfish version, the Aurora PostgreSQL version, and the compatible Microsoft SQL Server version\. You can use the TDS port or the PostgreSQL port\. 
+ [To use the TDS port to query for version information](#apg-version-info-tds)
+ [To use the PostgreSQL port to query for version information](#apg-version-info-psql)

**To use the TDS port to query for version information**

1. Use `sqlcmd` or `ssms` to connect to the endpoint for your Babelfish DB cluster\.

   ```
   sqlcmd -S bfish_db.cluster-123456789012.aws-region.rds.amazonaws.com,1433 -U
       login-id -P password -d db_name
   ```

1. To identify the Babelfish version, run the following query:

   ```
   1> SELECT CAST(serverproperty('babelfishversion') AS VARCHAR)
   2> GO
   ```

   The query returns results similar to the following:

   ```
   serverproperty
   ------------------------------
   2.3.0
   
   (1 rows affected)
   ```

1. To identify the version of the Aurora PostgreSQL DB cluster, run the following query:

   ```
   1> SELECT aurora_version() AS aurora_version
   2> GO
   ```

   The query returns results similar to the following:

   ```
   aurora_version                                                                                                                                                                                                                                  
   -------------------------------------------------
   14.6.0
   
   (1 rows affected)
   ```

1. To identify the compatible Microsoft SQL Server version, run the following query:

   ```
   1> SELECT @@VERSION AS version
   2> GO
   ```

   The query returns results similar to the following:

   ```
   Babelfish for Aurora PostgreSQL with SQL Server Compatibility - 12.0.2000.8
   Jan 6 2023 19:17:14
   Copyright (c) Amazon Web Services
   PostgreSQL 14.6 on x86_64-pc-linux-gnu (Babelfish 2.3.0)
   
   (1 rows affected)
   ```

As an example that shows one minor difference between Babelfish and Microsoft SQL Server, you can run the following query\. On Babelfish, the query returns `1`, while on Microsoft SQL Server, the query returns `NULL`\. 

```
SELECT CAST(serverproperty('babelfish') AS VARCHAR) AS runs_on_babelfish
```

You can also use the PostgreSQL port to obtain version information, as shown in the following procedure\.

**To use the PostgreSQL port to query for version information**

1. Use `psql` or `pgAdmin` to connect to the endpoint for your Babelfish DB cluster\. 

   ```
   psql host=bfish_db.cluster-123456789012.aws-region.rds.amazonaws.com
        port=5432 dbname=babelfish_db user=sa
   ```

1. Turn on the extended feature \(`\x`\) of `psql` for more readable output\.

   ```
   babelfish_db=> \x
   babelfish_db=> SELECT
   babelfish_db=> aurora_version() AS aurora_version,
   babelfish_db=> version() AS postgresql_version,
   babelfish_db=> sys.version() AS Babelfish_compatibility,
   babelfish_db=> sys.SERVERPROPERTY('BabelfishVersion') AS Babelfish_Version;
   ```

   The query returns output similar to the following:

   ```
   -[ RECORD 1 ]-----------+-----------------------------------------------------------------------------------------------
   aurora_version          | 14.6.0
   postgresql_version      | PostgreSQL 14.6 on x86_64-pc-linux-gnu, compiled by x86_64-pc-linux-gnu-gcc (GCC) 7.4.0, 64-bit
   babelfish_compatibility | Babelfish for Aurora Postgres with SQL Server Compatibility - 12.0.2000.8                     +
                           | Jan 6 2023 19:17:14                                                                          +
                           | Copyright (c) Amazon Web Services                                                             +
                           | PostgreSQL 14.6 on x86_64-pc-linux-gnu
   babelfish_version       | 2.3.0
   ```

## Upgrading your Babelfish cluster to a new version<a name="babelfish-information-upgrading"></a>

New versions of Babelfish become available with some new releases of the Aurora PostgreSQL database engine after version 13\.4\. Each new release of Babelfish has its own version number\. As with Aurora PostgreSQL, Babelfish uses the *major*\.*minor*\.*patch* naming scheme for versions\. For example, the first Babelfish release, Babelfish version 1\.0\.0, became available as part of Aurora PostgreSQL 13\.4\.0\. 

Babelfish doesn't require a separate installation process\. As discussed in [Creating a Babelfish for Aurora PostgreSQL DB cluster](babelfish-create.md), **Turn on Babelfish** is an option that you choose when you create an Aurora PostgreSQL DB cluster\. 

Likewise, you can't upgrade Babelfish independently from the supporting Aurora DB cluster\. To upgrade an existing Babelfish for Aurora PostgreSQL DB cluster to a new version of Babelfish, you upgrade the Aurora PostgreSQL DB cluster to a new version that supports the version of Babelfish that you want to use\. The procedure that you follow for the upgrade depends on the version of Aurora PostgreSQL that's supporting your Babelfish deployment, as follows\. 

**Major version upgrades**  
You can upgrade the following Aurora PostgreSQL versions to Aurora PostgreSQL 14\.6 with Babelfish 2\.3\.  <a name="apg-bfish-mvu-supported"></a>
+ Aurora PostgreSQL 13\.8 and all higher versions
+ Aurora PostgreSQL 13\.7\.1 and all higher minor versions
+ Aurora PostgreSQL 13\.6\.4 and all higher minor versions
Upgrading an Aurora PostgreSQL DB cluster to a new major version involves several preliminary tasks\. For more information, see [How to perform a major version upgrade](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. To successfully upgrade your Babelfish for Aurora PostgreSQL DB cluster, you need to create a custom DB cluster parameter group for the new Aurora PostgreSQL version\. This new parameter group must contain the same Babelfish parameter settings as that of the cluster that you're upgrading\. For more information and for a table of major version upgrade sources and targets, see [Upgrading Babelfish to a new major version](#babelfish-information-upgrading-major)\.

**Minor version upgrades and patches**  
Minor versions and patches don't require the creation of a new DB cluster parameter group for the upgrade\. Minor versions and patches can use the minor version upgrade process, whether that's applied automatically or manually\. For more information and a table of version sources and targets, see [Upgrading Babelfish to a new minor version](#babelfish-information-upgrading-minor)\. 

**Note**  
Before performing a major or a minor upgrade, apply all pending maintenance tasks to your Babelfish for Aurora PostgreSQL cluster\.

**Topics**
+ [Upgrading Babelfish to a new minor version](#babelfish-information-upgrading-minor)
+ [Upgrading Babelfish to a new major version](#babelfish-information-upgrading-major)

### Upgrading Babelfish to a new minor version<a name="babelfish-information-upgrading-minor"></a>

A new minor version includes only changes that are backward compatible\. A *patch* version includes important fixes for a minor version after its release\. For example, the version label for the first release of Aurora PostgreSQL 13\.4 was Aurora PostgreSQL 13\.4\.0\. Several patches for that minor version have been released to date, including Aurora PostgreSQL 13\.4\.1, 13\.4\.2, and 13\.4\.4\. You can find the patches available for each Aurora PostgreSQL version in the **Patch releases** list at the top of the Aurora PostgreSQL release notes for that version\. For an example, see [PostgreSQL 14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.143X) in the *Release Notes for Aurora PostgreSQL*\. 

If your Aurora PostgreSQL DB cluster is configured with the **Auto minor version upgrade** option, your Babelfish for Aurora PostgreSQL DB cluster is upgraded automatically during the cluster's maintenance window\. To learn more about auto minor version upgrade \(AMVU\) and how to use it, see [ Automatic minor version upgrades for Aurora DB clusters ](USER_UpgradeDBInstance.Maintenance.md#Aurora.Maintenance.AMVU)\. If your cluster isn't using AMVU, you can manually upgrade your Babelfish for Aurora PostgreSQL DB cluster to new minor versions either by responding to maintenance tasks, or by modifying the cluster to use the new version\. 

When you choose an Aurora PostgreSQL version to install and when you view an existing Aurora PostgreSQL DB cluster in the AWS Management Console, the version displays the *major*\.*minor* digits only\. For example, the following image from the Console for an existing Babelfish for Aurora PostgreSQL DB cluster with Aurora PostgreSQL 13\.4 recommends upgrading the cluster to version 13\.7, a new minor release of Aurora PostgreSQL\. 

![\[Minor version upgrade available for an Aurora PostgreSQL DB cluster with Babelfish.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-minor-upgrade-available-bfish-cluster.png)

To get complete version details, including the *patch* level, you can query the Aurora PostgreSQL DB cluster using the `aurora_version` Aurora PostgreSQL function\. For more information, see [aurora\_version](aurora_version.md) in the [Aurora PostgreSQL functions reference](Appendix.AuroraPostgreSQL.Functions.md)\. You can find an example of using the function in the [To use the PostgreSQL port to query for version information](#apg-version-info-psql) procedure in [Identifying your version of Babelfish](#babelfish-information-identify-version)\. 

The following table shows Aurora PostgreSQL and Babelfish version and the available target versions that can support the minor version upgrade process\.


| Current source versions | Newest upgrade targets | Other available upgrade versions | 
| --- | --- | --- | 
| Aurora PostgreSQL | Babelfish | Aurora PostgreSQL | Babelfish | Aurora PostgreSQL versions with Babelfish option | 
| 14\.5\.0 | 2\.2\.0 | 14\.6 | 2\.3\.0 |  |  |  | 
| 14\.3\.1 | 2\.1\.1 | 14\.5 | 2\.2\.0 |  |  |  | 
| 14\.3\.0 | 2\.1\.0 | 14\.5 | 2\.2\.0 | 14\.3\.1 |  |  | 
| 13\.8 | 1\.4\.0 | 13\.9 | 1\.5\.0 |  |  |  | 
| 13\.7\.1 | 1\.3\.1 | 13\.8 | 1\.4\.0 | 13\.8 |  |  | 
| 13\.7\.0 | 1\.3\.0 | 13\.8 | 1\.4\.0 | 13\.7\.1 |  |  | 
| 13\.6\.4 | 1\.2\.4 | 13\.8 | 1\.4\.0 | 13\.7 |  |  | 
| 13\.6\.3 | 1\.2\.1 | 13\.8 | 1\.4\.0 | 13\.7 | 13\.6\.4 |  | 
| 13\.6\.2 | 1\.2\.1 | 13\.8 | 1\.4\.0 | 13\.7 | 13\.6\.4 |  | 
| 13\.6\.1 | 1\.2\.0 | 13\.8 | 1\.4\.0 | 13\.7 | 13\.6\.4 |  | 
| 13\.6\.0 | 1\.2\.0 | 13\.8 | 1\.4\.0 | 13\.7 | 13\.6\.4 |  | 
| 13\.5 | 1\.1\.0 | 13\.8 | 1\.4\.0 | 13\.7 | 13\.6 |  | 
| 13\.4 | 1\.0\.0 | 13\.8 | 1\.4\.0 | 13\.7 | 13\.6 | 13\.5 | 

### Upgrading Babelfish to a new major version<a name="babelfish-information-upgrading-major"></a>

The following table shows Aurora PostgreSQL version and Babelfish version that can support a major version upgrade\.


| Current source versions | Newest available upgrade target | Other available versions \(minor version upgrades\) | 
| --- | --- | --- | 
| Aurora PostgreSQL | Babelfish | Aurora PostgreSQL | Babelfish | Aurora PostgreSQL version \(Babelfish version\) | 
| 13\.9 | 1\.5\.0 | 14\.6 | 2\.3\.0 | – | – | 
| 13\.8 | 1\.4\.0 | 14\.6 | 2\.3\.0 | – | – | 
| 13\.7\.1 | 1\.3\.1 | 14\.6 | 2\.3\.0 | 13\.8 \(1\.4\) | – | 
| 13\.6\.4 | 1\.2\.2 | 14\.6 | 2\.3\.0 | 13\.8 \(1\.4\) | 13\.7 \(1\.3\) | 

#### Before upgrading Babelfish to a new major version<a name="babelfish-information-upgrading-preliminary"></a>

An upgrade might involve brief outages\. For that reason, we recommend that you perform or schedule upgrades during your maintenance window or during other periods of low usage\.

**Before you perform a major version upgrade**

 

1. Identify the Babelfish version of your existing Aurora PostgreSQL DB cluster by using the commands outlined in [Identifying your version of Babelfish](#babelfish-information-identify-version)\. The Aurora PostgreSQL version and Babelfish version information is handled by PostgreSQL, so follow the steps detailed in the [To use the PostgreSQL port to query for version information](#apg-version-info-psql) procedure to get the details\. 

1. Verify that your version supports the major version upgrade feature\. For a list of versions, see the [Babelfish versions that support major version upgrade](#apg-bfish-mvu-supported)\.

1. Choose the target version for your upgrade from the table\. In some cases, the version that you want for the upgrade might not be a target for your current version\. If so, you can apply minor versions and patches to upgrade your cluster to a version that can support the major version upgrade\. For a table of minor versions, see [Upgrading Babelfish to a new minor version](#babelfish-information-upgrading-minor)\.

   For example, if your Babelfish version is running on an Aurora PostgreSQL 13\.5 DB cluster and you want to upgrade to Aurora PostgreSQL 14\.6, then first apply all the minor releases and patches to upgrade your cluster to Aurora PostgreSQL 13\.6\.4\. When your cluster is at version 13\.6\.4, continue with the major version upgrade process\.

1. Create a manual snapshot of your current Babelfish DB cluster as a backup\. The backup lets you restore the cluster to its Aurora PostgreSQL version, Babelfish version, and restore all data to the state before the upgrade\. For more information, see [Creating a DB cluster snapshot](USER_CreateSnapshotCluster.md)\. Be sure to keep your existing custom DB cluster parameter group to use again if you decide to restore this cluster to its pre\-upgraded state\. For more information, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md) and [Parameter group considerations](aurora-restore-snapshot.md#aurora-restore-snapshot.Parameters)\. 

1. Prepare a custom DB cluster parameter group for the target Aurora PostgreSQL DB version\. Duplicate the settings for the Babelfish parameters from your current Babelfish for Aurora PostgreSQL DB cluster\. To find a list of all Babelfish parameters, see [DB cluster parameter group settings for Babelfish](babelfish-configuration.md)\. For a major version upgrade, the following parameters require the same settings as the source DB cluster\. For the upgrade to succeed, all the settings must be the same\.
   + rds\.babelfish\_status
   + babelfishpg\_tds\.tds\_default\_numeric\_precision
   + babelfishpg\_tds\.tds\_default\_numeric\_scale
   + babelfishpg\_tsql\.database\_name
   + babelfishpg\_tsql\.default\_locale
   + babelfishpg\_tsql\.migration\_mode
   + babelfishpg\_tsql\.server\_collation\_name
**Warning**  
If the settings for the Babelfish parameters in the custom DB cluster parameter group for the new Aurora PostgreSQL version don't match the parameter values of the cluster that you're upgrading, the `ModifyDBCluster` operation fails\. An `InvalidParameterCombination` error message appears in the AWS Management Console or in the output from the `modify-db-cluster` AWS CLI command\.

1. Use the AWS Management Console or the AWS CLI to create the custom DB cluster parameter group\. Choose the applicable Aurora PostgreSQL family for the version of Aurora PostgreSQL that you want for the upgrade\. 
**Tip**  
Parameter groups are managed at the AWS Region level\. When you work with AWS CLI, you can configure with a default Region instead of specifying the `--region` in the command\. To learn more about using the AWS CLI, see [Quick setup](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html) in the *AWS Command Line Interface User Guide*\. 

In the following examples, you can find the series of AWS CLI commands that create a new DB cluster parameter group for Aurora PostgreSQL version 14 and then modify several values to match the settings of the existing Babelfish DB cluster parameters\. 

#### After upgrading to a new major version<a name="babelfish-information-upgrading-post-upgrade"></a>

After a major version upgrade to a new Aurora PostgreSQL version, the `IDENTITY` value in tables with an `IDENTITY` column might be larger \(\+32\) than the value was before the upgrade\. The result is that when the next row is inserted into such tables, the generated identity column value jumps to the \+32 number and starts the sequence from there\. This condition won't negatively affect the functions of your Babelfish DB cluster\. However, if you want, you can reset the sequence object based on the maximum value of the column\. To do so, connect to the T\-SQL port on your Babelfish writer instance using `sqlcmd` or another SQL Server client\. For more information, see [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)\. 

```
sqlcmd -S bfish-db.cluster-123456789012.aws-region.rds.amazonaws.com,1433 -U
     sa -P ******** -d dbname
```

When connected, use the following SQL command to generate statements that you can use to seed the associated sequence object\. This SQL command works for both single database and multiple database Babelfish configurations\. For more information about these two deployment models, see [Using Babelfish with a single database or multiple databases](babelfish-architecture.md#babelfish-single_vs_multi_db)\. 

```
DECLARE @schema_prefix NVARCHAR(200) = ''
IF current_setting('babelfishpg_tsql.migration_mode') = 'multi-db'
    SET @schema_prefix = db_name() + '_'
SELECT 'SELECT setval(pg_get_serial_sequence(''' + @schema_prefix + schema_name(tables.schema_id)
    + '.' + tables.name + ''', ''' + columns.name + '''),(select max(' + columns.name + ')
    FROM ' + schema_name(tables.schema_id) + '.' + tables.name + '));
    'FROM sys.tables tables JOIN sys.columns
    columns ON tables.object_id = columns.object_id
    WHERE columns.is_identity = 1
GO
```

The query generates a series of SELECT statements that you can then run to reset the maximum IDENTITY value and close any gap\. The following shows the output when using the sample SQL Server database, Northwind, running on a Babelfish cluster\. 

```
--------------------------------------------------------
SELECT setval(pg_get_serial_sequence('northwind_dbo.categories', 'categoryid'),(select max(categoryid)
    FROM dbo.categories));

SELECT setval(pg_get_serial_sequence('northwind_dbo.orders', 'orderid'),(select max(orderid)
    FROM dbo.orders));
                                                                                                                        
SELECT setval(pg_get_serial_sequence('northwind_dbo.products', 'productid'),(select max(productid)
    FROM dbo.products));
                                                                                                                        
SELECT setval(pg_get_serial_sequence('northwind_dbo.shippers', 'shipperid'),(select max(shipperid)
    FROM dbo.shippers));
                                                                                                                        
SELECT setval(pg_get_serial_sequence('northwind_dbo.suppliers', 'supplierid'),(select max(supplierid)
    FROM dbo.suppliers));
                                                                                                                        
(5 rows affected)
```

Run the statements one by one to reset the sequence values\. 

#### Example: Upgrading the Babelfish DB cluster to a major release<a name="babelfish-information-upgrading-example"></a>

 The following procedure shows an example of upgrading an Aurora PostgreSQL 13\.6\.4 DB cluster running Babelfish version 1\.2\.2 to Aurora PostgreSQL 14\.6\. First, you create a custom DB cluster parameter group for Aurora PostgreSQL 14\. Next, you modify the parameter values to match those of your Aurora PostgreSQL version 13 source\. Finally, you perform the upgrade by modifying the source cluster\. For more information, see [DB cluster parameter group settings for Babelfish](babelfish-configuration.md)\. In that topic, you can also find information about using the AWS Management Console to perform the upgrade\.

Use the [create\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-parameter-group.html) CLI command to create the DB cluster parameter group for the new version\.

For Linux, macOS, or Unix:

```
aws rds create-db-cluster-parameter-group \
    --db-cluster-parameter-group-name docs-lab-babelfish-apg-14 \
    --db-parameter-group-family aurora-postgresql14 \
    --description 'New custom parameter group for upgrade to new major version' \
    --region us-west-1
```

When you issue this command, the custom DB cluster parameter group is created in the AWS Region\. You see output similar to the following\.

```
{
    "DBClusterParameterGroup": {
        "DBClusterParameterGroupName": "docs-lab-babelfish-apg-14",
        "DBParameterGroupFamily": "aurora-postgresql14",
        "Description": "New custom parameter group for upgrade to new major version",
        "DBClusterParameterGroupArn": "arn:aws:rds:us-west-1:111122223333:cluster-pg:docs-lab-babelfish-apg-14"
    }
}
```

For more information, see [Creating a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. 

Use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) CLI command to modify the settings so that they match the source cluster\.

For Windows:

```
aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name docs-lab-babelfish-apg-14 ^
  --parameters "ParameterName=rds.babelfish_status,ParameterValue=on,ApplyMethod=pending-reboot" ^
 "ParameterName=babelfishpg_tds.tds_default_numeric_precision,ParameterValue=38,ApplyMethod=pending-reboot" ^
 "ParameterName=babelfishpg_tds.tds_default_numeric_scale,ParameterValue=8,ApplyMethod=pending-reboot" ^
 "ParameterName=babelfishpg_tsql.database_name,ParameterValue=babelfish_db,ApplyMethod=pending-reboot" ^
 "ParameterName=babelfishpg_tsql.default_locale,ParameterValue=en-US,ApplyMethod=pending-reboot" ^
 "ParameterName=babelfishpg_tsql.migration_mode,ParameterValue=single-db,ApplyMethod=pending-reboot" ^
 "ParameterName=babelfishpg_tsql.server_collation_name,ParameterValue=sql_latin1_general_cp1_ci_as,ApplyMethod=pending-reboot"
```

The response looks similar to the following\.

```
{
    "DBClusterParameterGroupName": "docs-lab-babelfish-apg-14"
}
```

Use the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) CLI command to modify the cluster to use the new version and the new custom DB cluster parameter group\. You also specify the `--allow-major-version-upgrade` argument, as shown in the following sample\. 

```
aws rds modify-db-cluster \
--db-cluster-identifier docs-lab-bfish-apg-14 \
--engine-version 14.6 \
--db-cluster-parameter-group-name docs-lab-babelfish-apg-14 \
--allow-major-version-upgrade \
--region us-west-1 \
--apply-immediately
```

Use the [reboot\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html) CLI command to reboot the writer instance of the cluster, so that the parameter settings can take effect\.

```
aws rds reboot-db-instance \
--db-instance-identifier docs-lab-bfish-apg-14-instance-1\
--region us-west-1
```