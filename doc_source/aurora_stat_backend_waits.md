# aurora\_stat\_backend\_waits<a name="aurora_stat_backend_waits"></a>

Displays statistics for wait activity for a specific backend process\. 

## Syntax<a name="aurora_stat_backend_waits-syntax"></a>

 

```
aurora_stat_backend_waits(pid)
```

## Arguments<a name="aurora_stat_backend_waits-arguments"></a>

`pid` – The ID for the backend process\. You can obtain process IDs by using the `pg_stat_activity` view\.

## Return type<a name="aurora_stat_backend_waits-return-type"></a>

SETOF record with following columns:
+ `type_id` – A number that denotes the type of wait event, such as `1` for a lightweight lock \(`LWLock`\), `3` for a lock, or `6` for a client session, to name some examples\. These values become meaningful when you join the results of this function with columns from `aurora_stat_wait_type` function, as shown in the [Examples](#aurora_stat_backend_waits-examples)\. 
+ `event_id` – An identifying number for the wait event\. Join this value with the columns from `aurora_stat_wait_event` to obtain meaningful event names\. 
+ `waits` – A count of the number of waits accumulated for the specified process ID\.
+ `wait_time` – Wait time in milliseconds\.

## Usage notes<a name="aurora_stat_backend_waits-usage-notes"></a>

You can use this function to analyze specific backend \(session\) wait events that occurred since a connection opened\. To get more meaningful information about wait event names and types, you can combine this function `aurora_stat_wait_type` and `aurora_stat_wait_event`, by using JOIN as shown in the examples\. 

## Examples<a name="aurora_stat_backend_waits-examples"></a>

This example shows all waits, types, and event names for a backend process ID 3027\. 

```
=> SELECT type_name, event_name, waits, wait_time
        FROM aurora_stat_backend_waits(3027)
NATURAL JOIN aurora_stat_wait_type()
NATURAL JOIN aurora_stat_wait_event();
type_name |       event_name       | waits | wait_time
-----------+------------------------+-------+------------
 LWLock    | ProcArrayLock          |     3 |         27
 LWLock    | wal_insert             |   423 |      16336
 LWLock    | buffer_content         | 11840 |    1033634
 LWLock    | lock_manager           | 23821 |    5664506
 Lock      | tuple                  | 10258 |  152280165
 Lock      | transactionid          | 78340 | 1239808783
 Client    | ClientRead             | 34072 |   17616684
 IO        | ControlFileSyncUpdate  |     2 |          0
 IO        | ControlFileWriteUpdate |     4 |         32
 IO        | RelationMapRead        |     2 |        795
 IO        | WALWrite               | 36666 |      98623
 IO        | XactSync               |  4867 |    7331963
```

This example shows current and cumulative wait types and wait events for all active sessions \(`pg_stat_activity state <> 'idle'`\) \(but without the current session that's invoking the function \(`pid <> pg_backend_pid()`\)\.

```
=> SELECT a.pid,
             a.usename,
             a.app_name,
             a.current_wait_type,
             a.current_wait_event,
             a.current_state,
             wt.type_name AS wait_type,
             we.event_name AS wait_event,
             a.waits,
             a.wait_time
        FROM (SELECT pid,
                     usename,
                     left(application_name,16) AS app_name,
                     coalesce(wait_event_type,'CPU') AS current_wait_type,
                     coalesce(wait_event,'CPU') AS current_wait_event,
                     state AS current_state,
                     (aurora_stat_backend_waits(pid)).*
                FROM pg_stat_activity
               WHERE pid <> pg_backend_pid()
                 AND state <> 'idle') a
NATURAL JOIN aurora_stat_wait_type() wt
NATURAL JOIN aurora_stat_wait_event() we;
  pid  | usename  | app_name | current_wait_type | current_wait_event | current_state | wait_type |       wait_event       | waits | wait_time
-------+----------+----------+-------------------+--------------------+---------------+-----------+------------------------+-------+-----------
 30099 | postgres | pgbench  | Lock              | transactionid      | active        | LWLock    | wal_insert             |  1937 |     29975
 30099 | postgres | pgbench  | Lock              | transactionid      | active        | LWLock    | buffer_content         | 22903 |    760498
 30099 | postgres | pgbench  | Lock              | transactionid      | active        | LWLock    | lock_manager           | 10012 |    223207
 30099 | postgres | pgbench  | Lock              | transactionid      | active        | Lock      | tuple                  | 20315 |  63081529
 .
 .
 .
 30099 | postgres | pgbench  | Lock              | transactionid      | active        | IO        | WALWrite               | 93293 |    237440
 30099 | postgres | pgbench  | Lock              | transactionid      | active        | IO        | XactSync               | 13010 |  19525143
 30100 | postgres | pgbench  | Lock              | transactionid      | active        | LWLock    | ProcArrayLock          |     6 |        53
 30100 | postgres | pgbench  | Lock              | transactionid      | active        | LWLock    | wal_insert             |  1913 |     25450
 30100 | postgres | pgbench  | Lock              | transactionid      | active        | LWLock    | buffer_content         | 22874 |    778005
 .
 .
 .
 30109 | postgres | pgbench  | IO                | XactSync           | active        | LWLock    | ProcArrayLock          |     3 |        71
 30109 | postgres | pgbench  | IO                | XactSync           | active        | LWLock    | wal_insert             |  1940 |     27741
 30109 | postgres | pgbench  | IO                | XactSync           | active        | LWLock    | buffer_content         | 22962 |    776352
 30109 | postgres | pgbench  | IO                | XactSync           | active        | LWLock    | lock_manager           |  9879 |    218826
 30109 | postgres | pgbench  | IO                | XactSync           | active        | Lock      | tuple                  | 20401 |  63581306
 30109 | postgres | pgbench  | IO                | XactSync           | active        | Lock      | transactionid          | 50769 | 211645008
 30109 | postgres | pgbench  | IO                | XactSync           | active        | Client    | ClientRead             | 89901 |  44192439
```

This example shows current and the top three \(3\) cumulative wait type and wait events for all active sessions \(`pg_stat_activity state <> 'idle'`\) excluding current session \(`pid <>pg_backend_pid()`\)\.

```
=> SELECT top3.*
       FROM (SELECT a.pid,
                    a.usename,
                    a.app_name,
                    a.current_wait_type,
                    a.current_wait_event,
                    a.current_state,
                    wt.type_name AS wait_type,
                    we.event_name AS wait_event,
                    a.waits,
                    a.wait_time,
                    RANK() OVER (PARTITION BY pid ORDER BY a.wait_time DESC)
               FROM (SELECT pid,
                            usename,
                            left(application_name,16) AS app_name,
                            coalesce(wait_event_type,'CPU') AS current_wait_type,
                            coalesce(wait_event,'CPU') AS current_wait_event,
                            state AS current_state,
                            (aurora_stat_backend_waits(pid)).*
                       FROM pg_stat_activity
                      WHERE pid <> pg_backend_pid()
                        AND state <> 'idle') a
       NATURAL JOIN aurora_stat_wait_type() wt
       NATURAL JOIN aurora_stat_wait_event() we) top3
 WHERE RANK <=3;
  pid  | usename  | app_name | current_wait_type | current_wait_event | current_state | wait_type |   wait_event    |  waits  | wait_time  | rank
-------+----------+----------+-------------------+--------------------+---------------+-----------+-----------------+---------+------------+------
 20567 | postgres | psql     | CPU               | CPU                | active        | LWLock    | wal_insert      |   25000 |   67512003 |    1
 20567 | postgres | psql     | CPU               | CPU                | active        | IO        | WALWrite        | 3071758 |    1016961 |    2
 20567 | postgres | psql     | CPU               | CPU                | active        | IO        | BufFileWrite    |   20750 |     184559 |    3
 27743 | postgres | pgbench  | Lock              | transactionid      | active        | Lock      | transactionid   |  237350 | 1265580011 |    1
 27743 | postgres | pgbench  | Lock              | transactionid      | active        | Lock      | tuple           |   93641 |  341472318 |    2
 27743 | postgres | pgbench  | Lock              | transactionid      | active        | Client    | ClientRead      |  417556 |  204796837 |    3
 .
 .
 .
 27745 | postgres | pgbench  | IO                | XactSync           | active        | Lock      | transactionid   |  238068 | 1265816822 |    1
 27745 | postgres | pgbench  | IO                | XactSync           | active        | Lock      | tuple           |   93210 |  338312247 |    2
 27745 | postgres | pgbench  | IO                | XactSync           | active        | Client    | ClientRead      |  419157 |  207836533 |    3
 27746 | postgres | pgbench  | Lock              | transactionid      | active        | Lock      | transactionid   |  237621 | 1264528811 |    1
 27746 | postgres | pgbench  | Lock              | transactionid      | active        | Lock      | tuple           |   93563 |  339799310 |    2
 27746 | postgres | pgbench  | Lock              | transactionid      | active        | Client    | ClientRead      |  417304 |  208372727 |    3
```