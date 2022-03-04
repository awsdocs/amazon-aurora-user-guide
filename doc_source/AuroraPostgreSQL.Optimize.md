# Managing query execution plans for Aurora PostgreSQL<a name="AuroraPostgreSQL.Optimize"></a>

With query plan management for Amazon Aurora PostgreSQL\-Compatible Edition, you can control how and when query execution plans change\. Query plan management has two main objectives: 
+ Preventing plan regressions when the database system changes
+ Controlling when the query optimizer can use new plans

The quality and consistency of query optimization have a major impact on the performance and stability of any relational database management system \(RDBMS\)\. Query optimizers create a query execution plan for a SQL statement at a specific point in time\. As conditions change, the optimizer might pick a different plan that makes performance better or worse\. In some cases, a number of changes can all cause the query optimizer to choose a different plan and lead to performance regression\. These changes include changes in statistics, constraints, environment settings, query parameter bindings, and software upgrades\. Regression is a major concern for high\-performance applications\.

With query plan management, you can control execution plans for a set of statements that you want to manage\. You can do the following:
+ Improve plan stability by forcing the optimizer to choose from a small number of known, good plans\.
+ Optimize plans centrally and then distribute the best plans globally\.
+ Identify indexes that aren't used and assess the impact of creating or dropping an index\.
+ Automatically detect a new minimum\-cost plan discovered by the optimizer\.
+ Try new optimizer features with less risk, because you can choose to approve only the plan changes that improve performance\.

**Topics**
+ [Enabling query plan management for Aurora PostgreSQL](#AuroraPostgreSQL.Optimize.Enable)
+ [Upgrading query plan management](#AuroraPostgreSQL.Optimize.Upgrade)
+ [Basics of query plan management](#AuroraPostgreSQL.Optimize.Start)
+ [Best practices for query plan management](AuroraPostgreSQL.Optimize.BestPractice.md)
+ [Examining plans in the apg\_plan\_mgmt\.dba\_plans view](AuroraPostgreSQL.Optimize.ViewPlans.md)
+ [Capturing execution plans](AuroraPostgreSQL.Optimize.CapturePlans.md)
+ [Using managed plans](AuroraPostgreSQL.Optimize.UsePlans.md)
+ [Maintaining execution plans](AuroraPostgreSQL.Optimize.Maintenance.md)
+ [Parameter reference for query plan management](AuroraPostgreSQL.Optimize.Parameters.md)
+ [Function reference for query plan management](AuroraPostgreSQL.Optimize.Functions.md)

## Enabling query plan management for Aurora PostgreSQL<a name="AuroraPostgreSQL.Optimize.Enable"></a>

Query plan management is available with the following Aurora PostgreSQL versions: 
+ All Aurora PostgreSQL 13 versions
+ Aurora PostgreSQL version 12\.4 and higher
+ Aurora PostgreSQL version 11\.6 and higher
+ Aurora PostgreSQL version 10\.5 and higher

Only users with the `rds_superuser` role can complete the following procedure\. The `rds_superuser` is required for creating the `apg_plan_mgmt` extension and its `apg_plan_mgmt` role\. Users must be granted the `apg_plan_mgmt` role to administer the `apg_plan_mgmt` extension\.

**To enable query plan management**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Create a new instance\-level parameter group to use for query plan management parameters\. For more information, see [Creating a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Creating)\. Associate the new parameter group with the DB instances in which you want to use query plan management\. For more information, see [Modify a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\.

1. Create a new cluster\-level parameter group to use for query plan management parameters\. For more information, see [Creating a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. Associate the new cluster\-level parameter group with the DB clusters in which you want to use query plan management\. For more information, see [Modifying the DB cluster by using the console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\.

1. Open your cluster\-level parameter group and set the `rds.enable_plan_management` parameter to `1`\. For more information, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

1. Reboot your DB instance to enable this new setting\.

1. Connect to your DB instance with a SQL client such as psql\.

1. Create the `apg_plan_mgmt` extension for your DB instance\. The following shows an example\. 

   ```
   psql my-database
   my-database=> CREATE EXTENSION apg_plan_mgmt;
   ```

   If you create the `apg_plan_mgmt` extension in the `template1` default database, then the query plan management extension is available in each new database that you create\. 

You can disable query plan management at any time by turning off the `apg_plan_mgmt.use_plan_baselines` and `apg_plan_mgmt.capture_plan_baselines`:

```
my-database=> SET apg_plan_mgmt.use_plan_baselines = off;
my-database=> SET apg_plan_mgmt.capture_plan_baselines = off;
```

## Upgrading query plan management<a name="AuroraPostgreSQL.Optimize.Upgrade"></a>

The latest version of query plan management is 2\.0\. If you installed an earlier version of query plan management, we strongly recommend that you upgrade to version 2\.0\. For version details, see [Extension versions for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Extensions.md)\.

To upgrade, run the following commands at the cluster or DB instance level\.

```
ALTER EXTENSION apg_plan_mgmt UPDATE TO '2.0';
SELECT apg_plan_mgmt.validate_plans('update_plan_hash');
SELECT apg_plan_mgmt.reload();
```

## Basics of query plan management<a name="AuroraPostgreSQL.Optimize.Start"></a>

You can manage any SELECT, INSERT, UPDATE, or DELETE statement with query plan management, regardless of how complex the statement is\. Prepared, dynamic, embedded, and immediate\-mode SQL statements are all supported\. All PostgreSQL language features can be used, including partitioned tables, inheritance, row\-level security, and recursive common table expressions \(CTEs\)\.

**Topics**
+ [Performing a manual plan capture](#AuroraPostgreSQL.Optimize.Start.Capture)
+ [Viewing captured plans](#AuroraPostgreSQL.Optimize.Start.View)
+ [Working with managed statements and the SQL hash](#AuroraPostgreSQL.Optimize.Start.ManagedStatements)
+ [Working with automatic plan capture](#AuroraPostgreSQL.Optimize.Start.AutomaticCapture)
+ [Validating plans](#AuroraPostgreSQL.Optimize.Start.Validate)
+ [Approving new plans that improve performance](#AuroraPostgreSQL.Optimize.Start.Verify)
+ [Deleting plans](#AuroraPostgreSQL.Optimize.Start.Delete)

### Performing a manual plan capture<a name="AuroraPostgreSQL.Optimize.Start.Capture"></a>

To capture plans for specific statements, use the manual capture mode as in the following example\.

```
/* Turn on manual capture */
SET apg_plan_mgmt.capture_plan_baselines = manual; 
EXPLAIN SELECT COUNT(*) from pg_class;       -- capture the plan baseline
SET apg_plan_mgmt.capture_plan_baselines = off;    -- turn off capture
SET apg_plan_mgmt.use_plan_baselines =     true;   -- turn on plan usage
```

You can either execute SELECT, INSERT, UPDATE, or DELETE statements, or you can include the EXPLAIN statement as shown above\. Use EXPLAIN to capture a plan without the overhead or potential side\-effects of executing the statement\. For more about manual capture, see [Manually capturing plans for specific SQL statements](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Manual)\. Note that query plan management doesn't save the plans for statements that refer to system tables such as `pg_class`\. 

### Viewing captured plans<a name="AuroraPostgreSQL.Optimize.Start.View"></a>

When EXPLAIN SELECT runs in the previous example, the optimizer saves the plan\. To do so, it inserts a row into the `apg_plan_mgmt.dba_plans` view and commits the plan in an autonomous transaction\. You can see the contents of the `apg_plan_mgmt.dba_plans` view if you've been granted the `apg_plan_mgmt` role\. The following query displays some important columns of the `dba_plans` view\. 

```
SELECT sql_hash, plan_hash, status, enabled, plan_outline, sql_text::varchar(40)
FROM apg_plan_mgmt.dba_plans
ORDER BY sql_text, plan_created;
```

Each row displayed represents a managed plan\. The preceding example displays the following information\.
+ `sql_hash` – The ID of the managed statement that the plan is for\.
+ `plan_hash` – The ID of the managed plan\.
+ `status` – The status of the plan\. The optimizer can run an approved plan\.
+ `enabled` – A value that indicates whether the plan is enabled for use or disabled and not for use\.
+ `plan_outline` – Details of the managed plan\.

For more about the `apg_plan_mgmt.dba_plans` view, see [Examining plans in the apg\_plan\_mgmt\.dba\_plans view](AuroraPostgreSQL.Optimize.ViewPlans.md)\. 

### Working with managed statements and the SQL hash<a name="AuroraPostgreSQL.Optimize.Start.ManagedStatements"></a>

A *managed statement* is a SQL statement captured by the optimizer under query plan management\. You specify which SQL statements to capture as managed statements using either manual or automatic capture: 
+ For manual capture, you provide the specific statements to the optimizer as shown in the previous example\.
+ For automatic capture, the optimizer captures plans for statements that run multiple times\. Automatic capture is shown in a later example\.

In the `apg_plan_mgmt.dba_plans` view, you can identify a managed statement with a SQL hash value\. The SQL hash is calculated on a normalized representation of the SQL statement that removes some differences such as the literal values\. Using normalization means that when multiple SQL statements differ only in their literal or parameter values, they are represented by the same SQL hash in the `apg_plan_mgmt.dba_plans` view\. Therefore, there can be multiple plans for the same SQL hash where each plan is optimal under different conditions\. 

When the optimizer processes any SQL statement, it uses the following rules to create the normalized SQL statement: 
+ Removes any leading block comment
+ Removes the EXPLAIN keyword and EXPLAIN options, if present
+ Removes trailing spaces
+ Removes all literals
+ Preserves space and case for readability

For example, take the following statement\.

```
/*Leading comment*/ EXPLAIN SELECT /* Query 1 */ * FROM t WHERE x > 7 AND y = 1; 
```

The optimizer normalizes this statement as the following\.

```
SELECT /* Query 1 */ * FROM t WHERE x > CONST AND y = CONST; 
```

### Working with automatic plan capture<a name="AuroraPostgreSQL.Optimize.Start.AutomaticCapture"></a>

Use automatic plan capture if you want to capture plans for all SQL statements in your application, or if you can’t use manual capture\. With automatic plan capture, the optimizer captures plans for statements that run at least two times\. To use automatic plan capture, do the following\.

1. Create a custom DB parameter group based on the default DB parameter group for the version of Aurora PostgreSQL that you're running\. 

1. Edit the custom DB parameter group, by changing the `apg_plan_mgmt.capture_plan_baselines` setting to `automatic`\. 

1. Save your customized DB parameter group\. 

1. Apply your custom DB parameter group to an Aurora DB instance that is already running as follows:
   + Choose your Aurora PostgreSQL DB instance from the list in the navigation pane, and then choose **Modify**\. 
   + In the **Additional configuration** section of the Modify DB instance page, for the **DB parameter group**, choose your custom DB parameter group\.
   + Choose **Continue**\. Confirm the Summary of modifications and choose **Apply immediately**\. 
   + Choose **Modify DB instance** to apply your custom DB parameter group\.

You can also use your custom DB parameter group when you create a new Aurora PostgreSQL DB instance\. For more information about parameter groups, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 

As your application runs, the optimizer captures plans for any statement that runs more than once\. The optimizer always sets the status of a managed statement's first captured plan to `approved`\. A managed statement's set of approved plans is known as its *plan baseline*\. 

As your application continues to run, the optimizer might find additional plans for the managed statements\. The optimizer sets additional captured plans to a status of `Unapproved`\. 

The set of all captured plans for a managed statement is known as the *plan history*\. Later, you can decide if the `Unapproved` plans perform well and change them to `Approved`, `Rejected`, or `Preferred` by using the `apg_plan_mgmt.evolve_plan_baselines` function or the `apg_plan_mgmt.set_plan_status` function\.

To turn off automatic plan capture, set `apg_plan_mgmt.capture_plan_baselines` to `off` in the parameter group for the DB instance\. Follow the same general process as outlined above, modifying your custom DB parameter group value for `apg_plan_mgmt.capture_plan_baselines` and then applying the custom DB parameter group to your Aurora DB instance\.

For more about plan capture, see [Capturing execution plans](AuroraPostgreSQL.Optimize.CapturePlans.md)\. 

### Validating plans<a name="AuroraPostgreSQL.Optimize.Start.Validate"></a>

Managed plans can become invalid \("stale"\) when objects that they depend on are removed, such as an index\. To find and delete all plans that are stale, use the `apg_plan_mgmt.validate_plans` function\. 

```
SELECT apg_plan_mgmt.validate_plans('delete');
```

For more information, see [Validating plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.ValidatingPlans)\. 

### Approving new plans that improve performance<a name="AuroraPostgreSQL.Optimize.Start.Verify"></a>

While using your managed plans, you can verify whether newer, lower\-cost plans discovered by the optimizer are faster than the minimum\-cost plan already in the plan baseline\. To do the performance comparison and optionally approve the faster plans, call the `apg_plan_mgmt.evolve_plan_baselines` function\. 

The following example automatically approves any unapproved plan that is enabled and faster by at least 10 percent than the minimum\-cost plan in the plan baseline\.

```
SELECT apg_plan_mgmt.evolve_plan_baselines(
   sql_hash,
   plan_hash, 
   1.1,
   'approve'
)
FROM apg_plan_mgmt.dba_plans 
WHERE status = 'Unapproved' AND enabled = true;
```

When the `apg_plan_mgmt.evolve_plan_baselines` function runs, it collects performance statistics and saves them in the `apg_plan_mgmt.dba_plans` view in the columns `planning_time_ms`, `execution_time_ms`, `cardinality_error`, `total_time_benefit_ms`, and `execution_time_benefit_ms`\. The `apg_plan_mgmt.evolve_plan_baselines` function also updates the columns `last_verified` or `last_validated timestamps`, in which you can see the most recent time the performance statistics were collected\.

```
SELECT sql_hash, plan_hash, status, last_verified, sql_text::varchar(40)
FROM apg_plan_mgmt.dba_plans
ORDER BY last_verified DESC;  -- value updated by evolve_plan_baselines()
```

For more information about verifying plans, see [Evaluating plan performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\. 

### Deleting plans<a name="AuroraPostgreSQL.Optimize.Start.Delete"></a>

The optimizer deletes plans automatically if they have not been executed or chosen as the minimum\-cost plan for the plan retention period\. By default, the plan retention period is 32 days\. To change the plan retention period, set the `apg_plan_mgmt.plan_retention_period` parameter\.

You can also review the contents of the `apg_plan_mgmt.dba_plans` view and delete any plans you don't want by using the `apg_plan_mgmt.delete_plan` function\. For more information, see [Deleting plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.DeletingPlans)\. 