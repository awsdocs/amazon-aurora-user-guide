# cpu<a name="ams-waits.cpu"></a>

The `cpu` wait event occurs when a thread is active in CPU or is waiting for CPU\.

**Topics**
+ [Supported engine versions](#ams-waits.cpu.context.supported)
+ [Context](#ams-waits.cpu.context)
+ [Likely causes of increased waits](#ams-waits.cpu.causes)
+ [Actions](#ams-waits.cpu.actions)

## Supported engine versions<a name="ams-waits.cpu.context.supported"></a>

This wait event information is supported for the following engine versions:
+ Aurora MySQL version 2, up to 2\.09\.2

## Context<a name="ams-waits.cpu.context"></a>

For every vCPU, a connection can run work on this CPU\. In some situations, the number of active connections that are ready to run is higher than the number of vCPUs\. This imbalance results in connections waiting for CPU resources\. If the number of active connections stays consistently higher than the number of vCPUs, then your instance experiences CPU contention\. The contention causes the `cpu` wait event to occur\.

**Note**  
The Performance Insights metric for CPU is `DBLoadCPU`\. The value for `DBLoadCPU` can differ from the value for the CloudWatch metric `CPUUtilization`\. The latter metric is collected from the HyperVisor for a database instance\.

Performance Insights OS metrics provide detailed information about CPU utilization\. For example, you can display the following metrics:
+ `os.cpuUtilization.nice.avg`
+ `os.cpuUtilization.total.avg`
+ `os.cpuUtilization.wait.avg`
+ `os.cpuUtilization.idle.avg`

Performance Insights reports the CPU usage by the database engine as `os.cpuUtilization.nice.avg`\.

## Likely causes of increased waits<a name="ams-waits.cpu.causes"></a>

When this event occurs more than normal, possibly indicating a performance problem, typical causes include the following:
+ Analytic queries
+ Highly concurrent transactions
+ Long\-running transactions
+ A sudden increase in the number of connections, known as a *login storm*
+ An increase in context switching

## Actions<a name="ams-waits.cpu.actions"></a>

If the `cpu` wait event dominates database activity, it doesn't necessarily indicate a performance problem\. Respond to this event only when performance degrades\. 

Depending on the cause of the increase in CPU utilization, consider the following strategies:
+ Increase the CPU capacity of the host\. This approach typically gives only temporary relief\.
+ Identify top queries for potential optimization\.
+ Redirect some read\-only workload to reader nodes, if applicable\.

**Topics**
+ [Identify the sessions or queries that are causing the problem](#ams-waits.cpu.actions.az-vpc-subnet)
+ [Analyze and optimize the high CPU workload](#ams-waits.cpu.actions.db-instance-class)

### Identify the sessions or queries that are causing the problem<a name="ams-waits.cpu.actions.az-vpc-subnet"></a>

To find the sessions and queries, look at the **Top SQL** table in Performance Insights for the SQL statements that have the highest CPU load\. For more information, see [Analyzing metrics with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)\.

Typically, one or two SQL statements consume the majority of CPU cycles\. Concentrate your efforts on these statements\. Suppose that your DB instance has 2 vCPUs with a DB load of 3\.1 average active sessions \(AAS\), all in the CPU state\. In this case, your instance is CPU bound\. Consider the following strategies:
+ Upgrade to a larger instance class with more vCPUs\.
+ Tune your queries to have lower CPU load\.

In this example, the top SQL queries have a DB load of 1\.5 AAS, all in the CPU state\. Another SQL statement has a load of 0\.1 in the CPU state\. In this example, if you stopped the lowest\-load SQL statement, you don't significantly reduce database load\. However, if you optimize the two high\-load queries to be twice as efficient, you eliminate the CPU bottleneck\. If you reduce the CPU load of 1\.5 AAS by 50 percent, the AAS for each statement decreases to 0\.75\. The total DB load spent on CPU is now 1\.6 AAS\. This value is below the maximum vCPU line of 2\.0\.

For a useful overview of troubleshooting using Performance Insights, see the blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](http://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\. Also see the AWS Support article [How can I troubleshoot and resolve high CPU utilization on my Amazon RDS for MySQL instances?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-instance-high-cpu/)\.

### Analyze and optimize the high CPU workload<a name="ams-waits.cpu.actions.db-instance-class"></a>

After you identify the query or queries increasing CPU usage, you can either optimize them or end the connection\. The following example shows how to end a connection\.

```
CALL mysql.rds_kill(processID);
```

For more information, see [mysql\.rds\_kill](mysql-stored-proc-ending.md#mysql_rds_kill)\.

If you end a session, the action might trigger a long rollback\.

#### Follow the guidelines for optimizing queries<a name="ams-waits.cpu.actions.db-instance-class.optimizing"></a>

To optimize queries, consider the following guidelines:
+ Run the `EXPLAIN` statement\. 

  This command shows the individual steps involved in running a query\. For more information, see [Optimizing Queries with EXPLAIN](https://dev.mysql.com/doc/refman/5.7/en/using-explain.html) in the MySQL documentation\.
+ Run the `SHOW PROFILE` statement\.

  Use this statement to review profile details that can indicate resource usage for statements that are run during the current session\. For more information, see [SHOW PROFILE Statement](https://dev.mysql.com/doc/refman/5.7/en/show-profile.html) in the MySQL documentation\.
+ Run the `ANALYZE TABLE` statement\.

  Use this statement to refresh the index statistics for the tables accessed by the high\-CPU consuming query\. By analyzing the statement, you can help the optimizer choose an appropriate execution plan\. For more information, see [ANALYZE TABLE Statement](https://dev.mysql.com/doc/refman/5.7/en/analyze-table.html) in the MySQL documentation\.

#### Follow the guidelines for improving CPU usage<a name="ams-waits.cpu.actions.db-instance-class.considerations"></a>

To improve CPU usage in a database instance, follow these guidelines:
+ Ensure that all queries are using proper indexes\.
+ Find out whether you can use Aurora parallel queries\. You can use this technique to reduce CPU usage on the head node by pushing down function processing, row filtering, and column projection for the `WHERE` clause\.
+ Find out whether the number of SQL executions per second meets the expected thresholds\.
+ Find out whether index maintenance or new index creation takes up CPU cycles needed by your production workload\. Schedule maintenance activities outside of peak activity times\.
+ Find out whether you can use partitioning to help reduce the query data set\. For more information, see the blog post [How to plan and optimize Amazon Aurora with MySQL compatibility for consolidated workloads](https://aws.amazon.com/blogs/database/planning-and-optimizing-amazon-aurora-with-mysql-compatibility-for-consolidated-workloads/)\.

#### Check for connection storms<a name="ams-waits.cpu.actions.db-instance-class.cpu-util"></a>

 If the `DBLoadCPU` metric is not very high, but the `CPUUtilization` metric is high, the cause of the high CPU utilization lies outside of the database engine\. A classic example is a connection storm\.

Check whether the following conditions are true:
+ There is an increase in both the Performance Insights `CPUUtilization` metric and the Amazon CloudWatch `DatabaseConnections` metric\.
+ The number of threads in the CPU is greater than the number of vCPUs\.

If the preceding conditions are true, consider decreasing the number of database connections\. For example, you can use a connection pool such as RDS Proxy\. To learn the best practices for effective connection management and scaling, see the whitepaper [Amazon Aurora MySQL DBA Handbook for Connection Management](https://d1.awsstatic.com/whitepapers/RDS/amazon-aurora-mysql-database-administrator-handbook.pdf)\.