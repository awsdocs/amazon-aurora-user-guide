# aurora\_stat\_wait\_event<a name="aurora_stat_wait_event"></a>

Lists all supported wait events for Aurora PostgreSQL\. For information about Aurora PostgreSQL wait events, see [Amazon Aurora PostgreSQL wait events](AuroraPostgreSQL.Reference.Waitevents.md)\.

## Syntax<a name="aurora_stat_wait_event-syntax"></a>

 

```
aurora_stat_wait_event()
```

## Arguments<a name="aurora_stat_wait_event-arguments"></a>

None

## Return type<a name="aurora_stat_wait_event-return-type"></a>

SETOF record with following columns:
+ type\_name – Wait type name
+ event\_name – Wait event name
+ wait – Number of waits
+ wait\_time – Wait time in milliseconds 
+ ms\_per\_wait – Average milliseconds by the number of an wait
+ waits\_per\_xact – Average waits by the number of one transaction
+ ms\_per\_xact – Average milliseconds by the number of transactions

## Usage notes<a name="aurora_stat_wait_event-usage-notes"></a>

To see event names with event types instead of IDs, use this function together with other functions such as `aurora_stat_wait_type` and `aurora_stat_system_waits`\. Wait event names returned by this function are the same as those returned by the `aurora_wait_report` function\.

## Examples<a name="aurora_stat_wait_event-examples"></a>

The following example shows results from calling the `aurora_stat_wait_event` function\.

```
=>  SELECT * 
    FROM aurora_stat_wait_event();
    
 type_id | event_id  |                event_name
---------+-----------+-------------------------------------------
       1 |  16777216 | <unassigned:0>
       1 |  16777217 | ShmemIndexLock
       1 |  16777218 | OidGenLock
       1 |  16777219 | XidGenLock
.
.
.
       9 | 150994945 | PgSleep
       9 | 150994946 | RecoveryApplyDelay
      10 | 167772160 | BufFileRead
      10 | 167772161 | BufFileWrite
      10 | 167772162 | ControlFileRead
.
.
.
      10 | 167772226 | WALInitWrite
      10 | 167772227 | WALRead
      10 | 167772228 | WALSync
      10 | 167772229 | WALSyncMethodAssign
      10 | 167772230 | WALWrite
      10 | 167772231 | XactSync
.
.
.
      11 | 184549377 | LsnAllocate
```

The following example joins `aurora_stat_wait_type` and `aurora_stat_wait_event` to return type names and event names for improved readability\.

```
=> SELECT *
    FROM aurora_stat_wait_type() t 
    JOIN aurora_stat_wait_event() e 
      ON t.type_id = e.type_id; 

 type_id | type_name | type_id | event_id  |                event_name
---------+-----------+---------+-----------+-------------------------------------------
       1 | LWLock    |       1 |  16777216 | <unassigned:0>
       1 | LWLock    |       1 |  16777217 | ShmemIndexLock
       1 | LWLock    |       1 |  16777218 | OidGenLock
       1 | LWLock    |       1 |  16777219 | XidGenLock
       1 | LWLock    |       1 |  16777220 | ProcArrayLock
.
.
.
       3 | Lock      |       3 |  50331648 | relation
       3 | Lock      |       3 |  50331649 | extend
       3 | Lock      |       3 |  50331650 | page
       3 | Lock      |       3 |  50331651 | tuple
.
.
.
      10 | IO        |      10 | 167772214 | TimelineHistorySync
      10 | IO        |      10 | 167772215 | TimelineHistoryWrite
      10 | IO        |      10 | 167772216 | TwophaseFileRead
      10 | IO        |      10 | 167772217 | TwophaseFileSync
.
.
.
      11 | LSN       |      11 | 184549376 | LsnDurable
```