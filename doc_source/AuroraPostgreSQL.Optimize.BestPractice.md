# Best Practices for Query Plan Management<a name="AuroraPostgreSQL.Optimize.BestPractice"></a>

Consider using a plan management style that is either proactive or reactive\. These plan management styles contrast in how and when new plans get approved for use\. 

## Proactive Plan Management to Help Prevent Performance Regression<a name="AuroraPostgreSQL.Optimize.BestPractice.Proactive"></a>

With proactive plan management, you manually approve new plans after you have verified that they are faster\. Do this to prevent plan performance regressions\. Follow these steps for proactive plan management:

1. In a development environment, identify the SQL statements that have the greatest impact on performance or system throughput\. Then capture the plans for these statements as described in [Manually Capturing Plans for Specific SQL Statements](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Manual) and [Automatically Capturing Plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Export the captured plans from the development environment and import them into the production environment\. For more information, see [Exporting and Importing Plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.ExportingImporting)\. 

1. In production, run your application and enforce the use of approved managed plans\. For more information, see [Using Managed Plans](AuroraPostgreSQL.Optimize.UsePlans.md)\. While the application runs, also add new plans as the optimizer discovers them\. For more information, see [Automatically Capturing Plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Analyze the unapproved plans and approve those that perform well\. For more information, see [Evaluating Plan Performance](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance)\. 

1. While your application continues to run, the optimizer begins to use the new plans as appropriate\.

## Reactive Plan Management to Detect and Repair Performance Regression<a name="AuroraPostgreSQL.Optimize.BestPractice.Reactive"></a>

With reactive plan management, you monitor your application as it runs to detect plans that cause performance regressions\. When you detect regressions, you manually reject or fix the bad plans\. Follow these steps for reactive plan management:

1. While your application runs, enforce the use of managed plans and automatically add newly discovered plans as unapproved\. For more information, see [Using Managed Plans](AuroraPostgreSQL.Optimize.UsePlans.md) and [Automatically Capturing Plans](AuroraPostgreSQL.Optimize.CapturePlans.md#AuroraPostgreSQL.Optimize.CapturePlans.Automatic)\. 

1. Monitor your running application for performance regressions\.

1. When you discover a plan regression, set the plan's status to `rejected`\. The next time the optimizer runs the SQL statement, it automatically ignores the rejected plan and uses a different approved plan instead\. For more information, see [Rejecting or Disabling Slower Plans](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.EvaluatingPerformance.Rejecting)\. 

   In some cases, you might prefer to fix a bad plan rather than reject, disable, or delete it\. Use the `pg_hint_plan` extension to experiment with improving a plan\. With `pg_hint_plan`, you use special comments to tell the optimizer to override how it normally creates a plan\. For more information, see [Fixing Plans Using pg\_hint\_plan](AuroraPostgreSQL.Optimize.Maintenance.md#AuroraPostgreSQL.Optimize.Maintenance.pg_hint_plan)\. 