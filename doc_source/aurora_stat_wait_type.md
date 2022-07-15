# aurora\_stat\_wait\_type<a name="aurora_stat_wait_type"></a>

Lists all supported wait types for Aurora PostgreSQL\.

## Syntax<a name="aurora_stat_wait_type-syntax"></a>

 

```
aurora_stat_wait_type()
```

## Arguments<a name="aurora_stat_wait_type-arguments"></a>

None

## Return type<a name="aurora_stat_wait_type-return-type"></a>

SETOF record with following columns:
+ type\_id – The ID of the type of wait event\.
+ type\_name – Wait type name\.

## Usage notes<a name="aurora_stat_wait_type-usage-notes"></a>

To see wait event names with wait event types instead of IDs, use this function together with other functions such as `aurora_stat_wait_event` and `aurora_stat_system_waits`\. Wait type names returned by this function are the same as those returned by the `aurora_wait_report` function\.

## Examples<a name="aurora_stat_wait_type-examples"></a>

The following example shows results from calling the `aurora_stat_wait_type` function\.

```
=> SELECT * 
     FROM aurora_stat_wait_type();     
 type_id | type_name
---------+-----------
       1 | LWLock
       3 | Lock
       4 | BufferPin
       5 | Activity
       6 | Client
       7 | Extension
       8 | IPC
       9 | Timeout
      10 | IO
      11 | LSN
```