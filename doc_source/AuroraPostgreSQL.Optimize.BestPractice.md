# Best practices for query plan management<a name="AuroraPostgreSQL.Optimize.BestPractice"></a>

Query plan management lets you control how and when query execution plans change\. As a DBA, your main goals when using QPM include preventing regressions when there are changes to your database, and controlling when the optimizer can use a new plan\. In the following, you can find some recommended best practices for using query plan management\. Proactive and reactive plan management approaches differ in how and when new plans get approved for use\. 

## Proactive plan management to help prevent performance regression<a name="AuroraPostgreSQL.Optimize.BestPractice.Proactive"></a>

To prevent plan performance regressions from occurring, you gather evidence and then manually approve new plans after you have verified that they are faster\. 

1. In a development environment, identify the SQL statements that have the greatest impact on performance or system throughput\. Then capture the plans for these statements as described in [Manually capturing plans for specific SQL statements](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Manual) and [Automatically capturing plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Export the captured plans from the development environment and import them into the production environment\. For more information, see [Exporting and importing plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.ExportingImporting)\. 

1. In production, run your application and enforce the use of approved managed plans\. For more information, see [Using managed plans](AuroraPostgreSQL.Optimize.UsePlans.md)\. While the application runs, also add new plans as the optimizer discovers them\. For more information, see [Automatically capturing plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Analyze the unapproved plans and approve those that perform well\. For more information, see [Evaluating plan performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\. 

1. While your application continues to run, the optimizer begins to use the new plans as appropriate\.

### Ensuring plan stability after a major version upgrade<a name="AuroraPostgreSQL.Optimize.BestPractice.MajorVersionUpgrade"></a>

Each major version of PostgreSQL includes enhancements and changes to the query optimizer that are designed to improve performance\. However, your workload may include queries that result in a worse performing plan in the new version\. You can use the query plan manager to ensure plan stability after a major version upgrade\.

The optimizer always uses the minimum cost plan, even if more than one approved plans exist\. That means that you can have multiple approved plans for each statement in your workload prior to upgrading\. After the upgrade, the optimizer will use only one of the approved plans, even if the changes in the new major version might lead to a different plan being created\. After upgrading, you can use the `evolve_plan_baselines` function to compare plan performance before and after the upgrade using your query parameter bindings\. The following steps assume that you have been using approved managed plans in your production environment, as detailed in [Using managed plans](AuroraPostgreSQL.Optimize.UsePlans.md)\. 

1. Before upgrading, run your application with the query plan manager running\. While the application runs, add new plans as the optimizer discovers them\. For more information, see [Automatically capturing plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Evaluate each plan's performance\. For more information, see [Evaluating plan performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\.

1. After upgrading, analyze your approved plans again using the `evolve_plan_baselines` function\. Compare performance before and after using your query parameter bindings\. If the new plan is fast, you can add it to your approved plans\. If it's faster than another plan for the same parameter bindings, then you can mark the slower plan as Rejected\. 

   For more information, see [Approving better plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Approving)\. For reference information about this function, see [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines)\. 

For more information, see [Ensuring consistent performance after major version upgrades with Amazon Aurora PostgreSQL\-Compatible Edition Query Plan Management](http://aws.amazon.com/blogs/database/ensuring-consistent-performance-after-major-version-upgrades-with-amazon-aurora-postgresql-query-plan-management/)\. 

## Reactive plan management to detect and repair performance regressions<a name="AuroraPostgreSQL.Optimize.BestPractice.Reactive"></a>

By monitoring your application as it runs, you can detect plans that cause performance regressions\. When you detect regressions, you manually reject or fix the bad plans by following these steps:

1. While your application runs, enforce the use of managed plans and automatically add newly discovered plans as unapproved\. For more information, see [Using managed plans](AuroraPostgreSQL.Optimize.UsePlans.md) and [Automatically capturing plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Monitor your running application for performance regressions\.

1. When you discover a plan regression, set the plan's status to `rejected`\. The next time the optimizer runs the SQL statement, it automatically ignores the rejected plan and uses a different approved plan instead\. For more information, see [Rejecting or disabling slower plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Rejecting)\. 

   In some cases, you might prefer to fix a bad plan rather than reject, disable, or delete it\. Use the `pg_hint_plan` extension to experiment with improving a plan\. With `pg_hint_plan`, you use special comments to tell the optimizer to override how it normally creates a plan\. For more information, see [Fixing plans using pg\_hint\_plan](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.pg_hint_plan)\. 