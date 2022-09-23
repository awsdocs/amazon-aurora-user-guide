# Overview of Aurora PostgreSQL query plan management<a name="AuroraPostgreSQL.Optimize.overview"></a>

Aurora PostgreSQL query plan management is designed to ensure plan stability regardless of changes to the database that might cause query plan regression\. *Query plan regression* occurs when the optimizer chooses a sub\-optimal plan for a given SQL statement after system or database changes\. Changes to statistics, constraints, environment settings, query parameter bindings, and upgrades to the PostgreSQL database engine can all cause plan regression\.

With Aurora PostgreSQL query plan management, you can control how and when query execution plans change\. The benefits of Aurora PostgreSQL query plan management include the following\. 
+ Improve plan stability by forcing the optimizer to choose from a small number of known, good plans\.
+ Optimize plans centrally and then distribute the best plans globally\.
+ Identify indexes that aren't used and assess the impact of creating or dropping an index\.
+ Automatically detect a new minimum\-cost plan discovered by the optimizer\.
+ Try new optimizer features with less risk, because you can choose to approve only the plan changes that improve performance\.

You can use the tools provided by query plan management proactively, to specify the best plan for certain queries\. Or you can use query plan management to react to changing circumstances and avoid plan regressions\. For more information, see [Best practices for Aurora PostgreSQL query plan management](AuroraPostgreSQL.Optimize.BestPractice.md)\. 

**Topics**
+ [Supported SQL statements](#AuroraPostgreSQL.Optimize.overview.features)
+ [Query plan management limitations](#AuroraPostgreSQL.Optimize.overview.limitations)
+ [Query plan management terminology](#AuroraPostgreSQL.Optimize.Start-terminology)
+ [Aurora PostgreSQL versions and query plan management versions](#AuroraPostgreSQL.Optimize.overview.versions)
+ [Turning on Aurora PostgreSQL query plan management](#AuroraPostgreSQL.Optimize.Enable)
+ [Upgrading Aurora PostgreSQL query plan management](#AuroraPostgreSQL.Optimize.Upgrade)
+ [Turning off Aurora PostgreSQL query plan management](#AuroraPostgreSQL.Optimize.Enable.turnoff)

## Supported SQL statements<a name="AuroraPostgreSQL.Optimize.overview.features"></a>

Query plan management supports the following types of SQL statements\.
+ Any SELECT, INSERT, UPDATE, or DELETE statement, regardless of complexity\. 
+ Prepared statements\. For more information, see [PREPARE](https://www.postgresql.org/docs/14/sql-prepare.html) in the PosgreSQL documentation\.
+ Dynamic statements, including those run in immediate\-mode\. For more information, see [Dynamic SQL](https://www.postgresql.org/docs/current/ecpg-dynamic.html) and [EXECUTE IMMEDIATE](https://www.postgresql.org/docs/current/ecpg-sql-execute-immediate.html) in PostgreSQL documentation\. 
+ Embedded SQL commands and statements\. For more information, see [Embedded SQL Commands](https://www.postgresql.org/docs/current/ecpg-sql-commands.html) in the PostgreSQL documentation\.
+ Statements inside named functions\. For more information, see [CREATE FUNCTION](https://www.postgresql.org/docs/current/sql-createfunction.html) in the PostgreSQL documentation\. 
+ Statements containing temp tables\.

You can use query plan management with `EXPLAIN` and `EXPLAIN ANALYZE` in manual mode to capture a plan without actually running it\. For more information, see [Analyzing the optimizer's chosen plan](AuroraPostgreSQL.Optimize.UsePlans.md#AuroraPostgreSQL.Optimize.UsePlans.AnalyzePlans)\. To learn more about query plan management's modes \(manual, automatic\), see [Capturing Aurora PostgreSQL execution plans](AuroraPostgreSQL.Optimize.CapturePlans.md)\.

Aurora PostgreSQL query plan management supports all PostgreSQL language features, including partitioned tables, inheritance, row\-level security, and recursive common table expressions \(CTEs\)\. To learn more about these PostgreSQL language features, see [Table Partitioning](https://www.postgresql.org/docs/current/ddl-partitioning.html), [Row Security Policies](https://www.postgresql.org/docs/current/ddl-rowsecurity.html), and [WITH Queries \(Common Table Expressions\)](https://www.postgresql.org/docs/current/queries-with.html) and other topics in the PostgreSQL documentation\. 

## Query plan management limitations<a name="AuroraPostgreSQL.Optimize.overview.limitations"></a>

When using query plan management, be aware of the following limitations\.
+ **Plans aren't captured for certain types of SQL statements** – The current release of Aurora PostgreSQL query plan management doesn't capture plans for these statements\.
  + Statements that reference system relations, such as `pg_class`\. This is by design, to prevent a large number of system\-generated plans that are used internally from being captured\. This also applies to system tables inside views\.
  + Statements inside `DO` blocks\. This is also by design, given that these blocks contain transient, anonymous functions\.  
+ **Larger DB instance class might be needed for your Aurora PostgreSQL DB cluster** – Depending on the workload, query plan management might require a DB instance class that provides more than 2 vCPUs\. This is because of the limited number of `max_worker_processes` allowed by a 2\-vCPU DB instance class \(db\.t3\.medium, for example\)\. We recommend that you consider the demands of your workload before setting up query plan management and choose a DB instance class with more than 2 vCPUs for your Aurora PostgreSQL DB cluster\. If the DB instance class falls short in its ability to support your workload, query plan management raises an error message such as the following\. 

  ```
  WARNING: could not register plan insert background process
  HINT: You may need to increase max_worker_processes.
  ```

  The number of max\_worker\_processes is limited by the DB instance class size\. In this case, you should scale up your Aurora PostgreSQL DB cluster to a DB instance class size with more memory\. For more information,see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\.

## Query plan management terminology<a name="AuroraPostgreSQL.Optimize.Start-terminology"></a>

The following terms are used throughout this topic\. 

**managed statement**  
A SQL statement captured by the optimizer under query plan management\. A managed statement has one or more query execution plans stored in the `apg_plan_mgmt.dba_plans` view\.

**plan baseline**  
The set of approved plans for a given managed statement\. That is, all the plans for the managed statement that have "Approved" for their `status` column in the `dba_plan` view\. 

**plan history**  
The set of all captured plans for a given managed statement\. The plan history contains all plans captured for the statement, regardless of status\. 

**query plan regression**  
The case when the optimizer chooses a less optimal plan than it did before a given change to the database environment, such as a new PostgreSQL version or changes to statistics\.

## Aurora PostgreSQL versions and query plan management versions<a name="AuroraPostgreSQL.Optimize.overview.versions"></a>

Query plan management is supported by all currently available Aurora PostgreSQL releases\. For more information, see the list of [Amazon Aurora PostgreSQL updates](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) in the *Release Notes for Aurora PostgreSQL*\.

Query plan management functionality is added to your Aurora PostgreSQL DB cluster when you install the `apg_plan_mgmt` extension\. Different versions of Aurora PostgreSQL support different versions of the `apg_plan_mgmt` extension\. We recommend that you upgrade the query plan management extension to the latest release for your version of Aurora PostgreSQL\. 

**Note**  
For release notes for each `apg_plan_mgmt` extension versions, see [Aurora PostgreSQL apg\_plan\_mgmt extension versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html#AuroraPostgreSQL.Extensions.apg_plan_mgmt) in the *Release Notes for Aurora PostgreSQL*\.

You can identify the version running on your cluster by connecting to an instance using `psql` and using the metacommand \\dx to list extensions as shown following\.

```
labdb=> \dx
                       List of installed extensions
     Name      | Version |    Schema     |                            Description
---------------+---------+---------------+-------------------------------------------------------------------
 apg_plan_mgmt | 1.0     | apg_plan_mgmt | Amazon Aurora with PostgreSQL compatibility Query Plan Management
 plpgsql       | 1.0     | pg_catalog    | PL/pgSQL procedural language
(2 rows)
```

The output shows that this cluster is using 1\.0 version of the extension\. Only certain `apg_plan_mgmt` versions are available for a given Aurora PostgreSQL version\. In some cases, you might need to upgrade the Aurora PostgreSQL DB cluster to a new minor release or apply a patch so that you can upgrade to the most recent version of query plan management\. The `apg_plan_mgmt` version 1\.0 shown in the output is from an Aurora PostgreSQL version 10\.17 DB cluster, which doesn't have a newer version of `apg_plan_mgmt` available\. In this case, the Aurora PostgreSQL DB cluster should be upgraded to a more recent version of PostgreSQL\.

For more information about upgrading your Aurora PostgreSQL DB cluster to a new version of PostgreSQL, see [Amazon Aurora PostgreSQL updates](AuroraPostgreSQL.Updates.md)\.

To learn how to upgrade the `apg_plan_mgmt` extension, see [Upgrading Aurora PostgreSQL query plan management](#AuroraPostgreSQL.Optimize.Upgrade)\.

## Turning on Aurora PostgreSQL query plan management<a name="AuroraPostgreSQL.Optimize.Enable"></a>

Setting up query plan management for your Aurora PostgreSQL DB cluster involves installing an extension and changing several DB cluster parameter settings\. You need `rds_superuser` permissions to install the `apg_plan_mgmt` extension and to turn on the feature for the Aurora PostgreSQL DB cluster\.

Installing the extension creates a new role, `apg_plan_mgmt`\. This role allows database users to view, manage, and maintain query plans\. As an administrator with `rds_superuser` privileges, be sure to grant the `apg_plan_mgmt` role to database users as needed\. 

**To turn on query plan management for your Aurora PostgreSQL DB cluster**

The following steps turn on query plan management for all SQL statements that get submitted to the Aurora PostgreSQL DB cluster\. This is known as *automatic* mode\. To learn more about the difference between modes, see [Capturing Aurora PostgreSQL execution plans](AuroraPostgreSQL.Optimize.CapturePlans.md)\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Create a custom DB cluster parameter group for your Aurora PostgreSQL DB cluster\. You need to change certain parameters to activate query plan management and to set its behavior\. For more information, see [Creating a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Creating)\.

1. Open the custom DB cluster parameter group and set the `rds.enable_plan_management` parameter to `1`, as shown in the following image\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-qpm-custom-db-cluster-param-change-1.png)

   For more information, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

1. Create a custom DB parameter group that you can use to set query plan parameters at the instance level\. For more information, see [Creating a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. 

1. Modify the writer instance of the Aurora PostgreSQL DB cluster to use the custom DB parameter group\. For more information, see [Modifying a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\.

1. Modify the Aurora PostgreSQL DB cluster to use the custom DB cluster parameter group\. For more information, see [Modifying the DB cluster by using the console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\.

1. Reboot your DB instance to enable the custom parameter group settings\.

1. Connect to your Aurora PostgreSQL DB cluster's DB instance endpoint using `psql` or `pgAdmin`\. The following example uses the default `postgres` account for the `rds_superuser` role\.

   ```
   psql --host=cluster-instance-1.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=my-db
   ```

1. Create the `apg_plan_mgmt` extension for your DB instance, as shown following\.

   ```
   labdb=> CREATE EXTENSION apg_plan_mgmt;
   CREATE EXTENSION
   ```
**Tip**  
Install the `apg_plan_mgmt` extension in the `template1` default PostgreSQL database so that the query plan management feature is available in all new databases that you create\. To learn more, see [Template Databases](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) in the PostgreSQL documentation\.

1. Change the `apg_plan_mgmt.capture_plan_baselines` parameter to `automatic`\. This setting causes the optimizer to generate plans for every SQL statement\. 
**Note**  
Query plan management also has a *manual* mode that you can use for specific SQL statements\. To learn more, see [Capturing Aurora PostgreSQL execution plans](AuroraPostgreSQL.Optimize.CapturePlans.md)\. 

1. Change the value of `apg_plan_mgmt.use_plan_baselines` parameter to "on\." This parameter causes the optimizer to choose a plan for the statement from its plan baseline\. To learn more, see [Using Aurora PostgreSQL managed plans](AuroraPostgreSQL.Optimize.UsePlans.md)\. 
**Note**  
You can modify the value of either of these dynamic parameters for the session without needing to reboot the instance\. 

When your query plan management set up is complete, be sure to grant the `apg_plan_mgmt` role to any database users that need to view, manage, or maintain query plans\. 

## Upgrading Aurora PostgreSQL query plan management<a name="AuroraPostgreSQL.Optimize.Upgrade"></a>

We recommend that you upgrade the query plan management extension to the latest release for your version of Aurora PostgreSQL\.

1. Connect to the writer instance of your Aurora PostgreSQL DB cluster as a user that has `rds_superuser` privileges\. If you kept the default name when you set up your instance, you connect as `postgres` This example shows how to use `psql`, but you can also use pgAdmin if you prefer\.

   ```
   psql --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

1. Run the following query to upgrade the extension\.

   ```
   ALTER EXTENSION apg_plan_mgmt UPDATE TO '2.1';
   ```

1. Use the [apg\_plan\_mgmt\.validate\_plans](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.validate_plans) function to update the hashes of all plans\. The optimizer validates all Approved, Unapproved, and Rejected plans to ensure that they's still viable plans for new version of the extension\. 

   ```
   SELECT apg_plan_mgmt.validate_plans('update_plan_hash');
   ```

   To learn more about using this function, see [Validating plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.ValidatingPlans)\.

1. Use the [apg\_plan\_mgmt\.reload](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.reload) function to refresh any plans in the shared memory with the validated plans from the dba\_plans view\. 

   ```
   SELECT apg_plan_mgmt.reload();
   ```

To learn more about all functions available for query plan management, see [Function reference for Aurora PostgreSQL query plan management](AuroraPostgreSQL.Optimize.Functions.md)\.

## Turning off Aurora PostgreSQL query plan management<a name="AuroraPostgreSQL.Optimize.Enable.turnoff"></a>

You can disable query plan management at any time by turning off the `apg_plan_mgmt.use_plan_baselines` and `apg_plan_mgmt.capture_plan_baselines`\. 

```
labdb=> SET apg_plan_mgmt.use_plan_baselines = off;

labdb=> SET apg_plan_mgmt.capture_plan_baselines = off;
```