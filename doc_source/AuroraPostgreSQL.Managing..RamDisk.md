# Specifying the RAM disk for the stats\_temp\_directory<a name="AuroraPostgreSQL.Managing..RamDisk"></a>

You can use the Aurora PostgreSQL parameter, `rds.pg_stat_ramdisk_size`, to specify the system memory allocated to a RAM disk for storing the PostgreSQL `stats_temp_directory`\. The RAM disk parameter is available for all Aurora PostgreSQL versions\.

Under certain workloads, setting this parameter can improve performance and decrease IO requirements\. For more information about the `stats_temp_directory`, see [Run\-time Statistics](https://www.postgresql.org/docs/current/static/runtime-config-statistics.html#GUC-STATS-TEMP-DIRECTORY) the PostgreSQL documentation\.

To enable a RAM disk for your `stats_temp_directory`, set the `rds.pg_stat_ramdisk_size` parameter to a non\-zero value in the DB cluster parameter group used by your DB cluster\. This parameter denotes MB, so you must use an integer value\. Expressions, formulas, and functions aren't valid for the `rds.pg_stat_ramdisk_size` parameter\. Be sure to restart the DB cluster so that the change takes effect\. For information about setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

As an example, the following AWS CLI command sets the RAM disk parameter to 256 MB\.

```
aws rds modify-db-cluster-parameter-group \
    --db-cluster-parameter-group-name db-cl-pg-ramdisk-testing \
    --parameters "ParameterName=rds.pg_stat_ramdisk_size, ParameterValue=256, ApplyMethod=pending-reboot"
```

After you restart the DB cluster, run the following command to see the status of the `stats_temp_directory`:

```
postgres=> SHOW stats_temp_directory;
```

The command should return the following: 

```
stats_temp_directory
---------------------------
/rdsdbramdisk/pg_stat_tmp
(1 row)
```