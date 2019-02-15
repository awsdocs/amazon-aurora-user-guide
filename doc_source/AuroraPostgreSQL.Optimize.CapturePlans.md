# Capturing Execution Plans<a name="AuroraPostgreSQL.Optimize.CapturePlans"></a>

You can capture execution plans for specific SQL statements by using manual plan capture\. Alternatively, you can capture all \(or the slowest, or the most volatile\) plans that are executed two or more times as your application runs by using automatic plan capture\.

When capturing plans, the optimizer sets the status of a managed statement's first captured plan to `approved`\. The optimizer sets the status of any additional plans captured for a managed statement to `unapproved`\. However, more than one plan might occasionally be saved with the `approved` status\. This can happen when multiple plans are created for a statement in parallel and before the first plan for the statement is committed\.

To control the maximum number of plans that can be captured and stored in the `dba_plans` view, set the `apg_plan_mgmt.max_plans` parameter in your DB instance\-level parameter group\. A change to the `apg_plan_mgmt.max_plans` parameter requires a DB instance restart for a new value to take effect\. For more information, see the [apg\_plan\_mgmt\.max\_plans](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.max_plans) parameter\. 

**Topics**
+ [Manually Capturing Plans for Specific SQL Statements](#AuroraPostgreSQL.Optimize.CapturePlans.Manual)
+ [Automatically Capturing Plans](#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)
+ [Using Statistics to Identify SQL Statements](#AuroraPostgreSQL.Optimize.CapturePlans.Statistics)

## Manually Capturing Plans for Specific SQL Statements<a name="AuroraPostgreSQL.Optimize.CapturePlans.Manual"></a>

If you have a known set of SQL statements to manage, put the statements into a SQL script file and then manually capture plans\. The following shows a psql example of how to capture query plans manually for a set of SQL statements\.

```
psql> SET apg_plan_mgmt.capture_plan_baselines = manual;
psql> \i my-statements.sql 
psql> SET apg_plan_mgmt.capture_plan_baselines = off;
```

After capturing a plan for each SQL statement, the optimizer adds a new row to the `apg_plan_mgmt.dba_plans` view\.

We recommend that you use either EXPLAIN or EXPLAIN EXECUTE statements in the SQL script file\. Make sure that you include enough variations in parameter values to capture all the plans of interest\.

If you know of a better plan than the optimizer's minimum cost plan, you might be able to force the optimizer to use the better plan\. To do so, specify one or more optimizer hints\. For more information, see [Fixing Plans Using pg\_hint\_plan](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.pg_hint_plan)\. To compare the performance of the `unapproved` and `approved` plans and approve, reject, or delete them, see [Evaluating Plan Performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\. 

## Automatically Capturing Plans<a name="AuroraPostgreSQL.Optimize.CapturePlans.Automatic"></a>

Use automatic plan capture for situations such as the following:
+ You don't know the specific SQL statements that you want to manage\.
+ You have hundreds or thousands of SQL statements to manage\.
+ Your application uses a client API\. For example, JDBC uses unnamed prepared statements or bulk\-mode statements that cannot be expressed in PostgreSQL SQL\.

**To capture plans automatically**

1. Turn on automatic plan capture by setting `apg_plan_mgmt.capture_plan_baselines` to `automatic` in the DB instance\-level parameter group\. For more information, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 

1. Restart your DB instance\.

1. As the application runs, the optimizer captures plans for each SQL statement that runs at least twice\.

   As the application runs with default query plan management parameter settings, the optimizer captures plans for each SQL statement that runs at least twice\. Capturing all plans while using the defaults has very little run\-time overhead and can be enabled in production\.

   You can modify some parameters to capture plans for statements that have the greatest impact on throughput, run the longest, or have the most volatile performance\. However, this extended mode of automatic plan capture has a noticeable performance overhead\. 

**To turn off automatic plan capture**
+ Set the `apg_plan_mgmt.capture_plan_baselines` parameter to `off` from the DB instance\-level parameter group\.

To measure the performance of the unapproved plans and approve, reject, or delete them, see [Evaluating Plan Performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\. 

You can also identify which SQL statements to automatically capture based on statistical properties of the statements\. For more information, see [Using Statistics to Identify SQL Statements](#AuroraPostgreSQL.Optimize.CapturePlans.Statistics)\. 

## Using Statistics to Identify SQL Statements<a name="AuroraPostgreSQL.Optimize.CapturePlans.Statistics"></a>

With automatic plan capture, you can identify the SQL statements that you want to manage by their statistical properties\. To access SQL statements' statistics, install the `pg_stat_statements` extension\. 

Query plan management provides parameters that access these statistics\. You set these parameters to supply criteria to the optimizer so it can identify which SQL statements to manage\. During automatic plan capture, the optimizer captures plans for statements that meet the statistical criteria\.

Use the following parameters to define statistical criteria for the SQL statements that you want to manage\.


****  

| Parameter | Description | 
| --- | --- | 
| apg\_plan\_mgmt\.pgss\_min\_calls | How many times the statement ran\. For more information, see the [apg\_plan\_mgmt\.pgss\_min\_calls](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.pgss_min_calls) parameter\.  | 
| apg\_plan\_mgmt\.pgss\_min\_total\_time\_ms  | The statement's total execution time\. For more information, see the [apg\_plan\_mgmt\.pgss\_min\_total\_time\_ms](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.pgss_min_total_time_ms) parameter\.  | 
| apg\_plan\_mgmt\.pgss\_min\_mean\_time\_ms | The statement's average execution time\. For more information, see the [apg\_plan\_mgmt\.pgss\_min\_mean\_time\_ms](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.pgss_min_mean_time_ms) parameter\.  | 
| apg\_plan\_mgmt\.pgss\_min\_stddev\_time\_ms | The statement's standard deviation of execution time\. For more information, see the [apg\_plan\_mgmt\.pgss\_min\_stddev\_time\_ms](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.pgss_min_stddev_time_ms) parameter\. | 

**Note**  
Your application's performance is affected if you use these statistics to identify which SQL statements to manage\.

Before you can use statistics to identify which statements you want to manage, you need to install the `pg_stat_statements` extension\. For installation and other details, see the [PostgreSQL pg\_stats\_statements documentation](https://www.postgresql.org/docs/current/pgstatstatements.html)\. 

The following procedure shows how to identify SQL statements to manage based on statistical criteria and capture plans automatically for all matching statements\. 

**To capture plans based on SQL statement statistics**
**Note**  
Set the following `apg_plan_mgmt` parameters in the parameter group for your DB instance, then restart your DB instance\. For more about parameter groups, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 

1. Turn on automatic plan capture by setting the `apg_plan_mgmt.capture_plan_baselines` parameter to `automatic`\.

1. Set `apg_plan_mgmt.use_plan_baselines` to `true` to enforce the optimizer to use managed plans as more plans continue to be captured\. Set a value of `false` if you only want to capture plans and not use them yet\.

1. Set the statistical criteria to identify which SQL statements that you want to manage\.

   For example, setting `apg_plan_mgmt.pgss_min_calls` to 3 tells the optimizer to save plans only for statements that run at least 3 times\. Setting `apg_plan_mgmt.pgss_min_total_time_ms` to 30000 tells the optimizer to save plans for statements that run for 30 seconds or more\.

1. Restart your DB instance\.

1. Enable the `pg_stat_statements` extension to make the statistics for the SQL statements available for this DB instance\. 

   ```
   CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
   ```

As the application runs, the optimizer captures plans for each matching statement\.