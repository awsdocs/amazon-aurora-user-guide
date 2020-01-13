# Function Reference for Query Plan Management<a name="AuroraPostgreSQL.Optimize.Functions"></a>

The `apg_plan_mgmt` extension provides the following functions\.

**Topics**
+ [apg\_plan\_mgmt\.delete\_plan](#AuroraPostgreSQL.Optimize.Functions.delete_plan)
+ [apg\_plan\_mgmt\.evolve\_plan\_baselines](#AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines)
+ [apg\_plan\_mgmt\.plan\_last\_used](#AuroraPostgreSQL.Optimize.Functions.plan_last_used)
+ [apg\_plan\_mgmt\.reload](#AuroraPostgreSQL.Optimize.Functions.reload)
+ [apg\_plan\_mgmt\.set\_plan\_enabled](#AuroraPostgreSQL.Optimize.Functions.set_plan_enabled)
+ [apg\_plan\_mgmt\.set\_plan\_status](#AuroraPostgreSQL.Optimize.Functions.set_plan_status)
+ [apg\_plan\_mgmt\.validate\_plans](#AuroraPostgreSQL.Optimize.Functions.validate_plans)

## apg\_plan\_mgmt\.delete\_plan<a name="AuroraPostgreSQL.Optimize.Functions.delete_plan"></a>

Delete a managed plan\. 

**Syntax**

```
apg_plan_mgmt.delete_plan(
    sql_hash,
    plan_hash
)
```

**Return Value**  
Returns 0 if the delete was successful or \-1 if the delete failed\.

**Parameters**


****  

| Parameter | Description | 
| --- | --- | 
| sql\_hash  | The sql\_hash ID of the plan's managed SQL statement\. | 
| plan\_hash | The managed plan's plan\_hash ID\. | 

## apg\_plan\_mgmt\.evolve\_plan\_baselines<a name="AuroraPostgreSQL.Optimize.Functions.evolve_plan_baselines"></a>

Verifies whether an already approved plan is faster or whether a plan identified by the query optimizer as a minimum cost plan is faster\.

**Syntax**

```
apg_plan_mgmt.evolve_plan_baselines(
    sql_hash, 
    plan_hash,
    min_speedup_factor,
    action
)
```

**Return Value**

The number of plans that were not faster than the best approved plan\. 

**Parameters**


****  

| Parameter | Description | 
| --- | --- | 
| sql\_hash | The sql\_hash ID of the plan's managed SQL statement\. | 
| plan\_hash | The managed plan's plan\_hash ID\. Use NULL to mean all plans that have the same sql\_hash ID value\. | 
| min\_speedup\_factor |  The *minimum speedup factor* can be the number of times faster that a plan must be than the best of the already approved plans to approve it\. Alternatively, this factor can be the number of times slower that a plan must be to reject or disable it\. This is a positive float value\.  | 
| action |  The action the function is to perform\. Valid values include the following\. Case does not matter\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Optimize.Functions.html)  | 

**Usage Notes**

Set specified plans to approved, rejected, or disabled based on whether the planning plus execution time is faster than the best approved plan by a factor that you can set\. The action parameter might be set to `'approve'` or `'reject'` to automatically approve or reject a plan that meets the performance criteria\. Alternatively, it might be set to '' \(empty string\) to do the performance experiment and produce a report, but take no action\.

You can avoid pointlessly rerunning of the `apg_plan_mgmt.evolve_plan_baselines` function for a plan on which it was recently run\. To do so, restrict the plans to just the recently created unapproved plans\. Alternatively, you can avoid running the `apg_plan_mgmt.evolve_plan_baselines` function on any approved plan that has a recent `last_verified` timestamp\.

Conduct a performance experiment to compare the planning plus execution time of each plan relative to the other plans in the baseline\. In some cases, there is only one plan for a statement and the plan is approved\. In such a case, compare the planning plus execution time of the plan to the planning plus execution time of using no plan\.

The incremental benefit \(or disadvantage\) of each plan is recorded in the `apg_plan_mgmt.dba_plans` view in the `total_time_benefit_ms` column\. When this value is positive, there is a measurable performance advantage to including this plan in the baseline\.

In addition to collecting the planning and execution time of each candidate plan, the `last_verified` column of the `apg_plan_mgmt.dba_plans` view is updated with the `current_timestamp`\. The `last_verified` timestamp might be used to avoid running this function again on a plan that recently had its performance verified\.

## apg\_plan\_mgmt\.plan\_last\_used<a name="AuroraPostgreSQL.Optimize.Functions.plan_last_used"></a>

Returns the `last_used` date of the specified plan from shared memory\. 

**Syntax**

```
apg_plan_mgmt.plan_last_used(
    sql_hash,
    plan_hash
)
```

**Return Value**  
Returns the `last_used` date\.

**Parameters**


****  

| Parameter | Description | 
| --- | --- | 
| sql\_hash  | The sql\_hash ID of the plan's managed SQL statement\. | 
| plan\_hash | The managed plan's plan\_hash ID\. | 

## apg\_plan\_mgmt\.reload<a name="AuroraPostgreSQL.Optimize.Functions.reload"></a>

Reload plans into shared memory from the `apg_plan_mgmt.dba_plans` view\. 

**Syntax**

```
apg_plan_mgmt.reload()
```

**Return Value**

None\.

**Parameters**

None\.

**Usage Notes**

Call `reload` for the following situations:
+ Use it to refresh the shared memory of a read\-only replica immediately, rather than wait for new plans to propagate to the replica\.
+ Use it after importing managed plans\.

## apg\_plan\_mgmt\.set\_plan\_enabled<a name="AuroraPostgreSQL.Optimize.Functions.set_plan_enabled"></a>

Enable or disable a managed plan\.

**Syntax**

```
apg_plan_mgmt.set_plan_enabled(
    sql_hash, 
    plan_hash, 
    [true | false]
)
```

**Return Value**

Returns 0 if the setting was successful or \-1 if the setting failed\.

**Parameters**


****  

| Parameter | Description | 
| --- | --- | 
| sql\_hash | The sql\_hash ID of the plan's managed SQL statement\. | 
| plan\_hash | The managed plan's plan\_hash ID\. | 
| enabled |  Boolean value of true or false: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Optimize.Functions.html)  | 

## apg\_plan\_mgmt\.set\_plan\_status<a name="AuroraPostgreSQL.Optimize.Functions.set_plan_status"></a>

Set a managed plan's status to `Approved`, `Unapproved`, `Rejected`, or `Preferred`\.

**Syntax**

```
apg_plan_mgmt.set_plan_status(
    sql_hash, 
    plan_hash, 
    status
)
```

**Return Value**

Returns 0 if the setting was successful or \-1 if the setting failed\.

**Parameters**


****  

| Parameter | Description | 
| --- | --- | 
| sql\_hash | The sql\_hash ID of the plan's managed SQL statement\. | 
| plan\_hash | The managed plan's plan\_hash ID\. | 
| status |  A string with one of the following values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Optimize.Functions.html) The case you use does not matter, however the status value is set to initial uppercase in the `apg_plan_mgmt.dba_plans` view\. For more information about these values, see `status` in [Reference for the apg\_plan\_mgmt\.dba\_plans View](AuroraPostgreSQL.Optimize.ViewPlans.md#AuroraPostgreSQL.Optimize.ViewPlans.dba_plans)\.   | 

## apg\_plan\_mgmt\.validate\_plans<a name="AuroraPostgreSQL.Optimize.Functions.validate_plans"></a>

Validate that the optimizer can still recreate plans\. The optimizer validates `Approved`, `Unapproved`, and `Preferred` plans, whether the plan is enabled or disabled\. `Rejected` plans are not validated\. Optionally, you can use the `apg_plan_mgmt.validate_plans` function to delete or disable invalid plans\.

**Syntax**

```
apg_plan_mgmt.validate_plans(
    sql_hash, 
    plan_hash, 
    action)
            
apg_plan_mgmt.validate_plans(
    action)
```

**Return Value**

The number of invalid plans\.

**Parameters**


****  

| Parameter | Description | 
| --- | --- | 
| sql\_hash | The sql\_hash ID of the plan's managed SQL statement\. | 
| plan\_hash | The managed plan's plan\_hash ID\. Use NULL to mean all plans for the same sql\_hash ID value\. | 
| action |  The action the function is to perform for invalid plans\. Valid string values include the following\. Case does not matter\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Optimize.Functions.html) Any other value is treated like the empty string\.  | 

**Usage Notes**

Use the form `validate_plans(action)` to validate all the managed plans for all the managed statements in the entire `apg_plan_mgmt.dba_plans` view\.

Use the form `validate_plans(sql_hash, plan_hash, action)` to validate a managed plan specified with `plan_hash`, for a managed statement specified with `sql_hash`\. 

Use the form `validate_plans(sql_hash, NULL, action)` to validate all the managed plans for the managed statement specified with `sql_hash`\.