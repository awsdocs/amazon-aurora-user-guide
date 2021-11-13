# lwlock:buffer\_content \(BufferContent\)<a name="apg-waits.lockbuffercontent"></a>

The `lwlock:buffer_content` event occurs when a session is waiting to read or write a data page in memory while another session has that page locked for writing\. In Aurora PostgreSQL 13 and higher, this wait event is called `BufferContent`\.

**Topics**
+ [Supported engine versions](#apg-waits.lockbuffercontent.context.supported)
+ [Context](#apg-waits.lockbuffercontent.context)
+ [Likely causes of increased waits](#apg-waits.lockbuffercontent.causes)
+ [Actions](#apg-waits.lockbuffercontent.actions)

## Supported engine versions<a name="apg-waits.lockbuffercontent.context.supported"></a>

This wait event information is supported for all versions of Aurora PostgreSQL\.

## Context<a name="apg-waits.lockbuffercontent.context"></a>

To read or manipulate data, PostgreSQL accesses it through shared memory buffers\. To read from the buffer, a process gets a lightweight lock \(LWLock\) on the buffer content in shared mode\. To write to the buffer, it gets that lock in exclusive mode\. Shared locks allow other processes to concurrently acquire shared locks on that content\. Exclusive locks prevent other processes from getting any type of lock on it\.

The `lwlock:buffer_content` \(`BufferContent`\) event indicates that multiple processes are attempting to get a lock on contents of a specific buffer\.

## Likely causes of increased waits<a name="apg-waits.lockbuffercontent.causes"></a>

When the `lwlock:buffer_content` \(`BufferContent`\) event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**Increased concurrent updates to the same data**  
There might be an increase in the number of concurrent sessions with queries that update the same buffer content\. This contention can be more pronounced on tables with a lot of indexes\.

**Workload data is not in memory**  
When data that the active workload is processing is not in memory, these wait events can increase\. This effect is because processes holding locks can keep them longer while they perform disk I/O operations\.

**Excessive use of foreign key constraints**  
Foreign key constraints can increase the amount of time a process holds onto a buffer content lock\. This effect is because read operations require a shared buffer content lock on the referenced key while that key is being updated\.

## Actions<a name="apg-waits.lockbuffercontent.actions"></a>

We recommend different actions depending on the causes of your wait event\. You might identify `lwlock:buffer_content` \(`BufferContent`\) events by using Amazon RDS Performance Insights or by querying the view `pg_stat_activity`\.

**Topics**
+ [Improve in\-memory efficiency](#apg-waits.lockbuffercontent.actions.in-memory)
+ [Reduce usage of foreign key constraints](#apg-waits.lockbuffercontent.actions.foreignkey)
+ [Remove unused indexes](#apg-waits.lockbuffercontent.actions.indexes)

### Improve in\-memory efficiency<a name="apg-waits.lockbuffercontent.actions.in-memory"></a>

To increase the chance that active workload data is in memory, partition tables or scale up your instance class\. For information about DB instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.

### Reduce usage of foreign key constraints<a name="apg-waits.lockbuffercontent.actions.foreignkey"></a>

Investigate workloads experiencing high numbers of `lwlock:buffer_content` \(`BufferContent`\) wait events for usage of foreign key constraints\. Remove unnecessary foreign key constraints\.

### Remove unused indexes<a name="apg-waits.lockbuffercontent.actions.indexes"></a>

For workloads experiencing high numbers of `lwlock:buffer_content` \(`BufferContent`\) wait events, identify unused indexes and remove them\.