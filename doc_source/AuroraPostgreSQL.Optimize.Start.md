# Understanding Aurora PostgreSQL query plan management<a name="AuroraPostgreSQL.Optimize.Start"></a>

With query plan management turned on for your Aurora PostgreSQL DB cluster, the optimizer generates and stores query execution plans for any SQL statement that it processes more than once\. The optimizer always sets the status of a managed statement's first generated plan to `Approved`, and stores it in the `dba_plans` view\. 

The set of approved plans saved for a managed statement is known as its *plan baseline*\. As your application runs, the optimizer might generate additional plans for managed statements\. The optimizer sets additional captured plans to a status of `Unapproved`\. 

Later, you can decide if the `Unapproved` plans perform well and change them to `Approved`, `Rejected`, or `Preferred`\. To do so, you use the `apg_plan_mgmt.evolve_plan_baselines` function or the `apg_plan_mgmt.set_plan_status` function\. 

When the optimizer generates a plan for a SQL statement, query plan management saves the plan in the `apg_plan_mgmt.plans` table\. Database users that have been granted the `apg_plan_mgmt` role can see the plan details by querying the `apg_plan_mgmt.dba_plans` view\. For example, the following query lists details for plans currently in the view for a non\-production Aurora PostgreSQL DB cluster\.
+ `sql_hash` – An identifier for the SQL statement that's the hash value for the normalized text of the SQL statement\.
+ `plan_hash` – A unique identifier for the plan that's a combination of the `sql_hash` and a hash of the plan\.
+ `status` – The status of the plan\. The optimizer can run an approved plan\.
+ `enabled` – Indicates whether the plan is ready to use \(true\) or not \(false\)\.
+ `plan_outline` – A representation of the plan that's used to recreate the actual execution plan\. Operators in the tree structure map to operators in EXPLAIN output\.

The `apg_plan_mgmt.dba_plans` view has many more columns that contain all details of the plan, such as when the plan was last used\. For complete details, see [Reference for the apg\_plan\_mgmt\.dba\_plans view](AuroraPostgreSQL.Optimize.dba_plans_view_Reference.md)\. 

## Normalization and the SQL hash<a name="AuroraPostgreSQL.Optimize.Start.hash-and-normalization"></a>

In the `apg_plan_mgmt.dba_plans` view, you can identify a managed statement by its SQL hash value\. The SQL hash is calculated on a normalized representation of the SQL statement that removes some differences, such as literal values\. 

The *normalization* process for each SQL statement preserves space and case, so that you can still read and understand the gist of the SQL statement\. Normalization removes or replaces the following items\.
+ Leading block comments
+ The EXPLAIN keyword and EXPLAIN options, and EXPLAIN ANALYZE
+ Trailing spaces
+ All literals

As an example, take the following statement\.

```
/*Leading comment*/ EXPLAIN SELECT /* Query 1 */ * FROM t WHERE x > 7 AND y = 1; 
```

The optimizer normalizes this statement as shown following\.

```
SELECT /* Query 1 */ * FROM t WHERE x > CONST AND y = CONST; 
```

Normalization allows the same SQL hash to be used for similar SQL statements that might differ only in their literal or parameter values\. In other words, multiple plans for the same SQL hash can exist, with a different plan that's optimal under different conditions\.

**Note**  
A single SQL statement that's used with different schemas has different plans because it's bound to the specific schema at runtime\. The planner uses the statistics for schema binding to choose the optimal plan\.

To learn more about how the optimizer chooses a plan, see [Using Aurora PostgreSQL managed plans](AuroraPostgreSQL.Optimize.UsePlans.md)\. In that section, you can learn how to use `EXPLAIN` and `EXPLAIN ANALYZE` to preview a plan before it's actually used\. For details, see [Analyzing the optimizer's chosen plan](AuroraPostgreSQL.Optimize.UsePlans.md#AuroraPostgreSQL.Optimize.UsePlans.AnalyzePlans)\. For an image that outlines the process for choosing a plan, see [How the optimizer chooses which plan to run](AuroraPostgreSQL.Optimize.UsePlans.md#AuroraPostgreSQL.Optimize.UsePlans.ChoosePlans)\. 