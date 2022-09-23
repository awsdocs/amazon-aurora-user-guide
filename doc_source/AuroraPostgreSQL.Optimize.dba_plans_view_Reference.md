# Reference for the apg\_plan\_mgmt\.dba\_plans view<a name="AuroraPostgreSQL.Optimize.dba_plans_view_Reference"></a>

The columns of plan information in the `apg_plan_mgmt.dba_plans` view include the following\.


| dba\_plans column | Description | 
| --- | --- | 
| cardinality\_error |  A measure of the error between the estimated cardinality versus the actual cardinality\. *Cardinality* is the number of table rows that the plan is to process\. If the cardinality error is large, then it increases the likelihood that the plan isn't optimal\. This column is populated by the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\.   | 
| compatibility\_level |  The feature level of the Aurora PostgreSQL optimizer\.  | 
| created\_by | The authenticated user \(session\_user\) who created the plan\. | 
| enabled |  An indicator of whether the plan is enabled or disabled\. All plans are enabled by default\. You can disable plans to prevent them from being used by the optimizer\. To modify this value, use the [apg\_plan\_mgmt\.set\_plan\_enabled](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.set_plan_enabled) function\.   | 
| environment\_variables |  The PostgreSQL Grand Unified Configuration \(GUC\) parameters and values that the optimizer has overridden at the time the plan was captured\.   | 
| estimated\_startup\_cost | The estimated optimizer setup cost before the optimizer delivers rows of a table\. | 
| estimated\_total\_cost | The estimated optimizer cost to deliver the final table row\. | 
| execution\_time\_benefit\_ms | The execution time benefit in milliseconds of enabling the plan\. This column is populated by the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\.  | 
| execution\_time\_ms | The estimated time in milliseconds that the plan would run\. This column is populated by the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\.  | 
| has\_side\_effects | A value that indicates that the SQL statement is a data manipulation language \(DML\) statement or a SELECT statement that contains a VOLATILE function\.  | 
| last\_used | This value is updated to the current date whenever the plan is either executed or when the plan is the query optimizer's minimum\-cost plan\. This value is stored in shared memory and periodically flushed to disk\. To get the most up\-to\-date value, read the date from shared memory by calling the function apg\_plan\_mgmt\.plan\_last\_used\(sql\_hash, plan\_hash\) instead of reading the last\_used value\. For additional information, see the [apg\_plan\_mgmt\.plan\_retention\_period](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.plan_retention_period) parameter\.  | 
| last\_validated | The most recent date and time when it was verified that the plan could be recreated by either the [apg\_plan\_mgmt\.validate\_plans](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.validate_plans) function or the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\. | 
| last\_verified | The most recent date and time when a plan was verified to be the best\-performing plan for the specified parameters by the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\.  | 
| origin |  How the plan was captured with the [apg\_plan\_mgmt\.capture\_plan\_baselines](AuroraPostgreSQL.Optimize.Parameters.md#AuroraPostgreSQL.Optimize.Parameters.capture_plan_baselines) parameter\. Valid values include the following:  `M` – The plan was captured with manual plan capture\. `A` – The plan was captured with automatic plan capture\.  | 
| param\_list |  The parameter values that were passed to the statement if this is a prepared statement\.  | 
| plan\_created | The date and time the plan that was created\. | 
| plan\_hash | The plan identifier\. The combination of plan\_hash and sql\_hash uniquely identifies a specific plan\. | 
| plan\_outline | A representation of the plan that is used to recreate the actual execution plan, and that is database\-independent\. Operators in the tree correspond to operators that appear in the EXPLAIN output\. | 
| planning\_time\_ms |  The actual time to run the planner, in milliseconds\. This column is populated by the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\.   | 
| queryId | A statement hash, as calculated by the pg\_stat\_statements extension\. This isn't a stable or database\-independent identifier because it depends on object identifiers \(OIDs\)\.  | 
| sql\_hash | A hash value of the SQL statement text, normalized with literals removed\. | 
| sql\_text | The full text of the SQL statement\. | 
| status |  A plan's status, which determines how the optimizer uses a plan\. Valid values include the following\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Optimize.dba_plans_view_Reference.html)  | 
| stmt\_name | The name of the SQL statement within a PREPARE statement\. This value is an empty string for an unnamed prepared statement\. This value is NULL for a nonprepared statement\. | 
| total\_time\_benefit\_ms |  The total time benefit in milliseconds of enabling this plan\. This value considers both planning time and execution time\. If this value is negative, there is a disadvantage to enabling this plan\. This column is populated by the [apg\_plan\_mgmt\.evolve\_plan\_baselines](AuroraPostgreSQL.Optimize.Functions.md#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines) function\.   | 