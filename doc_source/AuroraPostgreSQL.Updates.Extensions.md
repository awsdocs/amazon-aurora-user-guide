# Extension Versions for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.Extensions"></a>

**Topics**
+ [The apg\_plan\_mgmt Extension Version 2\.0](#AuroraPostgreSQL.Updates.Extensions.apg_plan_mgmt.20)
+ [The apg\_plan\_mgmt Extension Version 1\.0\.1](#AuroraPostgreSQL.Updates.Extensions.apg_plan_mgmt.101)

## The apg\_plan\_mgmt Extension Version 2\.0<a name="AuroraPostgreSQL.Updates.Extensions.apg_plan_mgmt.20"></a>

You use the `apg_plan_mgmt` extension with query plan management\. For more about how to install, upgrade, and use the `apg_plan_mgmt` extension, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

The `apg_plan_mgmt` extension changes for version 2\.0 include the following:

**New Extension Features**

1. You can now manage all queries inside SQL functions, whether they have parameters or not\.

1. You can now manage all queries inside PL/pgSQL functions, whether they have parameters or not\.

1. You can now manage queries in generic plans, whether they have parameters or not\. To learn more about generic plans versus custom plans, see the `PREPARE` statement in the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/sql-prepare.html)\. 

1. You can now use query plan management to enforce the use of specific types of aggregate methods in query plans\.

**Extension Improvements**

1. You can now save planswith a size up to 8KB times the setting of the `max_worker_processes` parameter\. Previously the maximum plan size was 8KB\.

1. Fixed bugs for unnamed prepared statements such as those from JDBC\.

1. Previously, when you tried to do `CREATE EXTENSION apg_plan_mgmt` when it is not loaded in the `shared_preload_libraries`, the backend connection would be dropped\. Now, an error message prints and the connection is not dropped\.

1. The default value of the `cardinality_error` in the `apg_plan_mgmt.plans table` is NULL, but it can be set to \-1 during the `apg_plan_mgmt.evolve_plan_baselines` function\. NULL is now used consistently\.

1. Plans are now saved for queries that refer to temporary tables\.

1. The default maximum number of plans is increased from 1000 to 10000\.

1. The following pgss parameters are deprecated because the automatic plan capture mode should be used instead of those parameters\.
   + `apg_plan_mgmt.pgss_min_calls`
   + `apg_plan_mgmt.pgss_min_mean_time_ms`
   + `apg_plan_mgmt.pgss_min_stddev_time_ms`
   + `apg_plan_mgmt.pgss_min_total_time_ms`

## The apg\_plan\_mgmt Extension Version 1\.0\.1<a name="AuroraPostgreSQL.Updates.Extensions.apg_plan_mgmt.101"></a>

The `apg_plan_mgmt` extension changes for version 1\.0\.1 include the following:

**New Extension Features**

1. A new `update_plan_hash` parameter is available for the `validate_plans` function\. This parameter updates the `plan_hash` for plans that can't be reproduced exactly\. The `update_plan_hash` parameter also enables you to fix a plan by rewriting the SQL\. You can then register the good plan as an `Approved` plan for the original SQL\. Following is an example of using `update_plan_hash`\.

   ```
   UPDATE apg_plan_mgmt.dba_plans SET plan_hash = new _plan_hash, plan_outline = good_plan_outline 
      WHERE sql_hash = bad_plan_sql_hash AND plan_hash = bad_plan_plan_hash;
   SELECT apg_plan_mgmt.validate_plans(bad_plan_sql_hash, bad_plan_plan_hash, 'update_plan_hash');
   SELECT apg_plan_mgmt.reload();
   ```

1. A new `get_explain_stmt` function is available that generates the text of an `EXPLAIN` statement for the specified SQL statement\. It includes the parameters `sql_hash`, `plan_hash` and `explain_options`\. 

   The parameter `explain_options` can be any comma\-separated list of valid `EXPLAIN` options, as shown following\.

   ```
   analyze,verbose,buffers,hashes,format json
   ```

   If the parameter `explain_options` is NULL or an empty string, the `get_explain_stmt` function generates a simple `EXPLAIN` statement\. 

   To create an `EXPLAIN` script for your workload or a portion of it, use the `\a` , `\t`, and `\o` options to redirect the output to a file\. For example, you can create an `EXPLAIN` script for the top\-ranked \(top\-K\) statements by using the PostgreSQL `pg_stat_statements` view sorted by `total_time` in `DESC` order\.

1. The precise location of the Gather parallel query operator is determined by costing, and may change slightly over time\. To prevent these differences from invalidating the entire plan, query plan management now computes the same `plan_hash` even if the Gather operators move to different places in the plan tree\.

1. Support is added for nonparameterized statements inside pl/pgsql functions\.

1. Overhead is reduced when the `apg_plan_mgmt` extension is installed on multiple databases in the same cluster while two or more databases are being accessed concurrently\. Also, this release fixed a bug in this area that caused plans to not be stored in shared memory\.

**Extension Improvements**

1. Improvements to the `evolve_plan_baselines` function\.

   1. The `evolve_plan_baselines` function now computes a `cardinality_error` metric over all nodes in the plan\. Using this metric, you can identify any plan where the cardinality estimation error is large, and the plan quality is more doubtful\. Long\-running statements with high `cardinality_error` values are high\-priority candidates for query tuning\.

   1. Reports generated by `evolve_plan_baselines` now include `sql_hash`, `plan_hash`, and the plan `status`\.

   1. You can now allow `evolve_plan_baselines` to approve previously `Rejected` plans\.

   1. The meaning of `speedup_factor` for `evolve_plan_baselines` is now always relative to the baseline plan\. For example, a value of 1\.1 now means 10 percent faster than the baseline plan\. A value of 0\.9 means 10 percent slower than the baseline plan\. The comparison is made using execution time alone instead of total time\.

   1. The `evolve_plan_baselines` function now warms the cache in a new way\. It does this by running the baseline plan, then running the baseline plan one more time, and then running the candidate plan once\. Previously, `evolve_plan_baselines` ran the candidate plan twice\. This approach added significantly to execution time, especially for slow candidate plans\. However, running the candidate plan twice is more reliable when the candidate plan uses an index that isn't used in the baseline plan\.

1. Query plan management no longer saves plans that refer to system tables or views, temporary tables, or the query plan management's own tables\.

1. Bug fixes include caching a plan immediately when saved and fixing a bug that caused the back end to terminate\.