# Using Managed Plans<a name="AuroraPostgreSQL.Optimize.UsePlans"></a>

To get the optimizer to use captured plans for your managed statements, set the parameter `apg_plan_mgmt.use_plan_baselines` to `true`\. The following is a local instance example\. 

```
SET apg_plan_mgmt.use_plan_baselines = true;
```

While the application runs, this setting causes the optimizer to use the minimum\-cost, preferred, or approved plan that is valid and enabled, for each managed statement\. 

## How the Optimizer Chooses Which Plan to Run<a name="AuroraPostgreSQL.Optimize.UsePlans.ChoosePlans"></a>

The cost of an execution plan is an estimate that the optimizer makes to compare different plans\. Optimizer cost is a function of several factors that include the CPU and I/O operations that the plan uses\. For more information about PostgreSQL query planner costs, see the [PostgreSQL documentation on query planning](https://www.postgresql.org/docs/current/runtime-config-query.html)\. 

The following flowchart shows how the query plan management optimizer chooses which plan to run\. 

![\[Query Plan Management Workflow\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-QPM-workflow.png)

The flow is as follows:

1. When the optimizer processes every SQL statement, it generates a minimum\-cost plan\. 

1. Without query plan management, the optimizer simply runs its generated plan\. The optimizer uses query plan management if you set one or both of the following parameter settings:
   + `apg_plan_mgmt.capture_plan_baselines` to `manual` or `automatic`
   + `apg_plan_mgmt.use_plan_baselines` to `true`

1. The optimizer immediately runs the generated plan if the following are both true:
   + The optimizer's plan is already in the `apg_plan_mgmt.dba_plans` view for the SQL statement\.
   + The plan's status is either `approved` or `preferred`\.

1. The optimizer goes through the capture plan processing if the parameter `apg_plan_mgmt.capture_plan_baselines` is `manual` or `automatic`\.

   For details on how the optimizer captures plans, see [Capturing Execution Plans](AuroraPostgreSQL.Optimize.CapturePlans.md)\. 

1. The optimizer runs the generated plan if `apg_plan_mgmt.use_plan_baselines` is `false`\.

1. If the optimizer's plan isn't in the `apg_plan_mgmt.dba_plans` view, the optimizer captures the plan as a new `unapproved` plan\.

1. The optimizer runs the generated plan if the following are both true:
   + The optimizer's plan isn't a `rejected` or `disabled` plan\.
   + The plan's total cost is less than the unapproved execution plan threshold\.

   The optimizer doesn't run disabled plans or any plans that have the rejected status\. In most cases, the optimizer doesn't execute unapproved plans\. However, the optimizer runs an unapproved plan if you set a value for the parameter `apg_plan_mgmt.unapproved_plan_execution_threshold` and the plan's total cost is less than the threshold\. For more information, see the [apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.unapproved_plan_execution_threshold) parameter\. 

1. If the managed statement has any enabled and valid preferred plans, the optimizer runs the minimum\-cost one\.

   A valid plan is one that the optimizer can run\. Managed plans can become invalid for various reasons\. For example, plans become invalid when objects that they depend on are removed, such as an index or a partition of a partitioned table\. 

1. The optimizer determines the minimum\-cost plan from the managed statement's approved plans that are both enabled and valid\. The optimizer then runs the minimum\-cost approved plan\. 

## Analyzing Which Plan the Optimizer Will Use<a name="AuroraPostgreSQL.Optimize.UsePlans.AnalyzePlans"></a>

When the `apg_plan_mgmt.use_plan_baselines` parameter is set to `true`, you can use EXPLAIN ANALYZE SQL statements to cause the optimizer to show the plan it would use if it were to run the statement\. The following is an example\.

```
EXPLAIN ANALYZE EXECUTE rangeQuery (1,10000);
                                                    QUERY PLAN           
--------------------------------------------------------------------------
 Aggregate  (cost=393.29..393.30 rows=1 width=8) (actual time=7.251..7.251 rows=1 loops=1)
   ->  Index Only Scan using t1_pkey on t1 t  (cost=0.29..368.29 rows=10000 width=0) (actual time=0.061..4.859 rows=10000 loops=1)
Index Cond: ((id >= 1) AND (id <= 10000))         
         Heap Fetches: 10000
 Planning time: 1.408 ms
 Execution time: 7.291 ms
 Note: An Approved plan was used instead of the minimum cost plan.
 SQL Hash: 1984047223, Plan Hash: 512153379
```

The optimizer indicates which plan it will run, but notice that in this example that it found a lower\-cost plan\. In this case, you capture this new minimum cost plan by turning on automatic plan capture as described in [Automatically Capturing Plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

The optimizer captures new plans as `Unapproved`\. Use the `apg_plan_mgmt.evolve_plan_baselines` function to compare plans and change them to approved, rejected, or disabled\. For more information, see [Evaluating Plan Performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\. 