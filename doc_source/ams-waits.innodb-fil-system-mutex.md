# synch/mutex/innodb/fil\_system\_mutex<a name="ams-waits.innodb-fil-system-mutex"></a>

The `synch/mutex/innodb/fil_system_mutex` event occurs when a session is waiting to access the tablespace memory cache\.

**Topics**
+ [Supported engine versions](#ams-waits.innodb-fil-system-mutex.context.supported)
+ [Context](#ams-waits.innodb-fil-system-mutex.context)
+ [Likely causes of increased waits](#ams-waits.innodb-fil-system-mutex.causes)
+ [Actions](#ams-waits.innodb-fil-system-mutex.actions)

## Supported engine versions<a name="ams-waits.innodb-fil-system-mutex.context.supported"></a>

This wait event information is supported for the following engine versions:
+ Aurora MySQL version 2, up to 2\.09\.2
+ Aurora MySQL version 1, up to 1\.23\.1

## Context<a name="ams-waits.innodb-fil-system-mutex.context"></a>

InnoDB uses tablespaces to manage the storage area for tables and log files\. The *tablespace memory cache* is a global memory structure that maintains information about tablespaces\. MySQL uses `synch/mutex/innodb/fil_system_mutex` waits to control concurrent access to the tablespace memory cache\. 

The event `synch/mutex/innodb/fil_system_mutex` indicates that there is currently more than one operation that needs to retrieve and manipulate information in the tablespace memory cache for the same tablespace\.

## Likely causes of increased waits<a name="ams-waits.innodb-fil-system-mutex.causes"></a>

When the `synch/mutex/innodb/fil_system_mutex` event appears more than normal, possibly indicating a performance problem, this typically occurs when all of the following conditions are present:
+ An increase in concurrent data manipulation language \(DML\) operations that update or delete data in the same table\.
+ The tablespace for this table is very large and has a lot of data pages\.
+ The fill factor for these data pages is low\.

## Actions<a name="ams-waits.innodb-fil-system-mutex.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Identify the sessions and queries causing the events](#ams-waits.innodb-fil-system-mutex.actions.identify)
+ [Reorganize large tables during off\-peak hours](#ams-waits.innodb-fil-system-mutex.actions.reorganize)

### Identify the sessions and queries causing the events<a name="ams-waits.innodb-fil-system-mutex.actions.identify"></a>

Typically, databases with moderate to significant load have wait events\. The wait events might be acceptable if performance is optimal\. If performance isn't optimal, examine where the database is spending the most time\. Look at the wait events that contribute to the highest load, and find out whether you can optimize the database and application to reduce those events\.

**To find SQL queries that are responsible for high load**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard appears for that DB instance\.

1. In the **Database load** chart, choose **Slice by wait**\.

1. At the bottom of the page, choose **Top SQL**\.

   The chart lists the SQL queries that are responsible for the load\. Those at the top of the list are most responsible\. To resolve a bottleneck, focus on these statements\.

For a useful overview of troubleshooting using Performance Insights, see the blog post [Analyze Amazon Aurora MySQL Workloads with Performance Insights](https://aws.amazon.com/blogs/database/analyze-amazon-aurora-mysql-workloads-with-performance-insights/)\.

Another way to find out which queries are causing high numbers of `synch/mutex/innodb/fil_system_mutex` waits is to check `performance_schema`, as in the following example\.

```
mysql> select * from performance_schema.events_waits_current where EVENT_NAME='wait/synch/mutex/innodb/fil_system_mutex'\G
*************************** 1. row ***************************
            THREAD_ID: 19
             EVENT_ID: 195057
         END_EVENT_ID: 195057
           EVENT_NAME: wait/synch/mutex/innodb/fil_system_mutex
               SOURCE: fil0fil.cc:6700
          TIMER_START: 1010146190118400
            TIMER_END: 1010146196524000
           TIMER_WAIT: 6405600
                SPINS: NULL
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
           INDEX_NAME: NULL
          OBJECT_TYPE: NULL
OBJECT_INSTANCE_BEGIN: 47285552262176
     NESTING_EVENT_ID: NULL
   NESTING_EVENT_TYPE: NULL
            OPERATION: lock
      NUMBER_OF_BYTES: NULL
                FLAGS: NULL
*************************** 2. row ***************************
            THREAD_ID: 23
             EVENT_ID: 5480
         END_EVENT_ID: 5480
           EVENT_NAME: wait/synch/mutex/innodb/fil_system_mutex
               SOURCE: fil0fil.cc:5906
          TIMER_START: 995269979908800
            TIMER_END: 995269980159200
           TIMER_WAIT: 250400
                SPINS: NULL
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
           INDEX_NAME: NULL
          OBJECT_TYPE: NULL
OBJECT_INSTANCE_BEGIN: 47285552262176
     NESTING_EVENT_ID: NULL
   NESTING_EVENT_TYPE: NULL
            OPERATION: lock
      NUMBER_OF_BYTES: NULL
                FLAGS: NULL
*************************** 3. row ***************************
            THREAD_ID: 55
             EVENT_ID: 23233794
         END_EVENT_ID: NULL
           EVENT_NAME: wait/synch/mutex/innodb/fil_system_mutex
               SOURCE: fil0fil.cc:449
          TIMER_START: 1010492125341600
            TIMER_END: 1010494304900000
           TIMER_WAIT: 2179558400
                SPINS: NULL
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
           INDEX_NAME: NULL
          OBJECT_TYPE: NULL
OBJECT_INSTANCE_BEGIN: 47285552262176
     NESTING_EVENT_ID: 23233786
   NESTING_EVENT_TYPE: WAIT
            OPERATION: lock
      NUMBER_OF_BYTES: NULL
                FLAGS: NULL
```

### Reorganize large tables during off\-peak hours<a name="ams-waits.innodb-fil-system-mutex.actions.reorganize"></a>

Reorganize large tables that you identify as the source of high numbers of `synch/mutex/innodb/fil_system_mutex` wait events during a maintenance window outside of production hours\. Doing so ensures that the internal tablespaces map cleanup doesn't occur when quick access to the table is critical\. For information about reorganizing tables, see [OPTIMIZE TABLE Statement](https://dev.mysql.com/doc/refman/5.7/en/optimize-table.html) in the *MySQL Reference*\.