# Amazon Aurora MySQL reference<a name="AuroraMySQL.Reference"></a><a name="mysqlref"></a>

 This reference includes information about Aurora MySQL parameters, status variables, and general SQL extensions or differences from the community MySQL database engine\. 

**Topics**
+ [Aurora MySQL configuration parameters](#AuroraMySQL.Reference.ParameterGroups)
+ [MySQL parameters that don't apply to Aurora MySQL](#AuroraMySQL.Reference.Parameters.Inapplicable)
+ [MySQL status variables that don't apply to Aurora MySQL](#AuroraMySQL.Reference.StatusVars.Inapplicable)
+ [Aurora MySQL wait events](#AuroraMySQL.Reference.Waitevents)
+ [Aurora MySQL thread states](#AuroraMySQL.Reference.thread-states)
+ [Aurora MySQL isolation levels](#AuroraMySQL.Reference.IsolationLevels)
+ [Aurora MySQL hints](#AuroraMySQL.Reference.Hints)
+ [Aurora MySQL stored procedures](#AuroraMySQL.Reference.StoredProcs)
+ [Aurora MySQL–specific information\_schema tables](#AuroraMySQL.Reference.ISTables)

## Aurora MySQL configuration parameters<a name="AuroraMySQL.Reference.ParameterGroups"></a><a name="param_groups"></a>

 You manage your Amazon Aurora MySQL DB cluster in the same way that you manage other Amazon RDS DB instances, by using parameters in a DB parameter group\. Amazon Aurora differs from other DB engines in that you have a DB cluster that contains multiple DB instances\. As a result, some of the parameters that you use to manage your Aurora MySQL DB cluster apply to the entire cluster\. Other parameters apply only to a particular DB instance in the DB cluster\. 

 To manage cluster\-level parameters, you use DB cluster parameter groups\. To manage instance\-level parameters, you use DB parameter groups\. Each DB instance in an Aurora MySQL DB cluster is compatible with the MySQL database engine\. However, you apply some of the MySQL database engine parameters at the cluster level, and you manage these parameters using DB cluster parameter groups\. You can't find cluster\-level parameters in the DB parameter group for an instance in an Aurora DB cluster\. A list of cluster\-level parameters appears later in this topic\. 

 You can manage both cluster\-level and instance\-level parameters using the AWS Management Console, the AWS CLI, or the Amazon RDS API\. You use separate commands for managing cluster\-level parameters and instance\-level parameters\. For example, you can use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) CLI command to manage cluster\-level parameters in a DB cluster parameter group\. You can use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) CLI command to manage instance\-level parameters in a DB parameter group for a DB instance in a DB cluster\. 

 You can view both cluster\-level and instance\-level parameters in the console, or by using the CLI or RDS API\. For example, you can use the [describe\-db\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) AWS CLI command to view cluster\-level parameters in a DB cluster parameter group\. You can use the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) CLI command to view instance\-level parameters in a DB parameter group for a DB instance in a DB cluster\. 

**Note**  
 Each [default parameter group](USER_WorkingWithParamGroups.md) contains the default values for all parameters in the parameter group\. If the parameter has "engine default" for this value, see the version\-specific MySQL or PostgreSQL documentation for the actual default value\. 

 For more information on DB parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. For rules and restrictions for Aurora Serverless clusters, see [Parameter groups for Aurora Serverless v1](aurora-serverless-v1.how-it-works.md#aurora-serverless.parameter-groups)\. 

**Topics**
+ [Cluster\-level parameters](#AuroraMySQL.Reference.Parameters.Cluster)
+ [Instance\-level parameters](#AuroraMySQL.Reference.Parameters.Instance)

### Cluster\-level parameters<a name="AuroraMySQL.Reference.Parameters.Cluster"></a><a name="cluster_params"></a><a name="params"></a>

The following table shows all of the parameters that apply to the entire Aurora MySQL DB cluster\.


|  Parameter name  |  Modifiable  |  Notes  | 
| --- | --- | --- | 
|   `aurora_binlog_read_buffer_size`   |   Yes   |   Only affects clusters that use binary log \(binlog\) replication\. For information about binlog replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\. Removed from Aurora MySQL version 3\.   | 
|   `aurora_binlog_replication_max_yield_seconds`   |   Yes   |   Only affects clusters that use binary log \(binlog\) replication\. For information about binlog replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\.   | 
|   `aurora_binlog_use_large_read_buffer`   |   Yes   |   Only affects clusters that use binary log \(binlog\) replication\. For information about binlog replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\. Removed from Aurora MySQL version 3\.   | 
|   `aurora_enable_replica_log_compression`   |   Yes   |   For more information, see [Performance considerations for Amazon Aurora MySQL replication](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Performance)\. Doesn't apply to clusters that are part of an Aurora global database\. Removed from Aurora MySQL version 3\.   | 
|   `aurora_enable_repl_bin_log_filtering`   |   Yes   |   For more information, see [Performance considerations for Amazon Aurora MySQL replication](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Performance)\. Doesn't apply to clusters that are part of an Aurora global database\. Removed from Aurora MySQL version 3\.   | 
|  `aurora_enable_staggered_replica_restart`  |  Yes  | This setting is available in Aurora MySQL versions 1 and 3, but it isn't used\. | 
|   `aurora_enable_zdr`   |   Yes   |   This setting is turned on by default in Aurora MySQL 2\.10 and higher\. For more information, see [Zero\-downtime restart \(ZDR\) for Amazon Aurora MySQL](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Availability)\.   | 
|   `aurora_load_from_s3_role`   |   Yes   |   For more information, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\. Currently not available in Aurora MySQL version 3\. Use `aws_default_s3_role`\.  | 
|  `aurora_mask_password_hashes_type`  |  Yes  |  This setting is turned on by default in Aurora MySQL 2\.11 and higher\. Use this setting to mask Aurora MySQL password hashes in the general, slow query, and audit logs\. The allowed values are `0` and `1` \(default\)\. When set to `1`, passwords are logged as `<secret>`\. When set to `0`, passwords are logged as hash \(`#`\) values\.  | 
|   `aurora_select_into_s3_role`   |   Yes   |   For more information, see [Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\. Currently not available in Aurora MySQL version 3\. Use `aws_default_s3_role`\.  | 
|   `auto_increment_increment`   |   Yes   |    | 
|   `auto_increment_offset`   |   Yes   |    | 
|   `aws_default_lambda_role`   |   Yes   |   For more information, see [Invoking a Lambda function from an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Lambda.md)\.   | 
|  `aws_default_s3_role`  | Yes |  Used when invoking the `LOAD DATA FROM S3`, `LOAD XML FROM S3`, or `SELECT INTO OUTFILE S3` statement from your DB cluster\. In Aurora MySQL version 1 and 2, the IAM role specified in this parameter is used if an IAM role isn't specified for `aurora_load_from_s3_role` or `aurora_select_into_s3_role` for the appropriate statement\. In Aurora MySQL version 3, the IAM role specified for this parameter is always used\. For more information, see [Associating an IAM role with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.  | 
|   `binlog_checksum`   |   Yes   |   The AWS CLI and RDS API report a value of `None` if this parameter isn't set\. In that case, Aurora MySQL uses the engine default value, which is `CRC32`\. This is different than the explicit setting of `NONE`, which turns off the checksum\. For a bug fix related to this parameter, see [ Aurora MySQL database engine updates 2020\-09\-02 \(version 1\.23\.0\)](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.1230.html) and [ Aurora MySQL database engine updates 2020\-03\-05 \(version 1\.22\.2\)](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.1222.html) in the *Release Notes for Aurora MySQL*\.   | 
|   `binlog-do-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_format`   |   Yes   |   For more information, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\.   | 
|   `binlog_group_commit_sync_delay`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_group_commit_sync_no_delay_count`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog-ignore-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_row_image`   |   No   |    | 
|   `binlog_row_metadata`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_row_value_options`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_rows_query_log_events`   |   Yes   |    | 
|   `binlog_transaction_compression`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_transaction_compression_level_zstd`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_transaction_dependency_history_size`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_transaction_dependency_tracking`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `character-set-client-handshake`   |   Yes   |    | 
|   `character_set_client`   |   Yes   |    | 
|   `character_set_connection`   |   Yes   |    | 
|   `character_set_database`   |   Yes   |    | 
|   `character_set_filesystem`   |   Yes   |    | 
|   `character_set_results`   |   Yes   |    | 
|   `character_set_server`   |   Yes   |    | 
|   `collation_connection`   |   Yes   |    | 
|   `collation_server`   |   Yes   |    | 
|   `completion_type`   |   Yes   |    | 
|   `default_storage_engine`   |   No   |   Aurora MySQL clusters use the InnoDB storage engine for all of your data\.   | 
|   `enforce_gtid_consistency`   |   Sometimes   |  Modifiable in Aurora MySQL version 2\.04 and later\.  | 
|  `event_scheduler`  |  Yes  |  Indicates the status of the Event Scheduler\. Modifiable only at the cluster level in Aurora MySQL version 3\.  | 
|   `gtid-mode`   |   Sometimes   |  Modifiable in Aurora MySQL version 2\.04 and later\.  | 
|   `init_connect`   |   Yes   |  The command to be run by the server for each client that connects\. Use double quotes \("\) for settings to avoid connection failures, for example: <pre>SET optimizer_switch="hash_join=off"</pre> In Aurora MySQL version 3, this parameter doesn't apply for users who have the `CONNECTION_ADMIN` privilege\. This includes the Aurora master user\. For more information, see [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\.  | 
|   `innodb_autoinc_lock_mode`   |   Yes   |    | 
|   `innodb_checksums`   |   No   | Removed from Aurora MySQL version 3\.  | 
|   `innodb_cmp_per_index_enabled`   |   Yes   |    | 
|   `innodb_commit_concurrency`   |   Yes   |    | 
|   `innodb_data_home_dir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `innodb_deadlock_detect`   |  Yes  |  This option is used to disable deadlock detection on Aurora MySQL version 3\. On high\-concurrency systems, deadlock detection can cause a slowdown when numerous threads wait for the same lock\. Consult the MySQL documentation for more information on this parameter\.  | 
|  `innodb_default_row_format`  | Yes |  This parameter defines the default row format for InnoDB tables \(including user\-created InnoDB temporary tables\)\. It applies to Aurora MySQL versions 2 and 3, but not version 1\. Its value can be `DYNAMIC`, `COMPACT`, or `REDUNDANT.`  | 
|   `innodb_file_per_table`   |   Yes   |  This parameter affects how table storage is organized\. For more information, see [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling)\.  | 
|  `innodb_flush_log_at_trx_commit`  |  Aurora MySQL version 1 and 2: Yes Aurora MySQL version 3: No  |  For Aurora MySQL version 1 and 2, we highly recommend that you use the default value of 1\. For Aurora MySQL version 3, Aurora always uses the default value of 1\. For more information, see [Configuring how frequently the log buffer is flushed](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.Flush)\.  | 
|   `innodb_ft_max_token_size`   |   Yes   |    | 
|   `innodb_ft_min_token_size`   |   Yes   |    | 
|   `innodb_ft_num_word_optimize`   |   Yes   |    | 
|   `innodb_ft_sort_pll_degree`   |   Yes   |    | 
|   `innodb_online_alter_log_max_size`   |   Yes   |    | 
|   `innodb_optimize_fulltext_only`   |   Yes   |    | 
|   `innodb_page_size`   |   No   |    | 
|   `innodb_purge_batch_size`   |   Yes   |    | 
|   `innodb_purge_threads`   |   Yes   |    | 
|   `innodb_rollback_on_timeout`   |   Yes   |    | 
|   `innodb_rollback_segments`   |   Yes   |    | 
|   `innodb_spin_wait_delay`   |   Yes   |    | 
|   `innodb_strict_mode`   |   Yes   |    | 
|   `innodb_support_xa`   |   Yes   |  Removed from Aurora MySQL version 3\.   | 
|   `innodb_sync_array_size`   |   Yes   |    | 
|   `innodb_sync_spin_loops`   |   Yes   |    | 
|   `innodb_table_locks`   |   Yes   |    | 
|   `innodb_undo_directory`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `innodb_undo_logs`   |   Yes   |  Removed from Aurora MySQL version 3\.   | 
|   `innodb_undo_tablespaces`   |   No   |  Removed from Aurora MySQL version 3\.   | 
|   `internal_tmp_mem_storage_engine`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|  `key_buffer_size`  |   Yes   |  Key cache for MyISAM tables\. For more information, see [keycache\->cache\_lock mutex](#key-cache.cache-lock)\.  | 
|   `lc_time_names`   |   Yes   |    | 
|   `lower_case_table_names`   |   Yes \(Aurora MySQL version 1 and 2\), only at cluster creation time \(Aurora MySQL version 3\)   |   In Aurora MySQL version 2\.10 and higher 2\.x versions, make sure to reboot all reader instances after changing this setting and rebooting the writer instance\. For details, see [Rebooting an Aurora MySQL cluster \(version 2\.10 and higher\)](USER_RebootCluster.md#aurora-mysql-survivable-replicas)\. In Aurora MySQL version 3, the value of this parameter is set permanently at the time the cluster is created\. If you use a nondefault value for this option, set up your Aurora MySQL version 3 custom parameter group before upgrading, and specify the parameter group during the snapshot restore operation that creates the version 3 cluster\.   | 
|   `master-info-repository`   |   Yes   |  Removed from Aurora MySQL version 3\.   | 
|   `master_verify_checksum`   |   Yes   |   Aurora MySQL version 1 and 2\. Use `source_verify_checksum` in Aurora MySQL version 3\.   | 
|   `partial_revokes`   |   No   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `read_only`   |   Yes   |  When this parameter is turned on, the server permits no updates except from those performed by replica threads\. In Aurora MySQL version 3, this parameter doesn't apply for users who have the `CONNECTION_ADMIN` privilege\. This includes the Aurora master user\. For more information, see [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\.  | 
|   `relay-log-space-limit`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replica_preserve_commit_order`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replica_transaction_retries`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replicate-do-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replicate-do-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replicate-ignore-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replicate-ignore-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replicate-wild-do-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `replicate-wild-ignore-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `require_secure_transport`   |   Yes   |   For more information, see [Using SSL/TLS with Aurora MySQL DB clusters](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL)\.   | 
|   `rpl_read_size`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `server_audit_events`   |   Yes   |    | 
|   `server_audit_excl_users`   |   Yes   |    | 
|   `server_audit_incl_users`   |   Yes   |    | 
|   `server_audit_logging`   |   Yes   |   For instructions on uploading the logs to Amazon CloudWatch Logs, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.   | 
|   `server_id`   |   No   |    | 
|   `skip-character-set-client-handshake`   |   Yes   |    | 
|   `skip_name_resolve`   |   No   |    | 
|   `slave-skip-errors`   |   Yes   |   Only applies to Aurora MySQL version 2 clusters, with MySQL 5\.7 compatibility\.   | 
|   `source_verify_checksum`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `sync_frm`   |   Yes   |  Removed from Aurora MySQL version 3\.   | 
|   `time_zone`   |   Yes   |    | 
|   `tls_version`   |   Yes   |   For more information, see [TLS versions for Aurora MySQL](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL.TLS_Version)\.   | 

### Instance\-level parameters<a name="AuroraMySQL.Reference.Parameters.Instance"></a><a name="instance_params"></a><a name="db_params"></a>

 The following table shows all of the parameters that apply to a specific DB instance in an Aurora MySQL DB cluster\. 


|  Parameter name  |  Modifiable  |  Notes  | 
| --- | --- | --- | 
|   `activate_all_roles_on_login`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `allow-suspicious-udfs`   |   No   |    | 
|   `aurora_lab_mode`   |   Yes   |   For more information, see [Amazon Aurora MySQL lab mode](AuroraMySQL.Updates.LabMode.md)\. Removed from Aurora MySQL version 3\.   | 
|   `aurora_oom_response`   |   Yes   |  This parameter is supported for Aurora MySQL version 1\.18 and higher, and version 2\.04\.5 and higher\. It isn't supported for version 3\. For more information, see [ Amazon Aurora MySQL out\-of\-memory issues ](CHAP_Troubleshooting.md#CHAP_Troubleshooting.AuroraMySQLOOM)\.  | 
|   `aurora_parallel_query`   |   Yes   |  Set to `ON` to turn on parallel query in Aurora MySQL versions 1\.23 and 2\.09 or higher\. The old `aurora_pq` parameter isn't used in these versions\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.  | 
|   `aurora_pq`   |   Yes   |  Set to `OFF` to turn off parallel query for specific DB instances in Aurora MySQL versions before 1\.23 and 2\.09\. In 1\.23 and 2\.09 or higher, turn parallel query on and off with `aurora_parallel_query` instead\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.  | 
|   `autocommit`   |   Yes   |    | 
|   `automatic_sp_privileges`   |   Yes   |    | 
|   `back_log`   |   Yes   |    | 
|   `basedir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `binlog_cache_size`   |   Yes   |    | 
|   `binlog_max_flush_queue_time`   |   Yes   |    | 
|   `binlog_order_commits`   |   Yes   |    | 
|   `binlog_stmt_cache_size`   |   Yes   |    | 
|   `binlog_transaction_compression`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `binlog_transaction_compression_level_zstd`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `bulk_insert_buffer_size`   |   Yes   |    | 
|   `concurrent_insert`   |   Yes   |    | 
|   `connect_timeout`   |   Yes   |    | 
|   `core-file`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `datadir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `default_authentication_plugin`   |   No   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `default_time_zone`   |   No   |    | 
|   `default_tmp_storage_engine`   |   Yes   |    | 
|   `default_week_format`   |   Yes   |    | 
|   `delay_key_write`   |   Yes   |    | 
|   `delayed_insert_limit`   |   Yes   |    | 
|   `delayed_insert_timeout`   |   Yes   |    | 
|   `delayed_queue_size`   |   Yes   |    | 
|   `div_precision_increment`   |   Yes   |    | 
|   `end_markers_in_json`   |   Yes   |    | 
|   `eq_range_index_dive_limit`   |   Yes   |    | 
|   `event_scheduler`   |  Sometimes  |  Indicates the status of the Event Scheduler\. Modifiable only at the cluster level in Aurora MySQL version 3\.  | 
|   `explicit_defaults_for_timestamp`   |   Yes   |    | 
|   `flush`   |   No   |    | 
|   `flush_time`   |   Yes   |    | 
|   `ft_boolean_syntax`   |   No   |    | 
|   `ft_max_word_len`   |   Yes   |    | 
|   `ft_min_word_len`   |   Yes   |    | 
|   `ft_query_expansion_limit`   |   Yes   |    | 
|   `ft_stopword_file`   |   Yes   |    | 
|   `general_log`   |   Yes   |   For instructions on uploading the logs to CloudWatch Logs, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.   | 
|   `general_log_file`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `group_concat_max_len`   |   Yes   |    | 
|   `host_cache_size`   |   Yes   |    | 
|   `init_connect`   |   Yes   |  The command to be run by the server for each client that connects\. Use double quotes \("\) for settings to avoid connection failures, for example: <pre>SET optimizer_switch="hash_join=off"</pre> In Aurora MySQL version 3, this parameter doesn't apply for users who have the `CONNECTION_ADMIN` privilege\. This includes the Aurora master user\. For more information, see [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\.  | 
|   `innodb_adaptive_hash_index`   |   Yes   |  | 
|   `innodb_adaptive_max_sleep_delay`   |   Yes   |   Modifying this parameter has no effect, because `innodb_thread_concurrency` is always 0 for Aurora\.   | 
|   `innodb_autoextend_increment`   |   Yes   |    | 
|   `innodb_buffer_pool_dump_at_shutdown`   |   No   |    | 
|   `innodb_buffer_pool_dump_now`   |   No   |    | 
|   `innodb_buffer_pool_filename`   |   No   |    | 
|   `innodb_buffer_pool_load_abort`   |   No   |    | 
|   `innodb_buffer_pool_load_at_startup`   |   No   |    | 
|   `innodb_buffer_pool_load_now`   |   No   |    | 
|   `innodb_buffer_pool_size`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\.  | 
|   `innodb_change_buffer_max_size`   |   No   |   Aurora MySQL doesn't use the InnoDB change buffer at all\.   | 
|   `innodb_compression_failure_threshold_pct`   |   Yes   |    | 
|   `innodb_compression_level`   |   Yes   |    | 
|   `innodb_compression_pad_pct_max`   |   Yes   |    | 
|   `innodb_concurrency_tickets`   |   Yes   |   Modifying this parameter has no effect, because `innodb_thread_concurrency` is always 0 for Aurora\.   | 
|   `innodb_deadlock_detect`   |  Yes  |  This option is used to disable deadlock detection on Aurora MySQL version 3\. On high\-concurrency systems, deadlock detection can cause a slowdown when numerous threads wait for the same lock\. Consult the MySQL documentation for more information on this parameter\.  | 
|   `innodb_file_format`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `innodb_flushing_avg_loops`   |   No   |    | 
|   `innodb_force_load_corrupted`   |   No   |    | 
|   `innodb_ft_aux_table`   |   Yes   |    | 
|   `innodb_ft_cache_size`   |   Yes   |    | 
|   `innodb_ft_enable_stopword`   |   Yes   |    | 
|   `innodb_ft_server_stopword_table`   |   Yes   |    | 
|   `innodb_ft_user_stopword_table`   |   Yes   |    | 
|   `innodb_large_prefix`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `innodb_lock_wait_timeout`   |   Yes   |    | 
|   `innodb_log_compressed_pages`   |   No   |    | 
|   `innodb_lru_scan_depth`   |   Yes   |    | 
|   `innodb_max_purge_lag`   |   Yes   |    | 
|   `innodb_max_purge_lag_delay`   |   Yes   |    | 
|   `innodb_monitor_disable`   |   Yes   |    | 
|   `innodb_monitor_enable`   |   Yes   |    | 
|   `innodb_monitor_reset`   |   Yes   |    | 
|   `innodb_monitor_reset_all`   |   Yes   |    | 
|   `innodb_old_blocks_pct`   |   Yes   |    | 
|   `innodb_old_blocks_time`   |   Yes   |    | 
|   `innodb_open_files`   |   Yes   |    | 
|   `innodb_print_all_deadlocks`   |   Yes   |    | 
|   `innodb_random_read_ahead`   |   Yes   |    | 
|   `innodb_read_ahead_threshold`   |   Yes   |    | 
|   `innodb_read_io_threads`   |   No   |    | 
|   `innodb_read_only`   |   No   |   Aurora MySQL manages the read\-only and read/write state of DB instances based on the type of cluster\. For example, a provisioned cluster has one read/write DB instance \(the *primary instance*\) and any other instances in the cluster are read\-only \(the Aurora Replicas\)\.   | 
|   `innodb_replication_delay`   |   Yes   |    | 
|   `innodb_sort_buffer_size`   |   Yes   |    | 
|   `innodb_stats_auto_recalc`   |   Yes   |    | 
|   `innodb_stats_method`   |   Yes   |    | 
|   `innodb_stats_on_metadata`   |   Yes   |    | 
|   `innodb_stats_persistent`   |   Yes   |    | 
|   `innodb_stats_persistent_sample_pages`   |   Yes   |    | 
|   `innodb_stats_transient_sample_pages`   |   Yes   |    | 
|   `innodb_thread_concurrency`   |   No   |    | 
|   `innodb_thread_sleep_delay`   |   Yes   |   Modifying this parameter has no effect, because `innodb_thread_concurrency` is always 0 for Aurora\.   | 
|   `interactive_timeout`   |   Yes   |   Aurora evaluates the minimum value of `interactive_timeout` and `wait_timeout`\. It then uses that minimum as the timeout to end all idle sessions, both interactive and noninteractive\.   | 
|   `internal_tmp_mem_storage_engine`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\.   | 
|   `join_buffer_size`   |   Yes   |    | 
|   `keep_files_on_create`   |   Yes   |    | 
|  `key_buffer_size`  |   Yes   |  Key cache for MyISAM tables\. For more information, see [keycache\->cache\_lock mutex](#key-cache.cache-lock)\.  | 
|   `key_cache_age_threshold`   |   Yes   |    | 
|   `key_cache_block_size`   |   Yes   |    | 
|   `key_cache_division_limit`   |   Yes   |    | 
|   `local_infile`   |   Yes   |    | 
|   `lock_wait_timeout`   |   Yes   |    | 
|   `log-bin`   |   No   |   Setting `binlog_format` to `STATEMENT`, `MIXED`, or `ROW` automatically sets `log-bin` to `ON`\. Setting `binlog_format` to `OFF` automatically sets `log-bin` to `OFF`\. For more information, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\.   | 
|   `log_bin_trust_function_creators`   |   Yes   |    | 
|   `log_bin_use_v1_row_events`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `log_error`   |   No   |    | 
|   `log_output`   |   Yes   |    | 
|   `log_queries_not_using_indexes`   |   Yes   |    | 
|   `log_slave_updates`   |   No   |   Aurora MySQL version 1 and 2\. Use `log_replica_updates` in Aurora MySQL version 3\.   | 
|   `log_replica_updates`   |   No   |   Aurora MySQL version 3 and higher   | 
|   `log_throttle_queries_not_using_indexes`   |   Yes   |    | 
|   `log_warnings`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `long_query_time`   |   Yes   |    | 
|   `low_priority_updates`   |   Yes   |    | 
|   `max_allowed_packet`   |   Yes   |    | 
|   `max_binlog_cache_size`   |   Yes   |    | 
|   `max_binlog_size`   |   No   |    | 
|   `max_binlog_stmt_cache_size`   |   Yes   |    | 
|   `max_connect_errors`   |   Yes   |    | 
|   `max_connections`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\. For the default values depending on the instance class, see [Maximum connections to an Aurora MySQL DB instance](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.MaxConnections)\.   | 
|   `max_delayed_threads`   |   Yes   |    | 
|   `max_error_count`   |   Yes   |    | 
|   `max_heap_table_size`   |   Yes   |    | 
|   `max_insert_delayed_threads`   |   Yes   |    | 
|   `max_join_size`   |   Yes   |    | 
|   `max_length_for_sort_data`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `max_prepared_stmt_count`   |   Yes   |    | 
|   `max_seeks_for_key`   |   Yes   |    | 
|   `max_sort_length`   |   Yes   |    | 
|   `max_sp_recursion_depth`   |   Yes   |    | 
|   `max_tmp_tables`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `max_user_connections`   |   Yes   |    | 
|   `max_write_lock_count`   |   Yes   |    | 
|   `metadata_locks_cache_size`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `min_examined_row_limit`   |   Yes   |    | 
|   `myisam_data_pointer_size`   |   Yes   |    | 
|   `myisam_max_sort_file_size`   |   Yes   |    | 
|   `myisam_mmap_size`   |   Yes   |    | 
|   `myisam_sort_buffer_size`   |   Yes   |    | 
|   `myisam_stats_method`   |   Yes   |    | 
|   `myisam_use_mmap`   |   Yes   |    | 
|   `net_buffer_length`   |   Yes   |    | 
|   `net_read_timeout`   |   Yes   |    | 
|   `net_retry_count`   |   Yes   |    | 
|   `net_write_timeout`   |   Yes   |    | 
|   `old-style-user-limits`   |   Yes   |    | 
|   `old_passwords`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `optimizer_prune_level`   |   Yes   |    | 
|   `optimizer_search_depth`   |   Yes   |    | 
|   `optimizer_switch`   |   Yes   |   For information about Aurora MySQL features that use this switch, see [Best practices with Amazon Aurora MySQL](AuroraMySQL.BestPractices.md)\.   | 
|   `optimizer_trace`   |   Yes   |    | 
|   `optimizer_trace_features`   |   Yes   |    | 
|   `optimizer_trace_limit`   |   Yes   |    | 
|   `optimizer_trace_max_mem_size`   |   Yes   |    | 
|   `optimizer_trace_offset`   |   Yes   |    | 
|   `performance-schema-consumer-events-waits-current`   |   Yes   |    | 
|   `performance-schema-instrument`   |   Yes   |    | 
|   `performance_schema`   |   Yes   |    | 
|   `performance_schema_accounts_size`   |   Yes   |    | 
|   `performance_schema_consumer_global_instrumentation`   |   Yes   |    | 
|   `performance_schema_consumer_thread_instrumentation`   |   Yes   |    | 
|   `performance_schema_consumer_events_stages_current`   |   Yes   |    | 
|   `performance_schema_consumer_events_stages_history`   |   Yes   |    | 
|   `performance_schema_consumer_events_stages_history_long`   |   Yes   |    | 
|   `performance_schema_consumer_events_statements_current`   |   Yes   |    | 
|   `performance_schema_consumer_events_statements_history`   |   Yes   |    | 
|   `performance_schema_consumer_events_statements_history_long`   |   Yes   |    | 
|   `performance_schema_consumer_events_waits_history`   |   Yes   |    | 
|   `performance_schema_consumer_events_waits_history_long`   |   Yes   |    | 
|   `performance_schema_consumer_statements_digest`   |   Yes   |    | 
|   `performance_schema_digests_size`   |   Yes   |    | 
|   `performance_schema_events_stages_history_long_size`   |   Yes   |    | 
|   `performance_schema_events_stages_history_size`   |   Yes   |    | 
|   `performance_schema_events_statements_history_long_size`   |   Yes   |    | 
|   `performance_schema_events_statements_history_size`   |   Yes   |    | 
|   `performance_schema_events_transactions_history_long_size`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_events_transactions_history_size`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_events_waits_history_long_size`   |   Yes   |    | 
|   `performance_schema_events_waits_history_size`   |   Yes   |    | 
|   `performance_schema_hosts_size`   |   Yes   |    | 
|   `performance_schema_max_cond_classes`   |   Yes   |    | 
|   `performance_schema_max_cond_instances`   |   Yes   |    | 
|   `performance_schema_max_digest_length`   |   Yes   |    | 
|   `performance_schema_max_file_classes`   |   Yes   |    | 
|   `performance_schema_max_file_handles`   |   Yes   |    | 
|   `performance_schema_max_file_instances`   |   Yes   |    | 
|   `performance_schema_max_index_stat`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_memory_classes`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_metadata_locks`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_mutex_classes`   |   Yes   |    | 
|   `performance_schema_max_mutex_instances`   |   Yes   |    | 
|   `performance_schema_max_prepared_statements_instances`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_program_instances`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_rwlock_classes`   |   Yes   |    | 
|   `performance_schema_max_rwlock_instances`   |   Yes   |    | 
|   `performance_schema_max_socket_classes`   |   Yes   |    | 
|   `performance_schema_max_socket_instances`   |   Yes   |    | 
|   `performance_schema_max_sql_text_length`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_stage_classes`   |   Yes   |    | 
|   `performance_schema_max_statement_classes`   |   Yes   |    | 
|   `performance_schema_max_statement_stack`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_table_handles`   |   Yes   |    | 
|   `performance_schema_max_table_instances`   |   Yes   |    | 
|   `performance_schema_max_table_lock_stat`   |   Yes   |   Aurora MySQL 2\.x only   | 
|   `performance_schema_max_thread_classes`   |   Yes   |    | 
|   `performance_schema_max_thread_instances`   |   Yes   |    | 
|   `performance_schema_session_connect_attrs_size`   |   Yes   |    | 
|   `performance_schema_setup_actors_size`   |   Yes   |    | 
|   `performance_schema_setup_objects_size`   |   Yes   |    | 
|   `performance_schema_users_size`   |   Yes   |    | 
|   `pid_file`   |   No   |    | 
|   `plugin_dir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `port`   |   No   |   Aurora MySQL manages the connection properties and enforces consistent settings for all DB instances in a cluster\.   | 
|   `preload_buffer_size`   |   Yes   |    | 
|   `profiling_history_size`   |   Yes   |    | 
|   `query_alloc_block_size`   |   Yes   |    | 
|   `query_cache_limit`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `query_cache_min_res_unit`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `query_cache_size`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\.  Removed from Aurora MySQL version 3\.   | 
|   `query_cache_type`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `query_cache_wlock_invalidate`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `query_prealloc_size`   |   Yes   |    | 
|   `range_alloc_block_size`   |   Yes   |    | 
|   `read_buffer_size`   |   Yes   |    | 
|   `read_only`   |   Yes   |  When this parameter is turned on, the server permits no updates except from those performed by replica threads\. Removed at the instance level from Aurora MySQL version 3\.  | 
|   `read_rnd_buffer_size`   |   Yes   |    | 
|   `relay-log`   |   No   |    | 
|   `relay_log_info_repository`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `relay_log_recovery`   |   No   |    | 
|   `safe-user-create`   |   Yes   |    | 
|   `secure_auth`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `secure_file_priv`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `skip-slave-start`   |   No   |    | 
|   `skip_external_locking`   |   No   |    | 
|   `skip_show_database`   |   Yes   |    | 
|   `slave_checkpoint_group`   |   Yes   |   Aurora MySQL version 1 and 2\. Use `replica_checkpoint_group` in Aurora MySQL version 3\.   | 
|   `replica_checkpoint_group`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `slave_checkpoint_period`   |   Yes   |   Aurora MySQL version 1 and 2\. Use `replica_checkpoint_period` in Aurora MySQL version 3\.   | 
|   `replica_checkpoint_period`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `slave_parallel_workers`   |   Yes   |   Aurora MySQL version 1 and 2\. Use `replica_parallel_workers` in Aurora MySQL version 3\.   | 
|   `replica_parallel_workers`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `slave_pending_jobs_size_max`   |   Yes   |   Aurora MySQL version 1 and 2\. Use `replica_pending_jobs_size_max` in Aurora MySQL version 3\.   | 
|   `replica_pending_jobs_size_max`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `replica_skip_errors`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `slave_sql_verify_checksum`   |   Yes   |   Aurora MySQL version 1 and 2\. Use `replica_sql_verify_checksum` in Aurora MySQL version 3\.   | 
|   `replica_sql_verify_checksum`   |   Yes   |   Aurora MySQL version 3 and higher   | 
|   `slow_launch_time`   |   Yes   |    | 
|   `slow_query_log`   |   Yes   |   For instructions on uploading the logs to CloudWatch Logs, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.   | 
|   `slow_query_log_file`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `socket`   |   No   |    | 
|   `sort_buffer_size`   |   Yes   |    | 
|   `sql_mode`   |   Yes   |    | 
|   `sql_select_limit`   |   Yes   |    | 
|   `stored_program_cache`   |   Yes   |    | 
|   `sync_binlog`   |   No   |    | 
|   `sync_master_info`   |   Yes   |    | 
|   `sync_source_info`   |   Yes   |   This parameter applies to Aurora MySQL 3 and higher\.   | 
|   `sync_relay_log`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `sync_relay_log_info`   |   Yes   |    | 
|   `sysdate-is-now`   |   Yes   |    | 
|   `table_cache_element_entry_ttl`   |   No   |    | 
|   `table_definition_cache`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\.  | 
|   `table_open_cache`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\.  | 
|   `table_open_cache_instances`   |   Yes   |    | 
|   `temp-pool`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `temptable_max_mmap`   |   Yes   |  This parameter applies to Aurora MySQL version 3 and higher\. For details, see [New temporary table behavior in Aurora MySQL version 3](ams3-temptable-behavior.md)\.  | 
|  `temptable_max_ram`  |  Yes  |  This parameter applies to Aurora MySQL version 3 and higher\. For details, see [New temporary table behavior in Aurora MySQL version 3](ams3-temptable-behavior.md)\.  | 
|  `temptable_use_mmap`  |  Yes  |  This parameter applies to Aurora MySQL version 3 and higher\. For details, see [New temporary table behavior in Aurora MySQL version 3](ams3-temptable-behavior.md)\.  | 
|  `thread_handling`  |  No  |    | 
|   `thread_stack`   |  Yes  |    | 
|   `timed_mutexes`   |  Yes  |    | 
|  `tmp_table_size`  |  Yes  |    | 
|   `tmpdir`   |  No  |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `transaction_alloc_block_size`   |   Yes   |    | 
|   `transaction_isolation`   |   Yes   |   This parameter applies to Aurora MySQL version 3 and higher\. It replaces `tx_isolation`\.   | 
|   `transaction_prealloc_size`   |   Yes   |    | 
|   `tx_isolation`   |   Yes   |   Removed from Aurora MySQL version 3\. It is replaced by `transaction_isolation`\.   | 
|   `updatable_views_with_limit`   |   Yes   |    | 
|   `validate-password`   |   No   |    | 
|   `validate_password_dictionary_file`   |   No   |    | 
|   `validate_password_length`   |   No   |    | 
|   `validate_password_mixed_case_count`   |   No   |    | 
|   `validate_password_number_count`   |   No   |    | 
|   `validate_password_policy`   |   No   |    | 
|   `validate_password_special_char_count`   |   No   |    | 
|   `wait_timeout`   |   Yes   |  Aurora evaluates the minimum value of `interactive_timeout` and `wait_timeout`\. It then uses that minimum as the timeout to end all idle sessions, both interactive and noninteractive\.   | 

## MySQL parameters that don't apply to Aurora MySQL<a name="AuroraMySQL.Reference.Parameters.Inapplicable"></a>

 Because of architectural differences between Aurora MySQL and MySQL, some MySQL parameters don't apply to Aurora MySQL\. 

The following MySQL parameters don't apply to Aurora MySQL\. This list is not exhaustive\.
+ `activate_all_roles_on_login` – This parameter doesn't apply to Aurora MySQL version 1 and 2\. It is available in Aurora MySQL version 3\.
+ `big_tables`
+ `bind_address`
+ `character_sets_dir`
+ `innodb_adaptive_flushing`
+ `innodb_adaptive_flushing_lwm`
+ `innodb_buffer_pool_chunk_size`
+ `innodb_buffer_pool_instances`
+ `innodb_change_buffering`
+ `innodb_checksum_algorithm`
+ `innodb_data_file_path`
+ `innodb_deadlock_detect` – This parameter doesn't apply to Aurora MySQL version 1 and 2\. It is available in Aurora MySQL version 3\.
+ `innodb_dedicated_server`
+ `innodb_doublewrite`
+ `innodb_flush_log_at_timeout` – This parameter doesn't apply to Aurora MySQL\. For more information, see [Configuring how frequently the log buffer is flushed](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.Flush)\.
+ `innodb_flush_method`
+ `innodb_flush_neighbors`
+ `innodb_io_capacity`
+ `innodb_io_capacity_max`
+ `innodb_log_buffer_size`
+ `innodb_log_file_size`
+ `innodb_log_files_in_group`
+ `innodb_log_spin_cpu_abs_lwm`
+ `innodb_log_spin_cpu_pct_hwm`
+ `innodb_max_dirty_pages_pct`
+ `innodb_numa_interleave`
+ `innodb_page_size`
+ `innodb_redo_log_capacity`
+ `innodb_redo_log_encrypt`
+ `innodb_undo_log_encrypt`
+ `innodb_undo_log_truncate`
+ `innodb_use_native_aio`
+ `innodb_write_io_threads`
+ `thread_cache_size`

## MySQL status variables that don't apply to Aurora MySQL<a name="AuroraMySQL.Reference.StatusVars.Inapplicable"></a><a name="status_vars"></a>

 Because of architectural differences between Aurora MySQL and MySQL, some MySQL status variables don't apply to Aurora MySQL\. 

 The following MySQL status variables don't apply to Aurora MySQL\. This list is not exhaustive\. 
+  `innodb_buffer_pool_bytes_dirty` 
+  `innodb_buffer_pool_pages_dirty` 
+  `innodb_buffer_pool_pages_flushed` 

 Aurora MySQL version 3 removes the following status variables that were in Aurora MySQL version 2: 
+  `AuroraDb_lockmgr_bitmaps0_in_use` 
+  `AuroraDb_lockmgr_bitmaps1_in_use` 
+  `AuroraDb_lockmgr_bitmaps_mem_used` 
+  `AuroraDb_thread_deadlocks` 
+  `available_alter_table_log_entries` 
+  `Aurora_lockmgr_memory_used` 
+  `Aurora_missing_history_on_replica_incidents` 
+  `Aurora_new_lock_manager_lock_release_cnt` 
+  `Aurora_new_lock_manager_lock_release_total_duration_micro` 
+  `Aurora_new_lock_manager_lock_timeout_cnt` 
+  `Aurora_oom_response` 
+  `Aurora_total_op_memory` 
+  `Aurora_total_op_temp_space` 
+  `Aurora_used_alter_table_log_entries` 
+  `Aurora_using_new_lock_manager` 
+  `Aurora_volume_bytes_allocated` 
+  `Aurora_volume_bytes_left_extent` 
+  `Aurora_volume_bytes_left_total` 
+  `Com_alter_db_upgrade` 
+  `Compression` 
+  `External_threads_connected` 
+  `Innodb_available_undo_logs` 
+  `Last_query_cost` 
+  `Last_query_partial_plans` 
+  `Slave_heartbeat_period` 
+  `Slave_last_heartbeat` 
+  `Slave_received_heartbeats` 
+  `Slave_retried_transactions` 
+  `Slave_running` 
+  `Time_since_zero_connections` 

 These MySQL status variables are available in Aurora MySQL version 1 or 2, but they aren't available in Aurora MySQL version 3: 
+  `Innodb_redo_log_enabled` 
+  `Innodb_undo_tablespaces_total` 
+  `Innodb_undo_tablespaces_implicit` 
+  `Innodb_undo_tablespaces_explicit` 
+  `Innodb_undo_tablespaces_active` 

## Aurora MySQL wait events<a name="AuroraMySQL.Reference.Waitevents"></a>

The following are some common wait events for Aurora MySQL\.

**Note**  
For information about the naming conventions used in MySQL wait events, see [ Performance Schema instrument naming conventions](https://dev.mysql.com/doc/refman/5.6/en/performance-schema-instrument-naming.html) in the MySQL documentation\.

**cpu**  
The number of active connections that are ready to run is consistently higher than the number of vCPUs\. For more information, see [cpu](ams-waits.cpu.md)\.

**io/aurora\_redo\_log\_flush**  
A session is persisting data to Aurora storage\. Typically, this wait event is for a write I/O operation in Aurora MySQL\. For more information, see [io/aurora\_redo\_log\_flush](ams-waits.io-auredologflush.md)\.

**io/aurora\_respond\_to\_client**  
Query processing has completed and results are being returned to the application client for the following Aurora MySQL versions: 2\.10\.2 and higher 2\.10 versions, 2\.09\.3 and higher 2\.09 versions, 2\.07\.7 and higher 2\.07 versions, and 1\.22\.6 and higher 1\.22 versions\. Compare the network bandwidth of the DB instance class with the size of the result set being returned\. Also, check client\-side response times\. If the client is unresponsive and can't process the TCP packets, packet drops and TCP retransmissions can occur\. This situation negatively affects network bandwidth\. In versions lower than 2\.10\.2, 2\.09\.3, 2\.07\.7, and 1\.22\.6, the wait event erroneously includes idle time\. To learn how to tune your database when this wait is prominent, see [io/aurora\_respond\_to\_client](ams-waits.resond-to-client.md)\.

**io/file/csv/data**  
Threads are writing to tables in comma\-separated value \(CSV\) format\. Check your CSV table usage\. A typical cause of this event is setting `log_output` on a table\.

**io/file/innodb/innodb\_data\_file**  
Threads are waiting on I/O from storage\. This event is more prevalent in I/O\-intensive workloads\. When this wait event is prevalent, SQL statements might be running disk\-intensive queries or requesting data that can't be satisfied from the InnoDB buffer pool\. For more information, see [io/file/innodb/innodb\_data\_file](ams-waits.innodb-data-file.md)\.

**io/file/sql/binlog**  
A thread is waiting on a binary log \(binlog\) file that is being written to disk\.

**io/socket/sql/client\_connection**  
The `mysqld` program is busy creating threads to handle incoming new client connections\. For more information, see [io/socket/sql/client\_connection](ams-waits.client-connection.md)\.

**io/table/sql/handler**  
The engine is waiting for access to a table\. This event occurs regardless of whether the data is cached in the buffer pool or accessed on disk\. For more information, see [io/table/sql/handler](ams-waits.waitio.md)\.

**lock/table/sql/handler**  
This wait event is a table lock wait event handler\. For more information about atom and molecule events in the Performance Schema, see [ Performance Schema atom and molecule events](https://dev.mysql.com/doc/refman/5.6/en/performance-schema-atom-molecule-events.html) in the MySQL documentation\.

**synch/cond/mysys/my\_thread\_var::suspend**  
The thread is suspended while waiting on a table\-level lock because another thread issued `LOCK TABLES ... READ`\.

**synch/cond/sql/MDL\_context::COND\_wait\_status**  
Threads are waiting on a table metadata lock\. The engine uses this type of lock to manage concurrent access to a database schema and to ensure data consistency\. For more information, see [Optimizing locking operations](https://dev.mysql.com/doc/refman/5.6/en/locking-issues.html) in the MySQL documentation\. To learn how to tune your database when this event is prominent, see [synch/cond/sql/MDL\_context::COND\_wait\_status](ams-waits.cond-wait-status.md)\.

**synch/cond/sql/MYSQL\_BIN\_LOG::COND\_done**  
You have turned on binary logging\. There might be a high commit throughput, large number transactions committing, or replicas reading binlogs\. Consider using multirow statements or bundling statements into one transaction\. In Aurora, use global databases instead of binary log replication, or use the `aurora_binlog_*` parameters\.

**synch/mutex/innodb/aurora\_lock\_thread\_slot\_futex**  
Multiple data manipulation language \(DML\) statements are accessing the same database rows at the same time\. For more information, see [synch/mutex/innodb/aurora\_lock\_thread\_slot\_futex](ams-waits.waitsynch.md)\.

**synch/mutex/innodb/buf\_pool\_mutex**  
The buffer pool isn't large enough to hold the working data set\. Or the workload accesses pages from a specific table, which leads to contention in the buffer pool\. For more information, see [synch/mutex/innodb/buf\_pool\_mutex](ams-waits.bufpoolmutex.md)\.

**synch/mutex/innodb/fil\_system\_mutex**  
The process is waiting for access to the tablespace memory cache\. For more information, see [synch/mutex/innodb/fil\_system\_mutex](ams-waits.innodb-fil-system-mutex.md)\.

**synch/mutex/innodb/os\_mutex**  
This event is part of an event semaphore\. It provides exclusive access to variables used for signaling between threads\. Uses include statistics threads, full\-text search, buffer pool dump and load operations, and log flushes\. This wait event is specific to Aurora MySQL version 1\.

**synch/mutex/innodb/trx\_sys\_mutex**  
Operations are checking, updating, deleting, or adding transaction IDs in InnoDB in a consistent or controlled manner\. These operations require a `trx_sys` mutex call, which is tracked by Performance Schema instrumentation\. Operations include management of the transaction system when the database starts or shuts down, rollbacks, undo cleanups, row read access, and buffer pool loads\. High database load with a large number of transactions results in the frequent appearance of this wait event\. For more information, see [synch/mutex/innodb/trx\_sys\_mutex](ams-waits.trxsysmutex.md)\.

**synch/mutex/mysys/KEY\_CACHE::cache\_lock**  <a name="key-cache.cache-lock"></a>
The `keycache->cache_lock` mutex controls access to the key cache for MyISAM tables\. While Aurora MySQL doesn't allow usage of MyISAM tables to store persistent data, they are used to store internal temporary tables\. Consider checking the `created_tmp_tables` or `created_tmp_disk_tables` status counters, because in certain situations, temporary tables are written to disk when they no longer fit in memory\.

**synch/mutex/sql/FILE\_AS\_TABLE::LOCK\_offsets**  
The engine acquires this mutex when opening or creating a table metadata file\. When this wait event occurs with excessive frequency, the number of tables being created or opened has spiked\. 

**synch/mutex/sql/FILE\_AS\_TABLE::LOCK\_shim\_lists**  
The engine acquires this mutex while performing operations such as `reset_size`, `detach_contents`, or `add_contents` on the internal structure that keeps track of opened tables\. The mutex synchronizes access to the list contents\. When this wait event occurs with high frequency, it indicates a sudden change in the set of tables that were previously accessed\. The engine needs to access new tables or let go of the context related to previously accessed tables\.

**synch/mutex/sql/LOCK\_open**  
The number of tables that your sessions are opening exceeds the size of the table definition cache or the table open cache\. Increase the size of these caches\. For more information, see [How MySQL opens and closes tables](https://dev.mysql.com/doc/refman/8.0/en/table-cache.html)\.

**synch/mutex/sql/LOCK\_table\_cache**  
The number of tables that your sessions are opening exceeds the size of the table definition cache or the table open cache\. Increase the size of these caches\. For more information, see [How MySQL opens and closes tables](https://dev.mysql.com/doc/refman/8.0/en/table-cache.html)\.

**synch/mutex/sql/LOG**  
In this wait event, there are threads waiting on a log lock\. For example, a thread might wait for a lock to write to the slow query log file\.

**synch/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_commit**  
In this wait event, there is a thread that is waiting to acquire a lock with the intention of committing to the binary log\. Binary logging contention can occur on databases with a very high change rate\. Depending on your version of MySQL, there are certain locks being used to protect the consistency and durability of the binary log\. In RDS for MySQL, binary logs are used for replication and the automated backup process\. In Aurora MySQL, binary logs are not needed for native replication or backups\. They are disabled by default but can be enabled and used for external replication or change data capture\. For more information, see [The binary log](https://dev.mysql.com/doc/refman/5.6/en/binary-log.html) in the MySQL documentation\.

**sync/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_dump\_thread\_metrics\_collection**  
If binary logging is turned on, the engine acquires this mutex when it prints active dump threads metrics to the engine error log and to the internal operations map\.

**sync/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_inactive\_binlogs\_map**  
If binary logging is turned on, the engine acquires this mutex when it adds to, deletes from, or searches through the list of binlog files behind the latest one\.

**sync/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_io\_cache**  
If binary logging is turned on, the engine acquires this mutex during Aurora binlog IO cache operations: allocate, resize, free, write, read, purge, and access cache info\. If this event occurs frequently, the engine is accessing the cache where binlog events are stored\. To reduce wait times, reduce commits\. Try grouping multiple statements into a single transaction\.

**synch/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_log**  
You have turned on binary logging\. There might be high commit throughput, many transactions committing, or replicas reading binlogs\. Consider using multirow statements or bundling statements into one transaction\. In Aurora, use global databases instead of binary log replication or use the `aurora_binlog_*` parameters\.

**synch/mutex/sql/SERVER\_THREAD::LOCK\_sync**  
The mutex `SERVER_THREAD::LOCK_sync` is acquired during the scheduling, processing, or launching of threads for file writes\. The excessive occurrence of this wait event indicates increased write activity in the database\.

**synch/mutex/sql/TABLESPACES:lock**  
The engine acquires the `TABLESPACES:lock` mutex during the following tablespace operations: create, delete, truncate, and extend\. The excessive occurrence of this wait event indicates a high frequency of tablespace operations\. An example is loading a large amount of data into the database\.

**synch/rwlock/innodb/dict**  
In this wait event, there are threads waiting on an rwlock held on the InnoDB data dictionary\.

**synch/rwlock/innodb/dict\_operation\_lock**  
In this wait event, there are threads holding locks on InnoDB data dictionary operations\.

**synch/rwlock/innodb/dict sys RW lock**  
A high number of concurrent data control language statements \(DCLs\) in data definition language code \(DDLs\) are triggered at the same time\. Reduce the application's dependency on DDLs during regular application activity\.

**synch/rwlock/innodb/hash\_table\_locks**  
The excessive occurrence of this wait event indicates contention when modifying the hash table that maps the buffer cache\. Consider increasing the buffer cache size and improving access paths for the relevant queries\. To learn how to tune your database when this wait is prominent, see [synch/rwlock/innodb/hash\_table\_locks](ams-waits.rw-lock-hash-table-locks.md)\.

**synch/rwlock/innodb/index\_tree\_rw\_lock**  
A large number of similar data manipulation language \(DML\) statements are accessing the same database object at the same time\. Try using multirow statements\. Also, spread the workload over different database objects\. For example, implement partitioning\. 

**synch/sxlock/innodb/dict\_operation\_lock**  
A high number of concurrent data control language statements \(DCLs\) in data definition language code \(DDLs\) are triggered at the same time\. Reduce the application's dependency on DDLs during regular application activity\.

**synch/sxlock/innodb/dict\_sys\_lock**  
A high number of concurrent data control language statements \(DCLs\) in data definition language code \(DDLs\) are triggered at the same time\. Reduce the application's dependency on DDLs during regular application activity\.

**synch/sxlock/innodb/hash\_table\_locks**  
The session couldn't find pages in the buffer pool\. The engine either needs to read a file or modify the least\-recently used \(LRU\) list for the buffer pool\. Consider increasing the buffer cache size and improving access paths for the relevant queries\.

**synch/sxlock/innodb/index\_tree\_rw\_lock**  
Many similar data manipulation language \(DML\) statements are accessing the same database object at the same time\. Try using multirow statements\. Also, spread the workload over different database objects\. For example, implement partitioning\.

For more information on troubleshooting synch wait events, see [Why is my MySQL DB instance showing a high number of active sessions waiting on SYNCH wait events in Performance Insights?](https://aws.amazon.com/premiumsupport/knowledge-center/aurora-mysql-synch-wait-events/)\.

## Aurora MySQL thread states<a name="AuroraMySQL.Reference.thread-states"></a>

The following are some common thread states for Aurora MySQL\.

**checking permissions**  
The thread is checking whether the server has the required privileges to run the statement\.

**checking query cache for query**  
The server is checking whether the current query is present in the query cache\.

**cleaned up**  
This is the final state of a connection whose work is complete but which hasn't been closed by the client\. The best solution is to explicitly close the connection in code\. Or you can set a lower value for `wait_timeout` in your parameter group\.

**closing tables**  
The thread is flushing the changed table data to disk and closing the used tables\. If this isn't a fast operation, verify the network bandwidth consumption metrics against the instance class network bandwidth\. Also, check that the parameter values for `table_open_cache` and `table_definition_cache` parameter allow for enough tables to be simultaneously open so that the engine doesn't need to open and close tables frequently\. These parameters influence the memory consumption on the instance\.

**converting HEAP to MyISAM**  
The query is converting a temporary table from in\-memory to on\-disk\. This conversion is necessary because the temporary tables created by MySQL in the intermediate steps of query processing grew too big for memory\. Check the values of `tmp_table_size` and `max_heap_table_size`\. In later versions, this thread state name is `converting HEAP to ondisk`\.

**converting HEAP to ondisk**  
The thread is converting an internal temporary table from an in\-memory table to an on\-disk table\.

**copy to tmp table**  
The thread is processing an `ALTER TABLE` statement\. This state occurs after the table with the new structure has been created but before rows are copied into it\. For a thread in this state, you can use the Performance Schema to obtain information about the progress of the copy operation\.

**creating sort index**  
Aurora MySQL is performing a sort because it can't use an existing index to satisfy the `ORDER BY` or `GROUP BY` clause of a query\. For more information, see [creating sort index](ams-states.sort-index.md)\.

**creating table**  
The thread is creating a permanent or temporary table\.

**delayed commit ok done**  
An asynchronous commit in Aurora MySQL has received an acknowledgement and is complete\.

**delayed commit ok initiated**  
The Aurora MySQL thread has started the async commit process but is waiting for acknowledgement\. This is usually the genuine commit time of a transaction\.

**delayed send ok done**  
An Aurora MySQL worker thread that is tied to a connection can be freed while a response is sent to the client\. The thread can begin other work\. The state `delayed send ok` means that the asynchronous acknowledgement to the client completed\.

**delayed send ok initiated**  
An Aurora MySQL worker thread has sent a response asynchronously to a client and is now free to do work for other connections\. The transaction has started an async commit process that hasn't yet been acknowledged\.

**executing**  
The thread has begun running a statement\.

**freeing items**  
The thread has run a command\. Some freeing of items done during this state involves the query cache\. This state is usually followed by cleaning up\.

**init**  
This state occurs before the initialization of `ALTER TABLE`, `DELETE`, `INSERT`, `SELECT`, or `UPDATE` statements\. Actions in this state include flushing the binary log or InnoDB log, and some cleanup of the query cache\.

**master has sent all binlog to slave**  
The primary node has finished its part of the replication\. The thread is waiting for more queries to run so that it can write to the binary log \(binlog\)\.

**opening tables**  
The thread is trying to open a table\. This operation is fast unless an `ALTER TABLE` or a `LOCK TABLE` statement needs to finish, or it exceeds the value of `table_open_cache`\.

**optimizing**  
The server is performing initial optimizations for a query\.

**preparing**  
This state occurs during query optimization\.

**query end**  
This state occurs after processing a query but before the freeing items state\.

**removing duplicates**  
Aurora MySQL couldn't optimize a `DISTINCT` operation in the early stage of a query\. Aurora MySQL must remove all duplicated rows before sending the result to the client\.

**searching rows for update**  
The thread is finding all matching rows before updating them\. This stage is necessary if the `UPDATE` is changing the index that the engine uses to find the rows\.

**sending binlog event to slave**  
The thread read an event from the binary log and is sending it to the replica\.

**sending cached result to client**  
The server is taking the result of a query from the query cache and sending it to the client\.

**sending data**  
The thread is reading and processing rows for a `SELECT` statement but hasn't yet started sending data to the client\. The process is identifying which pages contain the results necessary to satisfy the query\. For more information, see [sending data](ams-states.sending-data.md)\.

**sending to client**  
The server is writing a packet to the client\. In earlier MySQL versions, this wait event was labeled `writing to net`\.

**starting**  
This is the first stage at the beginning of statement execution\.

**statistics**  
The server is calculating statistics to develop a query execution plan\. If a thread is in this state for a long time, the server is probably disk\-bound while performing other work\.

**storing result in query cache**  
The server is storing the result of a query in the query cache\.

**system lock**  
The thread has called `mysql_lock_tables`, but the thread state hasn't been updated since the call\. This general state occurs for many reasons\.

**update**  
The thread is preparing to start updating the table\. 

**updating**  
The thread is searching for rows and is updating them\.

**user lock**  
The thread issued a `GET_LOCK` call\. The thread either requested an advisory lock and is waiting for it, or is planning to request it\.

**waiting for more updates**  
The primary node has finished its part of the replication\. The thread is waiting for more queries to run so that it can write to the binary log \(binlog\)\.

**waiting for schema metadata lock**  
This is a wait for a metadata lock\.

**waiting for stored function metadata lock**  
This is a wait for a metadata lock\.

**waiting for stored procedure metadata lock**  
This is a wait for a metadata lock\.

**waiting for table flush**  
The thread is executing `FLUSH TABLES` and is waiting for all threads to close their tables\. Or the thread received notification that the underlying structure for a table changed, so it must reopen the table to get the new structure\. To reopen the table, the thread must wait until all other threads have closed the table\. This notification takes place if another thread has used one of the following statements on the table: `FLUSH TABLES`, `ALTER TABLE`, `RENAME TABLE`, `REPAIR TABLE`, `ANALYZE TABLE`, or `OPTIMIZE TABLE`\.

**waiting for table level lock**  
One session is holding a lock on a table while another session tries to acquire the same lock on the same table\.

**waiting for table metadata lock**  
Aurora MySQL uses metadata locking to manage concurrent access to database objects and to ensure data consistency\. In this wait event, one session is holding a metadata lock on a table while another session tries to acquire the same lock on the same table\. When the Performance Schema is enabled, this thread state is reported as the wait event `synch/cond/sql/MDL_context::COND_wait_status`\.

**writing to net**  
The server is writing a packet to the network\. In later MySQL versions, this wait event is labeled `Sending to client`\. 

## Aurora MySQL isolation levels<a name="AuroraMySQL.Reference.IsolationLevels"></a>

 Following, you can learn how DB instances in an Aurora MySQL cluster implement the database property of isolation\. Doing so helps you understand how the Aurora MySQL default behavior balances between strict consistency and high performance\. You can also decide when to change the default settings based on the characteristics of your workload\. 

### Available isolation levels for writer instances<a name="AuroraMySQL.Reference.IsolationLevels.writer"></a>

 You can use the isolation levels `REPEATABLE READ`, `READ COMMITTED`, `READ UNCOMMITTED`, and `SERIALIZABLE` on the primary instance of an Aurora MySQL single\-master cluster\. You can use the isolation levels `REPEATABLE READ`, `READ COMMITTED`, and `READ UNCOMMITTED` on any DB instance in an Aurora MySQL multi\-master cluster\. These isolation levels work the same in Aurora MySQL as in RDS for MySQL\. 

### REPEATABLE READ isolation level for reader instances<a name="AuroraMySQL.Reference.IsolationLevels.reader"></a>

 By default, Aurora MySQL DB instances configured as read\-only Aurora Replicas always use the `REPEATABLE READ` isolation level\. These DB instances ignore any `SET TRANSACTION ISOLATION LEVEL` statements and continue using the `REPEATABLE READ` isolation level\. 

### READ COMMITTED isolation level for reader instances<a name="AuroraMySQL.Reference.IsolationLevels.relaxed"></a>

 If your application includes a write\-intensive workload on the primary instance and long\-running queries on the Aurora Replicas, you might experience substantial purge lag\. *Purge lag* happens when internal garbage collection is blocked by long\-running queries\. The symptom that you see is a high value for `history list length` in output from the `SHOW ENGINE INNODB STATUS` command\. You can monitor this value using the `RollbackSegmentHistoryListLength` metric in CloudWatch\. This condition can reduce the effectiveness of secondary indexes and lead to reduced overall query performance and wasted storage space\. 

 If you experience such issues, you can use an Aurora MySQL session\-level configuration setting, `aurora_read_replica_read_committed`, to use the `READ COMMITTED` isolation level on Aurora Replicas\. Using this setting can help reduce slowdowns and wasted space that can result from performing long\-running queries at the same time as transactions that modify your tables\. 

 We recommend making sure that you understand the specific Aurora MySQL behavior of the `READ COMMITTED` isolation before using this setting\. The Aurora Replica `READ COMMITTED` behavior complies with the ANSI SQL standard\. However, the isolation is less strict than typical MySQL `READ COMMITTED` behavior that you might be familiar with\. Thus, you might see different query results under `READ COMMITTED` on an Aurora MySQL read replica than for the same query under `READ COMMITTED` on the Aurora MySQL primary instance or on RDS for MySQL\. You might use the `aurora_read_replica_read_committed` setting for such use cases as a comprehensive report that scans a very large database\. You might avoid it for short queries with small result sets, where precision and repeatability are important\. 

 The `READ COMMITTED` isolation level isn't available for sessions within a secondary cluster in an Aurora global database that use the write forwarding feature\. For information about write forwarding, see [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)\. 

#### Enabling READ COMMITTED for readers<a name="AuroraMySQL.Reference.IsolationLevels.relaxed.enabling"></a>

 To enable the `READ COMMITTED` isolation level for Aurora Replicas, enable the `aurora_read_replica_read_committed` configuration setting\. Enable this setting at the session level while connected a specific Aurora Replica\. To do so, run the following SQL commands\. 

```
set session aurora_read_replica_read_committed = ON;
set session transaction isolation level read committed;
```

 You might enable this configuration setting temporarily to perform interactive ad hoc \(one\-time\) queries\. You might also want to run a reporting or data analysis application that benefits from the `READ COMMITTED` isolation level, while leaving the default unchanged for other applications\. 

 When the `aurora_read_replica_read_committed` setting is enabled, use the `SET TRANSACTION ISOLATION LEVEL` command to specify the isolation level for the appropriate transactions\. 

```
set transaction isolation level read committed;
```

#### Differences in READ COMMITTED behavior on Aurora replicas<a name="AuroraMySQL.Reference.IsolationLevels.relaxed.behavior"></a>

 The `aurora_read_replica_read_committed` setting makes the `READ COMMITTED` isolation level available for an Aurora Replica, with consistency behavior that is optimized for long\-running transactions\. The `READ COMMITTED` isolation level on Aurora Replicas has less strict isolation than on Aurora primary instances or multi\-master instances\. For that reason, enable this setting only on Aurora Replicas where you know that your queries can accept the possibility of certain types of inconsistent results\. 

 Your queries can experience certain kinds of read anomalies when the `aurora_read_replica_read_committed` setting is turned on\. Two kinds of anomalies are especially important to understand and handle in your application code\. A *non\-repeatable read* occurs when another transaction commits while your query is running\. A long\-running query can see different data at the start of the query than it sees at the end\. A *phantom read* occurs when other transactions cause existing rows to be reorganized while your query is running, and one or more rows are read twice by your query\. 

 Your queries might experience inconsistent row counts as a result of phantom reads\. Your queries might also return incomplete or inconsistent results due to non\-repeatable reads\. For example, suppose that a join operation refers to tables that are concurrently modified by SQL statements such as `INSERT` or `DELETE`\. In this case, the join query might read a row from one table but not the corresponding row from another table\. 

 The ANSI SQL standard allows both of these behaviors for the `READ COMMITTED` isolation level\. However, those behaviors are different than the typical MySQL implementation of `READ COMMITTED`\. Thus, before enabling the `aurora_read_replica_read_committed` setting, check any existing SQL code to verify if it operates as expected under the looser consistency model\. 

 Row counts and other results might not be strongly consistent under the `READ COMMITTED` isolation level while this setting is enabled\. Thus, you typically enable the setting only while running analytic queries that aggregate large amounts of data and don't require absolute precision\. If you don't have these kinds of long\-running queries alongside a write\-intensive workload, you probably don't need the `aurora_read_replica_read_committed` setting\. Without the combination of long\-running queries and a write\-intensive workload, you're unlikely to encounter issues with the length of the history list\. 

**Example Queries showing isolation behavior for READ COMMITTED on Aurora replicas**  
 The following example shows how `READ COMMITTED` queries on an Aurora Replica might return non\-repeatable results if transactions modify the associated tables at the same time\. The table `BIG_TABLE` contains 1 million rows before any queries start\. Other data manipulation language \(DML\) statements add, remove, or change rows while the are running\.   
 The queries on the Aurora primary instance under the `READ COMMITTED` isolation level produce predictable results\. However, the overhead of keeping the consistent read view for the lifetime of every long\-running query can lead to expensive garbage collection later\.   
 The queries on the Aurora Replica under the `READ COMMITTED` isolation level are optimized to minimize this garbage collection overhead\. The tradeoff is that the results might vary depending on whether the queries retrieve rows that are added, removed, or reorganized by transactions that commit while the query is running\. The queries are allowed to consider these rows but aren't required to\. For demonstration purposes, the queries check only the number of rows in the table by using the `COUNT(*)` function\.   


|  Time  |  DML statement on Aurora primary instance  |  Query on Aurora primary instance with READ COMMITTED  |  Query on Aurora replica with READ COMMITTED  | 
| --- | --- | --- | --- | 
|  T1  |  INSERT INTO big\_table SELECT \* FROM other\_table LIMIT 1000000; COMMIT;   |  |  | 
|  T2  |  |  Q1: SELECT COUNT\(\*\) FROM big\_table;  |  Q2: SELECT COUNT\(\*\) FROM big\_table;  | 
|  T3  |  INSERT INTO big\_table \(c1, c2\) VALUES \(1, 'one more row'\); COMMIT;   |  |  | 
|  T4  |  |  If Q1 finishes now, result is 1,000,000\.  |  If Q2 finishes now, result is 1,000,000 or 1,000,001\.  | 
|  T5  |  DELETE FROM big\_table LIMIT 2; COMMIT;   |  |  | 
|  T6  |  |  If Q1 finishes now, result is 1,000,000\.  |  If Q2 finishes now, result is 1,000,000 or 1,000,001 or 999,999 or 999,998\.  | 
|  T7  |  UPDATE big\_table SET c2 = CONCAT\(c2,c2,c2\); COMMIT;   |  |  | 
|  T8  |  |  If Q1 finishes now, result is 1,000,000\.  |  If Q2 finishes now, result is 1,000,000 or 1,000,001 or 999,999, or possibly some higher number\.  | 
|  T9  |  |  Q3: SELECT COUNT\(\*\) FROM big\_table;  |  Q4: SELECT COUNT\(\*\) FROM big\_table;  | 
|  T10  |  |  If Q3 finishes now, result is 999,999\.  |  If Q4 finishes now, result is 999,999\.  | 
|  T11  |  |  Q5: SELECT COUNT\(\*\) FROM parent\_table p JOIN child\_table c ON \(p\.id = c\.id\) WHERE p\.id = 1000;  |  Q6: SELECT COUNT\(\*\) FROM parent\_table p JOIN child\_table c ON \(p\.id = c\.id\) WHERE p\.id = 1000;  | 
|  T12  |   INSERT INTO parent\_table \(id, s\) VALUES \(1000, 'hello'\); INSERT INTO child\_table \(id, s\) VALUES \(1000, 'world'\); COMMIT;   |  |  | 
|  T13  |  |  If Q5 finishes now, result is 0\.  |  If Q6 finishes now, result is 0 or 1\.  | 
 If the queries finish quickly, before any other transactions perform DML statements and commit, the results are predictable and the same between the primary instance and the Aurora Replica\.   
 The results for Q1 are highly predictable, because `READ COMMITTED` on the primary instance uses a strong consistency model similar to the `REPEATABLE READ` isolation level\.   
 The results for Q2 might vary depending on what transactions commit while that query is running\. For example, suppose that other transactions perform DML statements and commit while the queries are running\. In this case, the query on the Aurora Replica with the `READ COMMITTED` isolation level might or might not take the changes into account\. The row counts are not predictable in the same way as under the `REPEATABLE READ` isolation level\. They also aren't as predictable as queries running under the `READ COMMITTED` isolation level on the primary instance, or on an RDS for MySQL instance\.   
 The `UPDATE` statement at T7 doesn't actually change the number of rows in the table\. However, by changing the length of a variable\-length column, this statement can cause rows to be reorganized internally\. A long\-running `READ COMMITTED` transaction might see the old version of a row, and later within the same query see the new version of the same row\. The query can also skip both the old and new versions of the row\. Thus, the row count might be different than expected\.   
 The results of Q5 and Q6 might be identical or slightly different\. Query Q6 on the Aurora Replica under `READ COMMITTED` is able to see, but is not required to see, the new rows that are committed while the query is running\. It might also see the row from one table but not from the other table\. If the join query doesn't find a matching row in both tables, it returns a count of zero\. If the query does find both the new rows in `PARENT_TABLE` and `CHILD_TABLE`, the query returns a count of one\. In a long\-running query, the lookups from the joined tables might happen at widely separated times\.   
 These differences in behavior depend on the timing of when transactions are committed and when the queries process the underlying table rows\. Thus, you're most likely to see such differences in report queries that take minutes or hours and that run on Aurora clusters processing OLTP transactions at the same time\. These are the kinds of mixed workloads that benefit the most from the `READ COMMITTED` isolation level on Aurora Replicas\. 

## Aurora MySQL hints<a name="AuroraMySQL.Reference.Hints"></a><a name="hints"></a>

 You can use SQL hints with Aurora MySQL queries to fine\-tune performance\. You can also use hints to prevent execution plans for important queries to change based on unpredictable conditions\. 

**Tip**  
 To verify the effect that a hint has on a query, examine the query plan produced by the `EXPLAIN` statement\. Compare the query plans with and without the hint\. 

 In Aurora MySQL version 3, you can use all the hints that are available in community MySQL 8\.0\. For details about these hints, see [Optimizer Hints](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) in the *MySQL Reference Manual*\. 

 The following hints are available in Aurora MySQL 2\.08 and higher\. These hints apply to queries that use the hash join feature in Aurora MySQL version 2, especially queries that use the parallel query optimization\. 

**HASH\_JOIN, NO\_HASH\_JOIN**  
 Turns on or off the ability of the optimizer to choose whether to use the hash join optimization method for a query\. `HASH_JOIN` enables the optimizer to use hash join if that mechanism is more efficient\. `NO_HASH_JOIN` prevents the optimizer from using hash join for the query\. This hint is available in Aurora MySQL 2\.08 and higher minor versions\. It has no effect in Aurora MySQL version 3\.   
 The following examples show how to use this hint\.   

```
EXPLAIN SELECT/*+ HASH_JOIN(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;

EXPLAIN SELECT /*+ NO_HASH_JOIN(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;
```

**HASH\_JOIN\_PROBING, NO\_HASH\_JOIN\_PROBING**  
 In a hash join query, specifies whether or not to use the specified table for the probe side of the join\. The query tests whether column values from the build table exist in the probe table, instead of reading the entire contents of the probe table\. You can use `HASH_JOIN_PROBING` and `HASH_JOIN_BUILDING` to specify how hash join queries are processed without reordering the tables within the query text\. This hint is available in Aurora MySQL 2\.08 and higher minor versions\. It has no effect in Aurora MySQL version 3\.   
 The following examples show how to use this hint\. Specifying the `HASH_JOIN_PROBING` hint for the table `T2` has the same effect as specifying `NO_HASH_JOIN_PROBING` for the table `T1`\.   

```
EXPLAIN SELECT /*+ HASH_JOIN(t2) HASH_JOIN_PROBING(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;

EXPLAIN SELECT /*+ HASH_JOIN(t2) NO_HASH_JOIN_PROBING(t1) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;
```

**HASH\_JOIN\_BUILDING, NO\_HASH\_JOIN\_BUILDING**  
 In a hash join query, specifies whether or not to use the specified table for the build side of the join\. The query processes all the rows from this table to build the list of column values to cross\-reference with the other table\. You can use `HASH_JOIN_PROBING` and `HASH_JOIN_BUILDING` to specify how hash join queries are processed without reordering the tables within the query text\. This hint is available in Aurora MySQL 2\.08 and higher minor versions\. It has no effect in Aurora MySQL version 3\.   
 The following examples show how to use this hint\. Specifying the `HASH_JOIN_BUILDING` hint for the table `T2` has the same effect as specifying `NO_HASH_JOIN_BUILDING` for the table `T1`\.   

```
EXPLAIN SELECT /*+ HASH_JOIN(t2) HASH_JOIN_BUILDING(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;

EXPLAIN SELECT /*+ HASH_JOIN(t2) NO_HASH_JOIN_BUILDING(t1) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;
```

**JOIN\_FIXED\_ORDER**  
 Specifies that tables in the query are joined based on the order they are listed in the query\. It is especially useful with queries involving three or more tables\. It is intended as a replacement for the MySQL `STRAIGHT_JOIN` hint\. Equivalent to the MySQL [JOIN\_FIXED\_ORDER](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL 2\.08 and higher\.   
 The following examples show how to use this hint\.   

```
EXPLAIN SELECT /*+ JOIN_FIXED_ORDER */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

**JOIN\_ORDER**  
 Specifies the join order for the tables in the query\. It is especially useful with queries involving three or more tables\. Equivalent to the MySQL [JOIN\_ORDER](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL 2\.08 and higher\.   
 The following examples show how to use this hint\.   

```
EXPLAIN SELECT /*+ JOIN_ORDER (t4, t2, t1, t3) */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

**JOIN\_PREFIX**  
 Specifies the tables to put first in the join order\. It is especially useful with queries involving three or more tables\. Equivalent to the MySQL [JOIN\_PREFIX](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL 2\.08 and higher\.   
 The following examples show how to use this hint\.   

```
EXPLAIN SELECT /*+ JOIN_ORDER (t4, t2) */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

**JOIN\_SUFFIX**  
 Specifies the tables to put last in the join order\. It is especially useful with queries involving three or more tables\. Equivalent to the MySQL [JOIN\_SUFFIX](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL 2\.08 and higher\.   
 The following examples show how to use this hint\.   

```
EXPLAIN SELECT /*+ JOIN_ORDER (t1, t3) */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

 For information about using hash join queries, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 

## Aurora MySQL stored procedures<a name="AuroraMySQL.Reference.StoredProcs"></a>

 You can call the following stored procedures while connected to the primary instance in an Aurora MySQL cluster\. These procedures control how transactions are replicated from an external database into Aurora MySQL, or from Aurora MySQL to an external database\. To learn how to use replication based on global transaction identifiers \(GTIDs\) with Aurora MySQL, see [Using GTID\-based replication for Amazon Aurora MySQL](mysql-replication-gtid.md)\. 

**Topics**
+ [mysql\.rds\_assign\_gtids\_to\_anonymous\_transactions \(Aurora MySQL version 3 and higher\)](#mysql_assign_gtids_to_anonymous_transactions)
+ [mysql\.rds\_set\_master\_auto\_position \(Aurora MySQL version 1 and 2\)](#mysql_rds_set_master_auto_position)
+ [mysql\.rds\_set\_source\_auto\_position \(Aurora MySQL version 3 and higher\)](#mysql_rds_set_source_auto_position)
+ [mysql\.rds\_set\_external\_master\_with\_auto\_position \(Aurora MySQL version 1 and 2\)](#mysql_rds_set_external_master_with_auto_position)
+ [mysql\.rds\_set\_external\_source\_with\_auto\_position \(Aurora MySQL version 3 and higher\)](#mysql_rds_set_external_source_with_auto_position)
+ [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid)

### mysql\.rds\_assign\_gtids\_to\_anonymous\_transactions \(Aurora MySQL version 3 and higher\)<a name="mysql_assign_gtids_to_anonymous_transactions"></a>



#### Syntax<a name="mysql_assign_gtids_to_anonymous_transactions-syntax"></a>

```
CALL mysql.rds_assign_gtids_to_anonymous_transactions(gtid_option);
```

#### Parameters<a name="mysql_assign_gtids_to_anonymous_transactions-parameters"></a>

 *gtid\_option*  
 String value\. The allowed values are `OFF`, `LOCAL`, or a specified UUID\. 

#### Usage notes<a name="mysql_assign_gtids_to_anonymous_transactions-usage-notes"></a>

 This procedure has the same effect as issuing the statement `CHANGE REPLICATION SOURCE TO ASSIGN_GTIDS_TO_ANONYMOUS_TRANSACTIONS = gtid_option` in community MySQL\. 

 GTID must be turned to `ON` for *gtid\_option* to be set to `LOCAL` or a specific UUID\. 

 The default is `OFF`, meaning that the feature is not used\. 

 `LOCAL` assigns a GTID including the replica's own UUID \(the `server_uuid` setting\)\. 

 Passing a parameter that is a UUID assigns a GTID that includes the specified UUID, such as the `server_uuid` setting for the replication source server\. 

#### Examples<a name="mysql_assign_gtids_to_anonymous_transactions-examples"></a>

 To turn off this feature: 

```
mysql> call mysql.rds_assign_gtids_to_anonymous_transactions('OFF');
+-------------------------------------------------------------+
| Message  |
+-------------------------------------------------------------+
| ASSIGN_GTIDS_TO_ANONYMOUS_TRANSACTIONS has been set to: OFF |
+-------------------------------------------------------------+
1 row in set (0.07 sec)
```

 To use the replica's own UUID: 

```
mysql> call mysql.rds_assign_gtids_to_anonymous_transactions('LOCAL');
+---------------------------------------------------------------+
| Message  |
+---------------------------------------------------------------+
| ASSIGN_GTIDS_TO_ANONYMOUS_TRANSACTIONS has been set to: LOCAL |
+---------------------------------------------------------------+
1 row in set (0.07 sec)
```

 To use a specified UUID: 

```
mysql> call mysql.rds_assign_gtids_to_anonymous_transactions('317a4760-f3dd-3b74-8e45-0615ed29de0e');
+----------------------------------------------------------------------------------------------+
| Message |
+----------------------------------------------------------------------------------------------+
| ASSIGN_GTIDS_TO_ANONYMOUS_TRANSACTIONS has been set to: 317a4760-f3dd-3b74-8e45-0615ed29de0e |
+----------------------------------------------------------------------------------------------+
1 row in set (0.07 sec)
```

### mysql\.rds\_set\_master\_auto\_position \(Aurora MySQL version 1 and 2\)<a name="mysql_rds_set_master_auto_position"></a>

 Sets the replication mode to be based on either binary log file positions or on global transaction identifiers \(GTIDs\)\. 

#### Syntax<a name="mysql_rds_set_master_auto_position-syntax"></a>

```
CALL mysql.rds_set_master_auto_position (auto_position_mode);
```

#### Parameters<a name="mysql_rds_set_master_auto_position-parameters"></a>

*auto\_position\_mode*  
 A value that indicates whether to use log file position replication or GTID\-based replication:   
+  `0` – Use the replication method based on binary log file position\. The default is `0`\. 
+  `1` – Use the GTID\-based replication method\. 

#### Usage notes<a name="mysql_rds_set_master_auto_position-usage-notes"></a>

 For an Aurora MySQL DB cluster, you call this stored procedure while connected to the primary instance\. 

 The master user must run the `mysql.rds_set_master_auto_position` procedure\. 

 For Aurora, this procedure is supported for Aurora MySQL version 2\.04 and later MySQL 5\.7–compatible versions\. GTID\-based replication isn't supported for Aurora MySQL 1\.1 or 1\.0\. 

### mysql\.rds\_set\_source\_auto\_position \(Aurora MySQL version 3 and higher\)<a name="mysql_rds_set_source_auto_position"></a>

 Sets the replication mode to be based on either binary log file positions or on global transaction identifiers \(GTIDs\)\. 

#### Syntax<a name="mysql_rds_set_source_auto_position-syntax"></a>

```
CALL mysql.rds_set_source_auto_position (auto_position_mode);
```

#### Parameters<a name="mysql_rds_set_source_auto_position-parameters"></a>

*auto\_position\_mode*  
 A value that indicates whether to use log file position replication or GTID\-based replication:   
+  `0` – Use the replication method based on binary log file position\. The default is `0`\. 
+  `1` – Use the GTID\-based replication method\. 

#### Usage notes<a name="mysql_rds_set_source_auto_position-usage-notes"></a>

 For an Aurora MySQL DB cluster, you call this stored procedure while connected to the primary instance\. 

 The administrative user must run the `mysql.rds_set_source_auto_position` procedure\. 

### mysql\.rds\_set\_external\_master\_with\_auto\_position \(Aurora MySQL version 1 and 2\)<a name="mysql_rds_set_external_master_with_auto_position"></a>

 Configures an Aurora MySQL primary instance to accept incoming replication from an external MySQL instance\. This procedure also configures replication based on global transaction identifiers \(GTIDs\)\. 

 This procedure is available for both RDS for MySQL and Aurora MySQL\. It works differently depending on the context\. When used with Aurora MySQL, this procedure doesn't configure delayed replication\. This limitation is because RDS for MySQL supports delayed replication but Aurora MySQL doesn't\. 

#### Syntax<a name="mysql_rds_set_external_master_with_auto_position-syntax"></a>

```
CALL mysql.rds_set_external_master_with_auto_position (
  host_name
  , host_port
  , replication_user_name
  , replication_user_password
  , ssl_encryption
);
```

#### Parameters<a name="mysql_rds_set_external_master_with_auto_position-parameters"></a>

*host\_name*  
 The host name or IP address of the MySQL instance running external to Aurora to become the replication master\. 

*host\_port*  
 The port used by the MySQL instance running external to Aurora to be configured as the replication master\. If your network configuration includes Secure Shell \(SSH\) port replication that converts the port number, specify the port number that is exposed by SSH\. 

*replication\_user\_name*  
 The ID of a user with `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the MySQL instance running external to Aurora\. We recommend that you provide an account that is used solely for replication with the external instance\. 

*replication\_user\_password*  
 The password of the user ID specified in `replication_user_name`\. 

*ssl\_encryption*  
 This option is not currently implemented\.  The default is 0\. 

#### Usage notes<a name="mysql_rds_set_external_master_with_auto_position-usage-notes"></a>

 For an Aurora MySQL DB cluster, you call this stored procedure while connected to the primary instance\. 

 The master user must run the `mysql.rds_set_external_master_with_auto_position` procedure\. The master user runs this procedure on the primary instance of an Aurora MySQL DB cluster that acts as a replication target\. This can be the replication target of an external MySQL DB instance or an Aurora MySQL DB cluster\. 

 For Aurora, this procedure is supported for Aurora MySQL version 2\.04 and later MySQL 5\.7\-compatible versions\. GTID\-based replication isn't supported for Aurora MySQL 1\.1 or 1\.0\. For Aurora MySQL version 3, use the procedure `mysql.rds_set_external_source_with_auto_position` instead\. 

 Before you run `mysql.rds_set_external_master_with_auto_position`, configure the external MySQL DB instance to be a replication master\. To connect to the external MySQL instance, specify values for `replication_user_name` and `replication_user_password`\. These values must indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external MySQL instance\. 

**To configure an external MySQL instance as a replication master**

1.  Using the MySQL client of your choice, connect to the external MySQL instance and create a user account to be used for replication\. The following is an example\. 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'SomePassW0rd'
   ```

1.  On the external MySQL instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the `'repl_user'` user for your domain\. 

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com'
   IDENTIFIED BY 'SomePassW0rd'
   ```

 When you call `mysql.rds_set_external_master_with_auto_position`, Amazon RDS records certain information\. This information is the time, the user, and an action of `"set master"` in the `mysql.rds_history` and `mysql.rds_replication_status` tables\. 

 To skip a specific GTID\-based transaction that is known to cause a problem, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid) stored procedure\. For more information about working with GTID\-based replication, see [Using GTID\-based replication for Amazon Aurora MySQL](mysql-replication-gtid.md)\. 

#### Examples<a name="mysql_rds_set_external_master_with_auto_position-examples"></a>

 When run on an Aurora primary instance, the following example configures the Aurora cluster to act as a read replica of an instance of MySQL running external to Aurora\. 

```
call mysql.rds_set_external_master_with_auto_position(
  'Externaldb.some.com',
  3306,
  'repl_user'@'mydomain.com',
  'SomePassW0rd');
```

### mysql\.rds\_set\_external\_source\_with\_auto\_position \(Aurora MySQL version 3 and higher\)<a name="mysql_rds_set_external_source_with_auto_position"></a>

 Configures an Aurora MySQL primary instance to accept incoming replication from an external MySQL instance\. This procedure also configures replication based on global transaction identifiers \(GTIDs\)\. 

 This procedure is available for both RDS for MySQL and Aurora MySQL\. It works differently depending on the context\. When used with Aurora MySQL, this procedure doesn't configure delayed replication\. This limitation is because RDS for MySQL supports delayed replication but Aurora MySQL doesn't\. 

#### Syntax<a name="mysql_rds_set_external_source_with_auto_position-syntax"></a>

```
CALL mysql.rds_set_external_source_with_auto_position (
  host_name
  , host_port
  , replication_user_name
  , replication_user_password
  , ssl_encryption
);
```

#### Parameters<a name="mysql_rds_set_external_source_with_auto_position-parameters"></a>

*host\_name*  
 The host name or IP address of the MySQL instance running external to Aurora to become the replication source\. 

*host\_port*  
 The port used by the MySQL instance running external to Aurora to be configured as the replication source\. If your network configuration includes Secure Shell \(SSH\) port replication that converts the port number, specify the port number that is exposed by SSH\. 

*replication\_user\_name*  
 The ID of a user with `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the MySQL instance running external to Aurora\. We recommend that you provide an account that is used solely for replication with the external instance\. 

*replication\_user\_password*  
 The password of the user ID specified in `replication_user_name`\. 

*ssl\_encryption*  
 This option is not currently implemented\.  The default is 0\. 

#### Usage notes<a name="mysql_rds_set_external_source_with_auto_position-usage-notes"></a>

 For an Aurora MySQL DB cluster, you call this stored procedure while connected to the primary instance\. 

 The administrative user must run the `mysql.rds_set_external_source_with_auto_position` procedure\. The administrative user runs this procedure on the primary instance of an Aurora MySQL DB cluster that acts as a replication target\. This can be the replication target of an external MySQL DB instance or an Aurora MySQL DB cluster\. 

 For Aurora, this procedure is supported for Aurora MySQL version 2\.04 and later MySQL 5\.7\-compatible versions\. It's also supported for Aurora MySQL version 3\. GTID\-based replication isn't supported for Aurora MySQL 1\.1 or 1\.0\. 

 Before you run `mysql.rds_set_external_source_with_auto_position`, configure the external MySQL DB instance to be a replication source\. To connect to the external MySQL instance, specify values for `replication_user_name` and `replication_user_password`\. These values must indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external MySQL instance\. 

**To configure an external MySQL instance as a replication source**

1.  Using the MySQL client of your choice, connect to the external MySQL instance and create a user account to be used for replication\. The following is an example\. 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'SomePassW0rd'
   ```

1.  On the external MySQL instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the `'repl_user'` user for your domain\. 

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com'
   IDENTIFIED BY 'SomePassW0rd'
   ```

 When you call `mysql.rds_set_external_source_with_auto_position`, Amazon RDS records certain information\. This information is the time, the user, and an action of `"set master"` in the `mysql.rds_history` and `mysql.rds_replication_status` tables\. 

 To skip a specific GTID\-based transaction that is known to cause a problem, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid) stored procedure\. For more information about working with GTID\-based replication, see [Using GTID\-based replication for Amazon Aurora MySQL](mysql-replication-gtid.md)\. 

#### Examples<a name="mysql_rds_set_external_source_with_auto_position-examples"></a>

 When run on an Aurora primary instance, the following example configures the Aurora cluster to act as a read replica of an instance of MySQL running external to Aurora\. 

```
call mysql.rds_set_external_source_with_auto_position(
  'Externaldb.some.com',
  3306,
  'repl_user'@'mydomain.com',
  'SomePassW0rd');
```

### mysql\.rds\_skip\_transaction\_with\_gtid<a name="mysql_rds_skip_transaction_with_gtid"></a>

 Skips replication of a transaction with the specified global transaction identifier \(GTID\) on an Aurora primary instance\. 

 You can use this procedure for disaster recovery when a specific GTID transaction is known to cause a problem\. Use this stored procedure to skip the problematic transaction\. Examples of problematic transactions include transactions that disable replication, delete important data, or cause the DB instance to become unavailable\. 

#### Syntax<a name="mysql_rds_skip_transaction_with_gtid-syntax"></a>

```
CALL mysql.rds_skip_transaction_with_gtid (gtid_to_skip);
```

#### Parameters<a name="mysql_rds_skip_transaction_with_gtid-parameters"></a>

*gtid\_to\_skip*  
 The GTID of the replication transaction to skip\. 

#### Usage notes<a name="mysql_rds_skip_transaction_with_gtid-usage-notes"></a>

 For an Aurora MySQL DB cluster, you call this stored procedure while connected to the primary instance\. 

 The master user must run the `mysql.rds_skip_transaction_with_gtid` procedure\. 

 For Aurora, this procedure is supported for Aurora MySQL version 2\.04 and later MySQL 5\.7\-compatible versions\. It's also supported for Aurora MySQL version 3\. GTID\-based replication isn't supported for Aurora MySQL 1\.1 or 1\.0\. 

## Aurora MySQL–specific information\_schema tables<a name="AuroraMySQL.Reference.ISTables"></a>

Aurora MySQL has certain `information_schema` tables that are specific to Aurora\.

### information\_schema\.replica\_host\_status<a name="AuroraMySQL.Reference.ISTables.replica_host_status"></a>

The `information_schema.replica_host_status` table contains replication information\. The columns that you can use are shown in the following table\. The remaining columns are for Aurora internal use only\.


| Column | Data type | Description | 
| --- | --- | --- | 
| CPU | double | The CPU percentage usage of the replica host\. | 
| IS\_CURRENT | tinyint | Whether the replica is current\. | 
| LAST\_UPDATE\_TIMESTAMP | datetime\(6\) | The time the last update occurred\. Used to determine whether a record is stale\. | 
| REPLICA\_LAG\_IN\_MILLISECONDS | double | The replica lag in milliseconds\. | 
| SERVER\_ID | varchar\(100\) | The ID of the database server\. | 
| SESSION\_ID | varchar\(100\) | The ID of the database session\. Used to determine whether a DB instance is a writer or reader instance\. | 

**Note**  
When a replica instance falls behind, the information queried from its `information_schema.replica_host_status` table might be outdated\. In this situation, we recommend that you query from the writer instance instead\.  
While the `mysql.ro_replica_status` table has similar information, we don't recommend that you use it\.