# Parameter reference for query plan management<a name="AuroraPostgreSQL.Optimize.Parameters"></a>

The `apg_plan_mgmt` extension provides the following parameters\.

**Topics**
+ [apg\_plan\_mgmt\.capture\_plan\_baselines](#AuroraPostgreSQL.Optimize.Parameters.capture_plan_baselines)
+ [apg\_plan\_mgmt\.max\_databases](#AuroraPostgreSQL.Optimize.Parameters.max_databases)
+ [apg\_plan\_mgmt\.max\_plans](#AuroraPostgreSQL.Optimize.Parameters.max_plans)
+ [apg\_plan\_mgmt\.plan\_retention\_period](#AuroraPostgreSQL.Optimize.Parameters.plan_retention_period)
+ [apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold](#AuroraPostgreSQL.Optimize.Parameters.unapproved_plan_execution_threshold)
+ [apg\_plan\_mgmt\.use\_plan\_baselines](#AuroraPostgreSQL.Optimize.Parameters.use_plan_baselines)

Set the query plan management parameters at the appropriate level:
+ Set at the cluster\-level to provide the same settings for all DB instances\. For more information, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\. 
+ Set at the DB instance level to isolate the settings to an individual DB instance\. For more information, see [Modifying parameters in a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.
+ Set in a specific client session such as in psql, to isolate the values to only that session\.

You must be set the parameters `apg_plan_mgmt.max_databases` and `apg_plan_mgmt.max_plans` at the cluster or DB instance level\. 

## apg\_plan\_mgmt\.capture\_plan\_baselines<a name="AuroraPostgreSQL.Optimize.Parameters.capture_plan_baselines"></a>

Enable execution plan capture for SQL statements\.

```
SET apg_plan_mgmt.capture_plan_baselines = [off | automatic |manual];
```


****  

| Value | Description | 
| --- | --- | 
| off | Disable plan capture\. This is the default\. | 
| automatic | Enable plan capture for subsequent SQL statements that satisfy the eligibility criteria\. | 
| manual | Enable plan capture for subsequent SQL statements\. | 

## apg\_plan\_mgmt\.max\_databases<a name="AuroraPostgreSQL.Optimize.Parameters.max_databases"></a>

Sets the maximum number of database objects that might use query plan management\. A database object is what gets created with the CREATE DATABASE SQL statement\. 

**Important**  
Set `apg_plan_mgmt.max_databases` at the cluster or DB instance level\. It requires a DB instance restart for a new value to take effect\.

```
SET apg_plan_mgmt.max_databases = integer-value;
```


****  

| Value | Default | Description | 
| --- | --- | --- | 
| Positive integer  | 10 | A positive integer value\.  | 

 

## apg\_plan\_mgmt\.max\_plans<a name="AuroraPostgreSQL.Optimize.Parameters.max_plans"></a>

Sets the maximum number of plans that might be captured in the `apg_plan_mgmt.dba_plans` view\. 

**Important**  
Set `apg_plan_mgmt.max_plans` at the cluster or DB instance level\. It requires a DB instance restart for a new value to take effect\. We recommend setting this parameter to `10000` or higher for all Aurora PostgreSQL versions\.

```
SET apg_plan_mgmt.max_plans = integer-value;
```


****  

| Value | Default | Description | 
| --- | --- | --- | 
| integer |  10000 for Aurora PostgreSQL version 11 and higher 1000 for Aurora PostgreSQL version 10 and lower  | A positive integer value greater or equal to 10\.  | 

 

## apg\_plan\_mgmt\.plan\_retention\_period<a name="AuroraPostgreSQL.Optimize.Parameters.plan_retention_period"></a>

The number of days plans are kept in the `apg_plan_mgmt.dba_plans` view before being automatically deleted\. A plan is deleted when the current date is this many days since the plan's `last_used` date\. The `last_used` date is the most recent date that either the optimizer chose a plan as the minimum cost plan or that the plan was executed\.

```
SET apg_plan_mgmt.plan_retention_period = integer-value;
```


****  

| Value | Default | Description | 
| --- | --- | --- | 
| Positive integer | 32 | A positive integer value greater or equal to 32, representing days\. | 

## apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold<a name="AuroraPostgreSQL.Optimize.Parameters.unapproved_plan_execution_threshold"></a>

An estimated total plan cost threshold, below which the optimizer runs an unapproved plan\. By default, the optimizer does not run unapproved plans\. However, you can set an execution threshold for your fastest unapproved plans\. With this setting, the optimizer bypasses the overhead of enforcing only approved plans\.

```
SET apg_plan_mgmt.unapproved_plan_execution_threshold = integer-value;
```


****  

| Value | Default | Description | 
| --- | --- | --- | 
| Positive integer | 0 | A positive integer value greater or equal to 0\. A value of 0 means no unapproved plans run when use\_plan\_baselines is true\. | 



With the following example, the optimizer runs an unapproved plan if the estimated cost is less than 550, even if `use_plan_baselines` is `true`\.

```
SET apg_plan_mgmt.unapproved_plan_execution_threshold = 550;
```

## apg\_plan\_mgmt\.use\_plan\_baselines<a name="AuroraPostgreSQL.Optimize.Parameters.use_plan_baselines"></a>

Enforce the optimizer to use managed plans for managed statements\.

```
SET apg_plan_mgmt.use_plan_baselines = [true | false];
```


****  

| Value | Description | 
| --- | --- | 
| true |  Enforce the use of managed plans\. When a SQL statement runs and it is a managed statement in the `apg_plan_mgmt.dba_plans` view, the optimizer chooses a managed plan in the following order\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Optimize.Parameters.html)  | 
| false | \(Default\) Do not use managed plans\. The optimizer uses its generated minimum\-cost plan\. | 

** Usage notes**

When `use_plan_baselines` is `true`, then the optimizer makes the following execution decisions:

1. If the estimated cost of the optimizer's plan is below the `unapproved_plan_execution_threshold`, then execute it, else

1. If the plan is `approved` or `preferred`, then execute it, else

1. Execute a minimum\-cost `preferred` plan, if possible, else

1. Execute a minimum\-cost `approved` plan, if possible, else 

1. Execute the optimizer's minimum\-cost plan\.