# IO:XactSync<a name="apg-waits.xactsync"></a>

The `IO:XactSync` event occurs when the database is waiting for the Aurora storage subsystem to acknowledge the commit of a regular transaction, or the commit or rollback of a prepared transaction\. A prepared transaction is part of PostgreSQL's support for a two\-phase commit\.

**Topics**
+ [Supported engine versions](#apg-waits.xactsync.context.supported)
+ [Context](#apg-waits.xactsync.context)
+ [Likely causes of increased waits](#apg-waits.xactsync.causes)
+ [Actions](#apg-waits.xactsync.actions)

## Supported engine versions<a name="apg-waits.xactsync.context.supported"></a>

This wait event information is supported for all versions of Aurora PostgreSQL\.

## Context<a name="apg-waits.xactsync.context"></a>

The event `IO:XactSync` indicates that the instance is spending time waiting for the Aurora storage subsystem to confirm that transaction data was processed\.

## Likely causes of increased waits<a name="apg-waits.xactsync.causes"></a>

When the `IO:XactSync` event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**Network saturation**  
Traffic between clients and the DB instance or traffic to the storage subsystem might be too heavy for the network bandwidth\.

**CPU pressure**  
A heavy workload might be preventing the Aurora storage daemon from getting sufficient CPU time\.

## Actions<a name="apg-waits.xactsync.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Monitor your resources](#apg-waits.xactsync.actions.monitor)
+ [Scale up the CPU](#apg-waits.xactsync.actions.scalecpu)
+ [Increase network bandwidth](#apg-waits.xactsync.actions.scalenetwork)
+ [Reduce the number of commits](#apg-waits.xactsync.actions.commits)

### Monitor your resources<a name="apg-waits.xactsync.actions.monitor"></a>

To determine the cause of the increased `IO:XactSync` events, check the following metrics:
+ `WriteThroughput` and `CommitThroughput` – Changes in write throughput or commit throughput can show an increase in workload\.
+ `WriteLatency` and `CommitLatency` – Changes in write latency or commit latency can show that the storage subsystem is being asked to do more work\.
+ `CPUUtilization` – If the instance's CPU utilization is above 90 percent, the Aurora storage daemon might not be getting sufficient time on the CPU\. In this case, I/O performance degrades\.

For information about these metrics, see [Instance\-level metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md#Aurora.AuroraMySQL.Monitoring.Metrics.instances)\.

### Scale up the CPU<a name="apg-waits.xactsync.actions.scalecpu"></a>

To address CPU starvation issues, consider changing to an instance type with more CPU capacity\. For information about CPU capacity for a DB instance class, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.

### Increase network bandwidth<a name="apg-waits.xactsync.actions.scalenetwork"></a>

To determine whether the instance is reaching its network bandwidth limits, check for the following other wait events: 
+ `IO:DataFileRead`, `IO:BufferRead`, `IO:BufferWrite`, and `IO:XactWrite` – Queries using large amounts of I/O can generate more of these wait events\.
+ `Client:ClientRead` and `Client:ClientWrite` – Queries with large amounts of client communication can generate more of these wait events\.

If network bandwidth is an issue, consider changing to an instance type with more network bandwidth\. For information about network performance for a DB instance class, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.

### Reduce the number of commits<a name="apg-waits.xactsync.actions.commits"></a>

To reduce the number of commits, combine statements into transaction blocks\.