# aurora\_db\_instance\_identifier<a name="aurora_db_instance_identifier"></a>

Reports the name of the DB instance name to which you're connected\.

## Syntax<a name="aurora_db_instance_identifier-syntax"></a>



```
aurora_db_instance_identifier()
```

## Arguments<a name="aurora_db_instance_identifier-arguments"></a>

None

## Return type<a name="aurora_db_instance_identifier-return-type"></a>

VARCHAR string

## Usage notes<a name="aurora_db_instance_identifier-usage-notes"></a>

This function displays the name of Aurora PostgreSQL\-Compatible Edition cluster's DB instance for your database client or application connection\. 

This function is available starting with the release of Aurora PostgreSQL versions 13\.7, 12\.11, 11\.16, 10\.21 and for all other later versions\. 

## Examples<a name="aurora_db_instance_identifier-examples"></a>

The following example shows results of calling the `aurora_db_instance_identifier` function\.

```
=> SELECT aurora_db_instance_identifier();aurora_db_instance_identifier 
-------------------------------
 test-my-instance-name
```

You can join the results of this function with the `aurora_replica_status` function to obtain details about the DB instance for your connection\. The [aurora\_replica\_status](aurora_replica_status.md) alone doesn't provide show you which DB instance you're using\. The following example shows you how\. 

```
=> SELECT *
    FROM aurora_replica_status() rt, 
         aurora_db_instance_identifier() di
    WHERE r.server_id = di;
-[ RECORD 1 ]----------------------+-----------------------
server_id                          | test-my-instance-name
session_id                         | MASTER_SESSION_ID
durable_lsn                        | 88492069
highest_lsn_rcvd                   | 
current_read_lsn                   | 
cur_replay_latency_in_usec         | 
active_txns                        | 
is_current                         | t
last_transport_error               | 0
last_error_timestamp               | 
last_update_timestamp              | 2022-06-03 11:18:25+00
feedback_xmin                      | 
feedback_epoch                     | 
replica_lag_in_msec                | 
log_stream_speed_in_kib_per_second | 0
log_buffer_sequence_number         | 0
oldest_read_view_trx_id            | 
oldest_read_view_lsn               | 
pending_read_ios                   | 819
```