# Aurora MySQL configuration parameters<a name="AuroraMySQL.Reference.ParameterGroups"></a><a name="param_groups"></a>

You manage your Amazon Aurora MySQL DB cluster in the same way that you manage other Amazon RDS DB instances, by using parameters in a DB parameter group\. Amazon Aurora differs from other DB engines in that you have a DB cluster that contains multiple DB instances\. As a result, some of the parameters that you use to manage your Aurora MySQL DB cluster apply to the entire cluster\. Other parameters apply only to a particular DB instance in the DB cluster\.

To manage cluster\-level parameters, use DB cluster parameter groups\. To manage instance\-level parameters, use DB parameter groups\. Each DB instance in an Aurora MySQL DB cluster is compatible with the MySQL database engine\. However, you apply some of the MySQL database engine parameters at the cluster level, and you manage these parameters using DB cluster parameter groups\. You can't find cluster\-level parameters in the DB parameter group for an instance in an Aurora DB cluster\. A list of cluster\-level parameters appears later in this topic\.

You can manage both cluster\-level and instance\-level parameters using the AWS Management Console, the AWS CLI, or the Amazon RDS API\. You use separate commands for managing cluster\-level parameters and instance\-level parameters\. For example, you can use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) CLI command to manage cluster\-level parameters in a DB cluster parameter group\. You can use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) CLI command to manage instance\-level parameters in a DB parameter group for a DB instance in a DB cluster\.

You can view both cluster\-level and instance\-level parameters in the console, or by using the CLI or RDS API\. For example, you can use the [describe\-db\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) AWS CLI command to view cluster\-level parameters in a DB cluster parameter group\. You can use the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) CLI command to view instance\-level parameters in a DB parameter group for a DB instance in a DB cluster\.

**Note**  
Each [default parameter group](USER_WorkingWithParamGroups.md) contains the default values for all parameters in the parameter group\. If the parameter has "engine default" for this value, see the version\-specific MySQL or PostgreSQL documentation for the actual default value\.

For more information about DB parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. For rules and restrictions for Aurora Serverless v1 clusters, see [Parameter groups for Aurora Serverless v1](aurora-serverless-v1.how-it-works.md#aurora-serverless.parameter-groups)\.

**Topics**
+ [Cluster\-level parameters](#AuroraMySQL.Reference.Parameters.Cluster)
+ [Instance\-level parameters](#AuroraMySQL.Reference.Parameters.Instance)
+ [MySQL parameters that don't apply to Aurora MySQL](#AuroraMySQL.Reference.Parameters.Inapplicable)
+ [MySQL status variables that don't apply to Aurora MySQL](#AuroraMySQL.Reference.StatusVars.Inapplicable)

## Cluster\-level parameters<a name="AuroraMySQL.Reference.Parameters.Cluster"></a><a name="cluster_params"></a><a name="params"></a>

The following table shows all of the parameters that apply to the entire Aurora MySQL DB cluster\.


| Parameter name | Modifiable | Notes | 
| --- | --- | --- | 
|  `aurora_binlog_read_buffer_size`  |  Yes  |  Only affects clusters that use binary log \(binlog\) replication\. For information about binlog replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\. Removed from Aurora MySQL version 3\.  | 
|  `aurora_binlog_replication_max_yield_seconds`  |  Yes  |  Only affects clusters that use binary log \(binlog\) replication\. For information about binlog replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\.  | 
|  `aurora_binlog_use_large_read_buffer`  |  Yes  |  Only affects clusters that use binary log \(binlog\) replication\. For information about binlog replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\. Removed from Aurora MySQL version 3\.  | 
|  `aurora_disable_hash_join`   |  Yes  |  Set this parameter to `ON` to turn off hash join optimization in Aurora MySQL version 2\.09 or higher\. It isn't supported for version 3\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.  | 
|   `aurora_enable_replica_log_compression`   |   Yes   |   For more information, see [Performance considerations for Amazon Aurora MySQL replication](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Performance)\. Doesn't apply to clusters that are part of an Aurora global database\. Removed from Aurora MySQL version 3\.   | 
|   `aurora_enable_repl_bin_log_filtering`   |   Yes   |   For more information, see [Performance considerations for Amazon Aurora MySQL replication](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Performance)\. Doesn't apply to clusters that are part of an Aurora global database\. Removed from Aurora MySQL version 3\.   | 
|  `aurora_enable_staggered_replica_restart`  |  Yes  | This setting is available in Aurora MySQL version 3, but it isn't used\. | 
|   `aurora_enable_zdr`   |   Yes   |   This setting is turned on by default in Aurora MySQL 2\.10 and higher\. For more information, see [Zero\-downtime restart \(ZDR\) for Amazon Aurora MySQL](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Availability)\.   | 
|   `aurora_load_from_s3_role`   |   Yes   |   For more information, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\. Currently not available in Aurora MySQL version 3\. Use `aws_default_s3_role`\.  | 
|  `aurora_mask_password_hashes_type`  |  Yes  |  This setting is turned on by default in Aurora MySQL 2\.11 and higher\. Use this setting to mask Aurora MySQL password hashes in the general, slow query, and audit logs\. The allowed values are `0` and `1` \(default\)\. When set to `1`, passwords are logged as `<secret>`\. When set to `0`, passwords are logged as hash \(`#`\) values\.  | 
|   `aurora_select_into_s3_role`   |   Yes   |   For more information, see [Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\. Currently not available in Aurora MySQL version 3\. Use `aws_default_s3_role`\.  | 
|   `auto_increment_increment`   |   Yes   |    | 
|   `auto_increment_offset`   |   Yes   |    | 
|   `aws_default_lambda_role`   |   Yes   |   For more information, see [Invoking a Lambda function from an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Lambda.md)\.   | 
|  `aws_default_s3_role`  | Yes |  Used when invoking the `LOAD DATA FROM S3`, `LOAD XML FROM S3`, or `SELECT INTO OUTFILE S3` statement from your DB cluster\. In Aurora MySQL version 2, the IAM role specified in this parameter is used if an IAM role isn't specified for `aurora_load_from_s3_role` or `aurora_select_into_s3_role` for the appropriate statement\. In Aurora MySQL version 3, the IAM role specified for this parameter is always used\. For more information, see [Associating an IAM role with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.  | 
|   `binlog_checksum`   |   Yes   |  The AWS CLI and RDS API report a value of `None` if this parameter isn't set\. In that case, Aurora MySQL uses the engine default value, which is `CRC32`\. This is different from the explicit setting of `NONE`, which turns off the checksum\.  | 
|   `binlog-do-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_format`   |   Yes   |   For more information, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\.   | 
|   `binlog_group_commit_sync_delay`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_group_commit_sync_no_delay_count`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog-ignore-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_row_image`   |   No   |    | 
|   `binlog_row_metadata`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_row_value_options`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_rows_query_log_events`   |   Yes   |    | 
|   `binlog_transaction_compression`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_transaction_compression_level_zstd`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_transaction_dependency_history_size`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_transaction_dependency_tracking`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
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
|   `enforce_gtid_consistency`   |   Sometimes   |  Modifiable in Aurora MySQL version 2 and higher\.  | 
|  `event_scheduler`  |  Yes  |  Indicates the status of the Event Scheduler\. Modifiable only at the cluster level in Aurora MySQL version 3\.  | 
|   `gtid-mode`   |   Sometimes   |  Modifiable in Aurora MySQL version 2 and higher\.  | 
|   `init_connect`   |   Yes   |  The command to be run by the server for each client that connects\. Use double quotes \("\) for settings to avoid connection failures, for example: <pre>SET optimizer_switch="hash_join=off"</pre> In Aurora MySQL version 3, this parameter doesn't apply for users who have the `CONNECTION_ADMIN` privilege\. This includes the Aurora master user\. For more information, see [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\.  | 
|   `innodb_autoinc_lock_mode`   |   Yes   |    | 
|   `innodb_checksums`   |   No   | Removed from Aurora MySQL version 3\.  | 
|   `innodb_cmp_per_index_enabled`   |   Yes   |    | 
|   `innodb_commit_concurrency`   |   Yes   |    | 
|   `innodb_data_home_dir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `innodb_deadlock_detect`   |  Yes  |  This option is used to disable deadlock detection on Aurora MySQL version 3\. On high\-concurrency systems, deadlock detection can cause a slowdown when numerous threads wait for the same lock\. Consult the MySQL documentation for more information on this parameter\.  | 
|  `innodb_default_row_format`  | Yes |  This parameter defines the default row format for InnoDB tables \(including user\-created InnoDB temporary tables\)\. It applies to Aurora MySQL versions 2 and 3\. Its value can be `DYNAMIC`, `COMPACT`, or `REDUNDANT.`  | 
|   `innodb_file_per_table`   |   Yes   |  This parameter affects how table storage is organized\. For more information, see [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling)\.  | 
|  `innodb_flush_log_at_trx_commit`  |  Aurora MySQL version 2: Yes Aurora MySQL version 3: No  |  For Aurora MySQL version 2, we highly recommend that you use the default value of `1`\. For Aurora MySQL version 3, Aurora always uses the default value of `1`\. For more information, see [Configuring how frequently the log buffer is flushed](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.Flush)\.  | 
|   `innodb_ft_max_token_size`   |   Yes   |    | 
|   `innodb_ft_min_token_size`   |   Yes   |    | 
|   `innodb_ft_num_word_optimize`   |   Yes   |    | 
|   `innodb_ft_sort_pll_degree`   |   Yes   |    | 
|   `innodb_online_alter_log_max_size`   |   Yes   |    | 
|   `innodb_optimize_fulltext_only`   |   Yes   |    | 
|   `innodb_page_size`   |   No   |    | 
|   `innodb_print_all_deadlocks`   |   Yes   |  When turned on, records information about all InnoDB deadlocks in the Aurora MySQL error log\. For more information, see [](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.deadlocks)\.  | 
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
|   `innodb_undo_logs`   |   Yes   |  Removed from Aurora MySQL version 3\.  | 
|   `innodb_undo_tablespaces`   |   No   |  Removed from Aurora MySQL version 3\.  | 
|  `internal_tmp_disk_storage_engine`  | Yes |  This parameter applies to Aurora MySQL version 2\. Allowed values are `INNODB` and `MYISAM`\.  | 
|   `internal_tmp_mem_storage_engine`   |   Yes   |  This parameter applies to Aurora MySQL version 3\.  | 
|  `key_buffer_size`  |   Yes   |  Key cache for MyISAM tables\. For more information, see [keycache\->cache\_lock mutex](AuroraMySQL.Reference.Waitevents.md#key-cache.cache-lock)\.  | 
|   `lc_time_names`   |   Yes   |    | 
|  `lower_case_table_names`  |  Yes \(Aurora MySQL version 2\) Only at cluster creation time \(Aurora MySQL version 3\)  |  In Aurora MySQL version 2\.10 and higher 2\.x versions, make sure to reboot all reader instances after changing this setting and rebooting the writer instance\. For details, see [Rebooting an Aurora MySQL cluster \(version 2\.10 and higher\)](USER_RebootCluster.md#aurora-mysql-survivable-replicas)\. In Aurora MySQL version 3, the value of this parameter is set permanently at the time the cluster is created\. If you use a nondefault value for this option, set up your Aurora MySQL version 3 custom parameter group before upgrading, and specify the parameter group during the snapshot restore operation that creates the version 3 cluster\. With an Aurora global database based on Aurora MySQL, you can't perform an in\-place upgrade from Aurora MySQL version 2 to version 3 if the `lower_case_table_names` parameter is turned on\. For more information on the methods that you can use, see [Major version upgrades](aurora-global-database-upgrade.md#aurora-global-database-upgrade.major)\.  | 
|   `master-info-repository`   |   Yes   |  Removed from Aurora MySQL version 3\.  | 
|   `master_verify_checksum`   |   Yes   |  Aurora MySQL version 2\. Use `source_verify_checksum` in Aurora MySQL version 3\.  | 
|   `partial_revokes`   |   No   |  This parameter applies to Aurora MySQL version 3\.  | 
|   `read_only`   |   Yes   |  When this parameter is turned on, the server permits no updates except from those performed by replica threads\. In Aurora MySQL version 3, this parameter doesn't apply for users who have the `CONNECTION_ADMIN` privilege\. This includes the Aurora master user\. For more information, see [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\.  | 
|   `relay-log-space-limit`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replica_preserve_commit_order`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replica_transaction_retries`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replicate-do-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replicate-do-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replicate-ignore-db`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replicate-ignore-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replicate-wild-do-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `replicate-wild-ignore-table`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `require_secure_transport`   |   Yes   |   This parameter applies to Aurora MySQL version 2 and 3\. For more information, see [Using SSL/TLS with Aurora MySQL DB clusters](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL)\.   | 
|   `rpl_read_size`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `server_audit_events`   |   Yes   |    | 
|   `server_audit_excl_users`   |   Yes   |    | 
|   `server_audit_incl_users`   |   Yes   |    | 
|   `server_audit_logging`   |   Yes   |   For instructions on uploading the logs to Amazon CloudWatch Logs, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.   | 
|   `server_id`   |   No   |    | 
|   `skip-character-set-client-handshake`   |   Yes   |    | 
|   `skip_name_resolve`   |   No   |    | 
|   `slave-skip-errors`   |   Yes   |   Only applies to Aurora MySQL version 2 clusters, with MySQL 5\.7 compatibility\.   | 
|   `source_verify_checksum`   |   Yes   |   Aurora MySQL version 3   | 
|   `sync_frm`   |   Yes   |  Removed from Aurora MySQL version 3\.   | 
|   `time_zone`   |   Yes   |    | 
|   `tls_version`   |   Yes   |   For more information, see [TLS versions for Aurora MySQL](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL.TLS_Version)\.   | 

## Instance\-level parameters<a name="AuroraMySQL.Reference.Parameters.Instance"></a><a name="instance_params"></a><a name="db_params"></a>

 The following table shows all of the parameters that apply to a specific DB instance in an Aurora MySQL DB cluster\. 


|  Parameter name  |  Modifiable  |  Notes  | 
| --- | --- | --- | 
|   `activate_all_roles_on_login`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `allow-suspicious-udfs`   |   No   |    | 
|  `aurora_disable_hash_join`   |  Yes  |  Set this parameter to `ON` to turn off hash join optimization in Aurora MySQL version 2\.09 or higher\. It isn't supported for version 3\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.  | 
|   `aurora_lab_mode`   |   Yes   |   For more information, see [Amazon Aurora MySQL lab mode](AuroraMySQL.Updates.LabMode.md)\. Removed from Aurora MySQL version 3\.   | 
|   `aurora_oom_response`   |   Yes   |  This parameter is supported for Aurora MySQL version 2\. It isn't supported for version 3\. For more information, see [ Amazon Aurora MySQL out\-of\-memory issues ](CHAP_Troubleshooting.md#CHAP_Troubleshooting.AuroraMySQLOOM)\.  | 
|   `aurora_parallel_query`   |   Yes   |  Set to `ON` to turn on parallel query in Aurora MySQL version 2\.09 or higher\. The old `aurora_pq` parameter isn't used in these versions\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.  | 
|   `aurora_pq`   |   Yes   |  Set to `OFF` to turn off parallel query for specific DB instances in Aurora MySQL versions before 2\.09\. In version 2\.09 or higher, turn parallel query on and off with `aurora_parallel_query` instead\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.  | 
|   `autocommit`   |   Yes   |    | 
|   `automatic_sp_privileges`   |   Yes   |    | 
|   `back_log`   |   Yes   |    | 
|   `basedir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `binlog_cache_size`   |   Yes   |    | 
|   `binlog_max_flush_queue_time`   |   Yes   |    | 
|   `binlog_order_commits`   |   Yes   |    | 
|   `binlog_stmt_cache_size`   |   Yes   |    | 
|   `binlog_transaction_compression`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `binlog_transaction_compression_level_zstd`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `bulk_insert_buffer_size`   |   Yes   |    | 
|   `concurrent_insert`   |   Yes   |    | 
|   `connect_timeout`   |   Yes   |    | 
|   `core-file`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `datadir`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `default_authentication_plugin`   |   No   |   This parameter applies to Aurora MySQL version 3\.   | 
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
|   `init_connect`   |   Yes   |  The command to be run by the server for each client that connects\. Use double quotes \("\) for settings to avoid connection failures, for example: <pre>SET optimizer_switch="hash_join=off"</pre> In Aurora MySQL version 3, this parameter doesn't apply for users who have the `CONNECTION_ADMIN` privilege, including the Aurora master user\. For more information, see [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\.  | 
|   `innodb_adaptive_hash_index`   |   Yes   |  | 
|   `innodb_adaptive_max_sleep_delay`   |   Yes   |   Modifying this parameter has no effect because `innodb_thread_concurrency` is always 0 for Aurora\.   | 
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
|   `innodb_print_all_deadlocks`   |   Yes   |  When turned on, records information about all InnoDB deadlocks in the Aurora MySQL error log\. For more information, see [](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.deadlocks)\.  | 
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
|   `innodb_thread_sleep_delay`   |   Yes   |   Modifying this parameter has no effect because `innodb_thread_concurrency` is always 0 for Aurora\.   | 
|   `interactive_timeout`   |   Yes   |   Aurora evaluates the minimum value of `interactive_timeout` and `wait_timeout`\. It then uses that minimum as the timeout to end all idle sessions, both interactive and noninteractive\.   | 
|  `internal_tmp_disk_storage_engine`  | Yes |  This parameter applies to Aurora MySQL version 2\. Allowed values are `INNODB` and `MYISAM`\.  | 
|  `internal_tmp_mem_storage_engine`  |  Yes  |  This parameter applies to Aurora MySQL version 3\.  | 
|   `join_buffer_size`   |   Yes   |    | 
|   `keep_files_on_create`   |   Yes   |    | 
|  `key_buffer_size`  |   Yes   |  Key cache for MyISAM tables\. For more information, see [keycache\->cache\_lock mutex](AuroraMySQL.Reference.Waitevents.md#key-cache.cache-lock)\.  | 
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
|   `log_slave_updates`   |   No   |   Aurora MySQL version 2\. Use `log_replica_updates` in Aurora MySQL version 3\.   | 
|   `log_replica_updates`   |   No   |   Aurora MySQL version 3   | 
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
|   `performance_schema_events_transactions_history_long_size`   |  Yes  |  Aurora MySQL version 2 only  | 
|   `performance_schema_events_transactions_history_size`   |  Yes  |  Aurora MySQL version 2 only  | 
|   `performance_schema_events_waits_history_long_size`   |   Yes   |    | 
|   `performance_schema_events_waits_history_size`   |   Yes   |    | 
|   `performance_schema_hosts_size`   |   Yes   |    | 
|   `performance_schema_max_cond_classes`   |   Yes   |    | 
|   `performance_schema_max_cond_instances`   |   Yes   |    | 
|   `performance_schema_max_digest_length`   |   Yes   |    | 
|   `performance_schema_max_file_classes`   |   Yes   |    | 
|   `performance_schema_max_file_handles`   |   Yes   |    | 
|   `performance_schema_max_file_instances`   |   Yes   |    | 
|  `performance_schema_max_index_stat`  |  Yes  |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_memory_classes`   |   Yes   |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_metadata_locks`   |   Yes   |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_mutex_classes`   |   Yes   |    | 
|   `performance_schema_max_mutex_instances`   |   Yes   |    | 
|   `performance_schema_max_prepared_statements_instances`   |   Yes   |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_program_instances`   |   Yes   |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_rwlock_classes`   |   Yes   |    | 
|   `performance_schema_max_rwlock_instances`   |   Yes   |    | 
|   `performance_schema_max_socket_classes`   |   Yes   |    | 
|   `performance_schema_max_socket_instances`   |   Yes   |    | 
|   `performance_schema_max_sql_text_length`   |   Yes   |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_stage_classes`   |   Yes   |    | 
|   `performance_schema_max_statement_classes`   |   Yes   |    | 
|   `performance_schema_max_statement_stack`   |   Yes   |  Aurora MySQL version 2 only  | 
|   `performance_schema_max_table_handles`   |   Yes   |    | 
|   `performance_schema_max_table_instances`   |   Yes   |    | 
|   `performance_schema_max_table_lock_stat`   |   Yes   |  Aurora MySQL version 2 only  | 
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
|   `relay_log_recovery`  |   No   |    | 
|  `replica_checkpoint_group`  |   Yes   |   Aurora MySQL version 3   | 
|  `replica_checkpoint_period`  |   Yes   |  Aurora MySQL version 3   | 
|  `replica_parallel_workers`  |   Yes   |  Aurora MySQL version 3   | 
|  `replica_pending_jobs_size_max`  |   Yes   |  Aurora MySQL version 3   | 
|  `replica_skip_errors`  |   Yes   |  Aurora MySQL version 3   | 
|  `replica_sql_verify_checksum`  |   Yes   |  Aurora MySQL version 3   | 
|   `safe-user-create`   |   Yes   |    | 
|   `secure_auth`   |   Yes   |  This parameter is always turned on in Aurora MySQL version 2\. Trying to turn it off generates an error\. Removed from Aurora MySQL version 3\.  | 
|   `secure_file_priv`   |   No   |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `skip-slave-start`   |   No   |    | 
|   `skip_external_locking`   |   No   |    | 
|   `skip_show_database`   |   Yes   |    | 
|   `slave_checkpoint_group`   |   Yes   |   Aurora MySQL version 2\. Use `replica_checkpoint_group` in Aurora MySQL version 3\.   | 
|   `slave_checkpoint_period`   |   Yes   |   Aurora MySQL version 2\. Use `replica_checkpoint_period` in Aurora MySQL version 3\.   | 
|   `slave_parallel_workers`   |   Yes   |   Aurora MySQL version 2\. Use `replica_parallel_workers` in Aurora MySQL version 3\.   | 
|   `slave_pending_jobs_size_max`   |   Yes   |   Aurora MySQL version 2\. Use `replica_pending_jobs_size_max` in Aurora MySQL version 3\.   | 
|   `slave_sql_verify_checksum`   |   Yes   |   Aurora MySQL version 2\. Use `replica_sql_verify_checksum` in Aurora MySQL version 3\.   | 
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
|   `sync_source_info`   |   Yes   |   This parameter applies to Aurora MySQL version 3\.   | 
|   `sync_relay_log`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `sync_relay_log_info`   |   Yes   |    | 
|   `sysdate-is-now`   |   Yes   |    | 
|   `table_cache_element_entry_ttl`   |   No   |    | 
|   `table_definition_cache`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\.  | 
|   `table_open_cache`   |   Yes   |  The default value is represented by a formula\. For details about how the `DBInstanceClassMemory` value in the formula is calculated, see [DB parameter formula variables](USER_ParamValuesRef.md#USER_FormulaVariables)\.  | 
|   `table_open_cache_instances`   |   Yes   |    | 
|   `temp-pool`   |   Yes   |   Removed from Aurora MySQL version 3\.   | 
|   `temptable_max_mmap`   |   Yes   |  This parameter applies to Aurora MySQL version 3\. For details, see [New temporary table behavior in Aurora MySQL version 3](ams3-temptable-behavior.md)\.  | 
|  `temptable_max_ram`  |  Yes  |  This parameter applies to Aurora MySQL version 3\. For details, see [New temporary table behavior in Aurora MySQL version 3](ams3-temptable-behavior.md)\.  | 
|  `temptable_use_mmap`  |  Yes  |  This parameter applies to Aurora MySQL version 3\. For details, see [New temporary table behavior in Aurora MySQL version 3](ams3-temptable-behavior.md)\.  | 
|  `thread_handling`  |  No  |    | 
|   `thread_stack`   |  Yes  |    | 
|   `timed_mutexes`   |  Yes  |    | 
|  `tmp_table_size`  |  Yes  |    | 
|   `tmpdir`   |  No  |   Aurora MySQL uses managed instances where you don't access the file system directly\.   | 
|   `transaction_alloc_block_size`   |   Yes   |    | 
|   `transaction_isolation`   |   Yes   |   This parameter applies to Aurora MySQL version 3\. It replaces `tx_isolation`\.   | 
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

The following MySQL parameters don't apply to Aurora MySQL\. This list isn't exhaustive\.
+ `activate_all_roles_on_login` – This parameter doesn't apply to Aurora MySQL version 2\. It is available in Aurora MySQL version 3\.
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
+ `innodb_deadlock_detect` – This parameter doesn't apply to Aurora MySQL version 2\. It is available in Aurora MySQL version 3\.
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

 The following MySQL status variables don't apply to Aurora MySQL\. This list isn't exhaustive\. 
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

These MySQL status variables are available in Aurora MySQL version 2, but they aren't available in Aurora MySQL version 3:
+  `Innodb_redo_log_enabled` 
+  `Innodb_undo_tablespaces_total` 
+  `Innodb_undo_tablespaces_implicit` 
+  `Innodb_undo_tablespaces_explicit` 
+  `Innodb_undo_tablespaces_active` 