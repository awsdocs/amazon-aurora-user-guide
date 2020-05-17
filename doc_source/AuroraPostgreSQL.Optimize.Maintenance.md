# Maintaining Execution Plans<a name="AuroraPostgreSQL.Optimize.Maintenance"></a>

Query plan management provides techniques and functions to add, maintain, and improve execution plans\.

**Topics**
+ [Evaluating Plan Performance](#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)
+ [Validating Plans](#AuroraPostgreSQL.Optimize.Maintenance.ValidatingPlans)
+ [Fixing Plans Using pg\_hint\_plan](#AuroraPostgreSQL.Optimize.Maintenance.pg_hint_plan)
+ [Deleting Plans](#AuroraPostgreSQL.Optimize.Maintenance.DeletingPlans)
+ [Exporting and Importing Plans](#AuroraPostgreSQL.Optimize.Maintenance.ExportingImporting)

## Evaluating Plan Performance<a name="AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance"></a>

After the optimizer captures plans as unapproved, use the `apg_plan_mgmt.evolve_plan_baselines` function to compare plans based on their actual performance\. Depending on the outcome of your performance experiments, you can change a plan's status from unapproved to either approved or rejected\. You can instead decide to use the `apg_plan_mgmt.evolve_plan_baselines` function to temporarily disable a plan if it does not meet your requirements\. 

**Topics**
+ [Approving Better Plans](#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Approving)
+ [Rejecting or Disabling Slower Plans](#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Rejecting)

### Approving Better Plans<a name="AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Approving"></a>

The following example demonstrates how to change the status of managed plans to approved using the `apg_plan_mgmt.evolve_plan_baselines` function\. 

```
SELECT apg_plan_mgmt.evolve_plan_baselines (
   sql_hash, 
   plan_hash, 
   min_speedup_factor := 1.0, 
   action := 'approve'
) 
FROM apg_plan_mgmt.dba_plans WHERE status = 'Unapproved';
                
NOTICE:     rangequery (1,10000)
NOTICE:     Baseline   [ Planning time 0.761 ms, Execution time 13.261 ms]
NOTICE:     Baseline+1 [ Planning time 0.204 ms, Execution time 8.956 ms]
NOTICE:     Total time benefit: 4.862 ms, Execution time benefit: 4.305 ms
NOTICE:     Unapproved -> Approved
evolve_plan_baselines 
-----------------------
0
(1 row)
```

The output shows a performance report for the `rangequery` statement with parameter bindings of 1 and 10,000\. The new unapproved plan \(`Baseline+1`\) is better than the best previously approved plan \(`Baseline`\)\. To confirm that the new plan is now `Approved`, check the `apg_plan_mgmt.dba_plans` view\. 

```
SELECT sql_hash, plan_hash, status, enabled, stmt_name 
FROM apg_plan_mgmt.dba_plans;
                
sql_hash  | plan_hash |  status  | enabled | stmt_name  
------------+-----------+----------+---------+------------
1984047223 | 512153379 | Approved | t       | rangequery
1984047223 | 512284451 | Approved | t       | rangequery
(2 rows)
```

The managed plan now includes two approved plans that are the statement's plan baseline\. You can also call the `apg_plan_mgmt.set_plan_status` function to directly set a plan's status field to `'Approved'`, `'Rejected'`, `'Unapproved'`, or `'Preferred'`\. 

### Rejecting or Disabling Slower Plans<a name="AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Rejecting"></a>

To reject or disable plans, pass `'reject'` or `'disable' `as the action parameter to the `apg_plan_mgmt.evolve_plan_baselines` function\. This example disables any captured `Unapproved` plan that is slower by at least 10 percent than the best `Approved` plan for the statement\. 

```
SELECT apg_plan_mgmt.evolve_plan_baselines(
sql_hash,  -- The managed statement ID
plan_hash, -- The plan ID
1.1,       -- number of times faster the plan must be 
'disable'  -- The action to take. This sets the enabled field to false.
)
FROM apg_plan_mgmt.dba_plans
WHERE status = 'Unapproved' AND   -- plan is Unapproved
origin = 'Automatic';       -- plan was auto-captured
```

You can also directly set a plan to rejected or disabled\. To directly set a plan's enabled field to `true` or `false`, call the `apg_plan_mgmt.set_plan_enabled` function\. To directly set a plan's status field to `'Approved'`, `'Rejected'`, `'Unapproved'`, or `'Preferred'`, call the `apg_plan_mgmt.set_plan_status` function\.

## Validating Plans<a name="AuroraPostgreSQL.Optimize.Maintenance.ValidatingPlans"></a>

Use the `apg_plan_mgmt.validate_plans` function to delete or disable plans that are invalid\.

Plans can become invalid or stale when objects that they depend on are removed, such as an index or a table\. However, a plan might be invalid only temporarily if the removed object gets recreated\. If an invalid plan can become valid later, you might prefer to disable an invalid plan or do nothing rather than delete it\. 

To find and delete all plans that are invalid and haven't been used in the past week, use the `apg_plan_mgmt.validate_plans `function as follows\.

```
SELECT apg_plan_mgmt.validate_plans(sql_hash, plan_hash, 'delete') 
FROM apg_plan_mgmt.dba_plans
WHERE last_used < (current_date - interval '7 days');
```

To enable or disabled a plan directly, use the `apg_plan_mgmt.set_plan_enabled` function\.

## Fixing Plans Using pg\_hint\_plan<a name="AuroraPostgreSQL.Optimize.Maintenance.pg_hint_plan"></a>

The query optimizer is well\-designed to find an optimal plan for all statements, and in most cases the optimizer finds a good plan\. However, occasionally you might know that a much better plan exists than that generated by the optimizer\. Two recommended ways to get the optimizer to generate a desired plan include using the `pg_hint_plan` extension or setting Grand Unified Configuration \(GUC\) variables in PostgreSQL:
+ `pg_hint_plan` extension – Specify a “hint” to modify how the planner works by using PostgreSQL's `pg_hint_plan` extension\. To install and learn more about how to use the `pg_hint_plan` extension, see the [pg\_hint\_plan documentation](http://pghintplan.osdn.jp/)\.
+ GUC variables – Override one or more cost model parameters or other optimizer parameters, such as the `from_collapse_limit` or `GEQO_threshold`\. 

When you use one of these techniques to force the query optimizer to use a plan, you can also use query plan management to capture and enforce use of the new plan\.

You can use the `pg_hint_plan` extension to change the join order, the join methods, or the access paths for a SQL statement\. You use a SQL comment with special `pg_hint_plan` syntax to modify how the optimizer creates a plan\. For example, assume the problem SQL statement has a two\-way join\. 

```
SELECT * 
FROM t1, t2 
WHERE t1.id = t2.id;
```

Then suppose that the optimizer chooses the join order \(t1, t2\), but we know that the join order \(t2, t1\) is faster\. The following hint forces the optimizer to use the faster join order, \(t2, t1\)\. Include EXPLAIN so that the optimizer generates a plan for the SQL statement but does not run the statement\. \(Output not shown\.\)

```
/*+ Leading ((t2 t1)) */ EXPLAIN SELECT * 
FROM t1, t2 
WHERE t1.id = t2.id;
```

The following steps show how to use `pg_hint_plan`\.

**To modify the optimizer's generated plan and capture the plan using pg\_hint\_plan**

1. Turn on the manual capture mode\.

   ```
   SET apg_plan_mgmt.capture_plan_baselines = manual;
   ```

1. Specify a hint for the SQL statement of interest\. 

   ```
   /*+ Leading ((t2 t1)) */ EXPLAIN SELECT * 
   FROM t1, t2 
   WHERE t1.id = t2.id;
   ```

   After this runs, the optimizer captures the plan in the `apg_plan_mgmt.dba_plans` view\. The captured plan doesn't include the special `pg_hint_plan` comment syntax because query plan management normalizes the statement by removing leading comments\. 

1. View the managed plans by using the `apg_plan_mgmt.dba_plans` view\.

   ```
   SELECT sql_hash, plan_hash, status, sql_text, plan_outline 
   FROM apg_plan_mgmt.dba_plans;
   ```

1. Set the status of the plan to `Preferred`\. Doing so makes sure that the optimizer chooses to run it, instead of selecting from the set of approved plans, when the minimum\-cost plan isn't already `Approved` or `Preferred`\.

   ```
   SELECT apg_plan_mgmt.set_plan_status(<sql-hash>, <plan-hash>, 'preferred' ); 
   ```

1. Turn off manual plan capture and enforce the use of managed plans\.

   ```
   SET apg_plan_mgmt.capture_plan_baselines = false;
   SET apg_plan_mgmt.use_plan_baselines = true;
   ```

   Now, when the original SQL statement runs, the optimizer chooses either an `Approved` or `Preferred` plan\. If the minimum\-cost plan isn't `Approved` or `Preferred`, then the optimizer chooses the `Preferred` plan\.

## Deleting Plans<a name="AuroraPostgreSQL.Optimize.Maintenance.DeletingPlans"></a>

Delete plans that have not been used for a long time or that are no longer relevant\. Each plan has a `last_used` date that the optimizer updates each time it executes a plan or picks it as the minimum\-cost plan for a statement\. Use the `last_used` date to determine if a plan has been used recently and is still relevant\. 

For example, you can use the `apg_plan_mgmt.delete_plan` function as follows\. Doing this deletes all plans that haven't been chosen as the minimum\-cost plan or haven't run in at least 31 days\. However, this example doesn't delete plans that have been explicitly rejected\.

```
SELECT SUM(apg_plan_mgmt.delete_plan(sql_hash, plan_hash))
FROM apg_plan_mgmt.dba_plans 
WHERE last_used < (current_date - interval '31 days') 
AND status <> 'Rejected';
```

To delete any plan that is no longer valid and that you expect not to become valid again, use the `apg_plan_mgmt.validate_plans` function\. For more information, see [Validating Plans](#AuroraPostgreSQL.Optimize.Maintenance.ValidatingPlans)\. 

You can implement your own policy for deleting plans\. Plans are automatically deleted when the current date `last_used` is greater than the value of the `apg_plan_mgmt.plan_retention_period` parameter, which defaults to 32 days\. You can specify a longer interval, or you can implement your own plan retention policy by calling the `delete_plan` function directly\.

**Important**  
If you don't clean up plans, you might eventually run out of shared memory that is set aside for query plan management\. To control how much memory is available for managed plans, use the `apg_plan_mgmt.max_plans` parameter\. Set this parameter in your DB instance\-level parameter group and restart your DB instance for changes to take effect\. For more information, see the [apg\_plan\_mgmt\.max\_plans](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.max_plans) parameter\. 

## Exporting and Importing Plans<a name="AuroraPostgreSQL.Optimize.Maintenance.ExportingImporting"></a>

You can export your managed plans and import them into another DB instance\. 

**To export managed plans**  
An authorized user can copy any subset of the `apg_plan_mgmt.plans` table to another table, and then save it using the `pg_dump` command\. The following is an example\.

```
CREATE TABLE plans_copy AS SELECT * 
FROM apg_plan_mgmt.plans [ WHERE predicates ] ;
% pg_dump --table apg_plan_mgmt.plans_copy -Ft mysourcedatabase > plans_copy.tar
DROP TABLE apg_plan_mgmt.plans_copy;
```

**To import managed plans**

1. Copy the \.tar file of the exported managed plans to the system where the plans are to be restored\.

1. Use the `pg_restore` command to copy the tar file into a new table\. 

   ```
   % pg_restore --dbname mytargetdatabase -Ft plans_copy.tar
   ```

1. Merge the `plans_copy` table with the `apg_plan_mgmt.plans` table, as shown in the following example\.
**Note**  
In some cases, you might dump from one version of the `apg_plan_mgmt` extension and restore into a different version\. In these cases, the columns in the plans table might be different\. If so, name the columns explicitly instead of using SELECT \*\. 

   ```
   INSERT INTO apg_plan_mgmt.plans SELECT * FROM plans_copy
    ON CONFLICT ON CONSTRAINT plans_pkey
    DO UPDATE SET
    status = EXCLUDED.status,
    enabled = EXCLUDED.enabled,
    -- Save the most recent last_used date 
    --
    last_used = CASE WHEN EXCLUDED.last_used > plans.last_used 
    THEN EXCLUDED.last_used ELSE plans.last_used END, 
    -- Save statistics gathered by evolve_plan_baselines, if it ran:
    --
    estimated_startup_cost = EXCLUDED.estimated_startup_cost,
    estimated_total_cost = EXCLUDED.estimated_total_cost,
    planning_time_ms = EXCLUDED.planning_time_ms,
    execution_time_ms = EXCLUDED.execution_time_ms,
    total_time_benefit_ms = EXCLUDED.total_time_benefit_ms, 
    execution_time_benefit_ms = EXCLUDED.execution_time_benefit_ms;
   ```

1. Reload the managed plans into shared memory and remove the temporary plans table\.

   ```
   SELECT apg_plan_mgmt.reload(); -- refresh shared memory
   DROP TABLE plans_copy;
   ```