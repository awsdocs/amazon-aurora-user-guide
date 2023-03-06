# Configuring<a name="mysql-stored-proc-configuring"></a>

The following stored procedures set and show configuration parameters, such as for binary log file retention\.

**Topics**
+ [mysql\.rds\_set\_configuration](#mysql_rds_set_configuration)
+ [mysql\.rds\_show\_configuration](#mysql_rds_show_configuration)

## mysql\.rds\_set\_configuration<a name="mysql_rds_set_configuration"></a>

Specifies the number of hours to retain binary logs or the number of seconds to delay replication\.

### Syntax<a name="mysql_rds_set_configuration-syntax"></a>

 

```
CALL mysql.rds_set_configuration(name,value);
```

### Parameters<a name="mysql_rds_set_configuration-parameters"></a>

 *name*   
The name of the configuration parameter to set\.

 *value*   
The value of the configuration parameter\.

### Usage notes<a name="mysql_rds_set_configuration-usage-notes"></a>

The `mysql.rds_set_configuration` procedure supports the following configuration parameters:
+ [Binlog retention hours](#mysql_rds_set_configuration-usage-notes.binlog-retention-hours)

The configuration parameters are stored permanently and survive any DB instance reboot or failover\.

#### Binlog retention hours<a name="mysql_rds_set_configuration-usage-notes.binlog-retention-hours"></a>

The `binlog retention hours` parameter is used to specify the number of hours to retain binary log files\. Amazon Aurora normally purges a binary log as soon as possible, but the binary log might still be required for replication with a MySQL database external to Aurora\.

The default value of `binlog retention hours` is `NULL`\. For Aurora MySQL, `NULL` means binary logs are cleaned up lazily\. Aurora MySQL binary logs might remain in the system for a certain period, which is usually not longer than a day\.

To specify the number of hours to retain binary logs on a DB cluster, use the `mysql.rds_set_configuration` stored procedure and specify a period with enough time for replication to occur, as shown in the following example\.

`call mysql.rds_set_configuration('binlog retention hours', 24);`

For Aurora MySQL version 2 and 3 DB clusters, the maximum `binlog retention hours` value is 68 \(7 days\)\.

After you set the retention period, monitor storage usage for the DB instance to make sure that the retained binary logs don't take up too much storage\.

## mysql\.rds\_show\_configuration<a name="mysql_rds_show_configuration"></a>

The number of hours that binary logs are retained\.

### Syntax<a name="mysql_rds_show_configuration-syntax"></a>

 

```
CALL mysql.rds_show_configuration;
```

### Usage notes<a name="mysql_rds_show_configuration-usage-notes"></a>

To verify the number of hours that Amazon RDS retains binary logs, use the `mysql.rds_show_configuration` stored procedure\.

### Examples<a name="mysql_rds_show_configuration-examples"></a>

The following example displays the retention period:

```
call mysql.rds_show_configuration;
                name                         value     description
                binlog retention hours       24        binlog retention hours specifies the duration in hours before binary logs are automatically deleted.
```