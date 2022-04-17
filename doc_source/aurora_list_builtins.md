# aurora\_list\_builtins<a name="aurora_list_builtins"></a>

Lists all available Aurora PostgreSQL built\-in functions, along with brief descriptions and function details\.

## Syntax<a name="aurora_list_builtins-syntax"></a>

 

```
aurora_list_builtins()
```

## Arguments<a name="aurora_list_builtins-arguments"></a>

None

## Return type<a name="aurora_list_builtins-return-type"></a>

SETOF record

## Examples<a name="aurora_list_builtins-examples"></a>

The following example shows results from calling the `aurora_list_builtins` function\.

```
=> SELECT * 
FROM aurora_list_builtins();
     
               Name                | Result data type |                   Argument data types                    | Type | Volatility |  Parallel  | Security |                             Description
-----------------------------------+------------------+----------------------------------------------------------+------+------------+------------+----------+---------------------------------------------------------------------
 aurora_version                    | text             |                                                          | func | stable     | safe       | invoker  | Amazon Aurora PostgreSQL-Compatible Edition version string
 aurora_stat_wait_type             | SETOF record     | OUT type_id smallint, OUT type_name text                 | func | volatile   | restricted | invoker  | Lists all supported wait types
 aurora_stat_wait_event            | SETOF record     | OUT type_id smallint, OUT event_id integer, OUT event_na.| func | volatile   | restricted | invoker  | Lists all supported wait events
                                   |                  |.me text                                                  |      |            |            |          |
 aurora_list_builtins              | SETOF record     | OUT "Name" text, OUT "Result data type" text, OUT "Argum.| func | stable     | safe       | invoker  | Lists all Aurora built-in functions
                                   |                  |.ent data types" text, OUT "Type" text, OUT "Volatility" .|      |            |            |          |
                                   |                  |.text, OUT "Parallel" text, OUT "Security" text, OUT "Des.|      |            |            |          |
                                   |                  |.cription" text                                           |      |            |            |          |
 .
 .
 .
 aurora_stat_file                  | SETOF record     | OUT filename text, OUT allocated_bytes bigint, OUT used_.| func | stable     | safe       | invoker  | Lists all files present in Aurora storage
                                   |                  |.bytes bigint                                             |      |            |            |          |
 aurora_stat_get_db_commit_latency | bigint           | oid                                                      | func | stable     | restricted | invoker  | Per DB commit latency in microsecs
```