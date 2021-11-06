# CPU<a name="apg-waits.cpu"></a>

This event occurs when a thread is active in CPU or is waiting for CPU\.

**Topics**
+ [Supported engine versions](#apg-waits.cpu.context.supported)
+ [Context](#apg-waits.cpu.context)
+ [Likely causes of increased waits](#apg-waits.cpu.causes)
+ [Actions](#apg-waits.cpu.actions)

## Supported engine versions<a name="apg-waits.cpu.context.supported"></a>

This wait event information is relevant for Aurora PostgreSQL version 9\.6 and higher\. 

## Context<a name="apg-waits.cpu.context"></a>

The *central processing unit \(CPU\)* is the component of a computer that runs instructions\. For example, CPU instructions perform arithmetic operations and exchange data in memory\. If a query increases the number of instructions that it performs through the database engine, the time spent running the query increases\. *CPU scheduling* is giving CPU time to a process\. Scheduling is orchestrated by the kernel of the operating system\.

**Topics**
+ [How to tell when this wait occurs](#apg-waits.cpu.when-it-occurs)
+ [DBLoadCPU metric](#apg-waits.cpu.context.dbloadcpu)
+ [os\.cpuUtilization metrics](#apg-waits.cpu.context.osmetrics)
+ [Likely cause of CPU scheduling](#apg-waits.cpu.context.scheduling)

### How to tell when this wait occurs<a name="apg-waits.cpu.when-it-occurs"></a>

This `CPU` wait event indicates that a backend process is active in CPU or is waiting for CPU\. You know that it's occurring when a query shows the following information:
+ The `pg_stat_activity.state` column has the value `active`\.
+ The `wait_event_type` and `wait_event` columns in `pg_stat_activity` are both `null`\.

To see the backend processes that are using or waiting on CPU, run the following query\.

```
SELECT * 
FROM   pg_stat_activity
WHERE  state = 'active'
AND    wait_event_type IS NULL
AND    wait_event IS NULL;
```

### DBLoadCPU metric<a name="apg-waits.cpu.context.dbloadcpu"></a>

The Performance Insights metric for CPU is `DBLoadCPU`\. The value for `DBLoadCPU` can differ from the value for the Amazon CloudWatch metric `CPUUtilization`\. The latter metric is collected from the HyperVisor for a database instance\.

### os\.cpuUtilization metrics<a name="apg-waits.cpu.context.osmetrics"></a>

Performance Insights operating\-system metrics provide detailed information about CPU utilization\. For example, you can display the following metrics:
+ `os.cpuUtilization.nice.avg`
+ `os.cpuUtilization.total.avg`
+ `os.cpuUtilization.wait.avg`
+ `os.cpuUtilization.idle.avg`

Performance Insights reports the CPU usage by the database engine as `os.cpuUtilization.nice.avg`\.

### Likely cause of CPU scheduling<a name="apg-waits.cpu.context.scheduling"></a>

From an operating system perspective, the CPU is active when it isn't running the idle thread\. The CPU is active while it performs a computation, but it's also active when it waits on memory I/O\. This type of I/O dominates a typical database workload\.

Processes are likely to wait to get scheduled on a CPU when the following conditions are met:
+ The CloudWatch `CPUUtilization` metric is near 100 percent\.
+ The average load is greater than the number of vCPUs, indicating a heavy load\. You can find the `loadAverageMinute` metric in the OS metrics section in Performance Insights\.

## Likely causes of increased waits<a name="apg-waits.cpu.causes"></a>

When the CPU wait event occurs more than normal, possibly indicating a performance problem, typical causes include the following\.

**Topics**
+ [Likely causes of sudden spikes](#apg-waits.cpu.causes.spikes)
+ [Likely causes of long\-term high frequency](#apg-waits.cpu.causes.long-term)
+ [Corner cases](#apg-waits.cpu.causes.corner-cases)

### Likely causes of sudden spikes<a name="apg-waits.cpu.causes.spikes"></a>

The most likely causes of sudden spikes are as follows:
+ Your application has opened too many simultaneous connections to the database\. This scenario is known as a "connection storm\."
+ Your application workload changed in any of the following ways:
  + New queries
  + An increase in the size of your dataset
  + Index maintenance or creation
  + New functions
  + New operators
  + An increase in parallel query execution
+ Your query execution plans have changed\. In some cases, a change can cause an increase in buffers\. For example, the query is now using a sequential scan when it previously used an index\. In this case, the queries need more CPU to accomplish the same goal\.

### Likely causes of long\-term high frequency<a name="apg-waits.cpu.causes.long-term"></a>

The most likely causes of events that recur over a long period:
+ Too many backend processes are running concurrently on CPU\. These processes can be parallel workers\.
+ Queries are performing suboptimally because they need a large number of buffers\.

### Corner cases<a name="apg-waits.cpu.causes.corner-cases"></a>

If none of the likely causes turn out to be actual causes, the following situations might be occurring:
+ The CPU is swapping processes in and out\.
+ CPU context switching has increased\.
+ Aurora PostgreSQL code is missing wait events\.

## Actions<a name="apg-waits.cpu.actions"></a>

If the `CPU` wait event dominates database activity, it doesn't necessarily indicate a performance problem\. Respond to this event only when performance degrades\.

**Topics**
+ [Investigate whether the database is causing the CPU increase](#apg-waits.cpu.actions.db-CPU)
+ [Determine whether the number of connections increased](#apg-waits.cpu.actions.connections)
+ [Respond to workload changes](#apg-waits.cpu.actions.workload)

### Investigate whether the database is causing the CPU increase<a name="apg-waits.cpu.actions.db-CPU"></a>

Examine the `os.cpuUtilization.nice.avg` metric in Performance Insights\. If this value is far less than the CPU usage, nondatabase processes are the main contributor to CPU\.

### Determine whether the number of connections increased<a name="apg-waits.cpu.actions.connections"></a>

Examine the `DatabaseConnections` metric in Amazon CloudWatch\. Your action depends on whether the number increased or decreased during the period of increased CPU wait events\.

#### The connections increased<a name="apg-waits.cpu.actions.connections.increased"></a>

If the number of connections went up, compare the number of backend processes consuming CPU to the number of vCPUs\. The following scenarios are possible:
+ The number of backend processes consuming CPU is less than the number of vCPUs\.

  In this case, the number of connections isn't an issue\. However, you might still try to reduce CPU utilization\.
+ The number of backend processes consuming CPU is greater than the number of vCPUs\.

  In this case, consider the following options:
  + Decrease the number of backend processes connected to your database\. For example, implement a connection pooling solution such as RDS Proxy\. To learn more, see [Using Amazon RDS Proxy](rds-proxy.md)\.
  + Upgrade your instance size to get a higher number of vCPUs\.
  + Redirect some read\-only workloads to reader nodes, if applicable\.

#### The connections didn't increase<a name="apg-waits.cpu.actions.connections.decreased"></a>

Examine the `blks_hit` metrics in Performance Insights\. Look for a correlation between an increase in `blks_hit` and CPU usage\. The following scenarios are possible:
+ CPU usage and `blks_hit` are correlated\.

  In this case, find the top SQL statements that are linked to the CPU usage, and look for plan changes\. You can use either of the following techniques:
  + Explain the plans manually and compare them to the expected execution plan\.
  + Look for an increase in block hits per second and local block hits per second\. In the **Top SQL** section of Performance Insights dashboard, choose **Preferences**\.
+ CPU usage and `blks_hit` aren't correlated\.

  In this case, determine whether any of the following occurs:
  + The application is rapidly connecting to and disconnecting from the database\. 

    Diagnose this behavior by turning on `log_connections` and `log_disconnections`, then analyzing the PostgreSQL logs\. Consider using the `pgbadger` log analyzer\. For more information, see [https://github.com/darold/pgbadger](https://github.com/darold/pgbadger)\.
  + The OS is overloaded\.

    In this case, Performance Insights shows that backend processes are consuming CPU for a longer time than usual\. Look for evidence in the Performance Insights `os.cpuUtilization` metrics or the CloudWatch `CPUUtilization` metric\. If the operating system is overloaded, look at Enhanced Monitoring metrics to diagnose further\. Specifically, look at the process list and the percentage of CPU consumed by each process\.
  + Top SQL statements are consuming too much CPU\.

    Examine statements that are linked to the CPU usage to see whether they can use less CPU\. Run an `EXPLAIN` command, and focus on the plan nodes that have the most impact\. Consider using a PostgreSQL execution plan visualizer\. To try out this tool, see [http://explain.dalibo.com/](http://explain.dalibo.com/)\.

### Respond to workload changes<a name="apg-waits.cpu.actions.workload"></a>

If your workload has changed, look for the following types of changes:

New queries  
Check whether the new queries are expected\. If so, ensure that their execution plans and the number of executions per second are expected\.

An increase in the size of the data set  
Determine whether partitioning, if it's not already implemented, might help\. This strategy might reduce the number of pages that a query needs to retrieve\.

Index maintenance or creation  
Check whether the schedule for the maintenance is expected\. A best practice is to schedule maintenance activities outside of peak activities\.

New functions  
Check whether these functions perform as expected during testing\. Specifically, check whether the number of executions per second is expected\.

New operators  
Check whether they perform as expected during the testing\.

An increase in running parallel queries  
Determine whether any of the following situations has occurred:  
+ The relations or indexes involved have suddenly grown in size so that they differ significantly from `min_parallel_table_scan_size` or `min_parallel_index_scan_size`\.
+ Recent changes have been made to `parallel_setup_cost` or `parallel_tuple_cost`\.
+ Recent changes have been made to `max_parallel_workers` or `max_parallel_workers_per_gather`\.