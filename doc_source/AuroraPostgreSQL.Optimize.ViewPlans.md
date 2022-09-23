# Examining Aurora PostgreSQL query plans in the dba\_plans view<a name="AuroraPostgreSQL.Optimize.ViewPlans"></a>

Database users and administrators that have been granted the `apg_plan_mgmt` role can view and manage the plans stored in the `apg_plan_mgmt.dba_plans`\. An Aurora PostgreSQL DB cluster's administrator \(someone with `rds_superuser` permissions\) must explicitly grant this role to the database users who need to work with query plan management\. 

The `apg_plan_mgmt` view contains the plan history for all managed SQL statements for every database on the writer instance of the Aurora PostgreSQL DB cluster\. This view lets you examine plans, their state, when last used, and all other relevant details\.

As discussed in [Normalization and the SQL hash](AuroraPostgreSQL.Optimize.Start.md#AuroraPostgreSQL.Optimize.Start.hash-and-normalization), each managed plan is identified by the combined SQL hash value and a plan hash value\. With these identifiers, you can use tools such as Amazon RDS Performance Insights to track individual plan performance\. For more information about Performance Insights, see [Using Amazon RDS performance insights]( https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.html)\. 

## Listing managed plans<a name="AuroraPostgreSQL.Optimize.ViewPlans.List"></a>

To list the managed plans, use a SELECT statement on the `apg_plan_mgmt.dba_plans` view\. The following example displays some columns in the `dba_plans` view such as the `status`, which identifies the approved and unapproved plans\.

```
SELECT sql_hash, plan_hash, status, enabled, stmt_name 
FROM apg_plan_mgmt.dba_plans; 

 sql_hash   | plan_hash |   status   | enabled | stmt_name
------------+-----------+------------+---------+------------
 1984047223 | 512153379 | Approved   | t       | rangequery 
 1984047223 | 512284451 | Unapproved | t       | rangequery 
 (2 rows)
```

For readability, the query and the output shown list just a few of the columns from the `dba_plans` view\. For complete information, see [Reference for the apg\_plan\_mgmt\.dba\_plans view](AuroraPostgreSQL.Optimize.dba_plans_view_Reference.md)\. 