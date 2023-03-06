# Aurora MySQL version 2 compatible with MySQL 5\.7<a name="AuroraMySQL.CompareMySQL57"></a>

This topic describes the differences between Aurora MySQL version 2 and MySQL 5\.7 Community Edition\.

## Features not supported in Aurora MySQL version 2<a name="AuroraMySQL.CompareV2Community"></a>

The following features are supported in MySQL 5\.7, but are currently not supported in Aurora MySQL version 2:
+ `CREATE TABLESPACE` SQL statement
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin – You can install the plugin, but it isn't supported\. You can't customize the plugin\.
+ Query rewrite plugins
+ Replication filtering
+ X Protocol

For more information about these features, see the [MySQL 5\.7 documentation](https://dev.mysql.com/doc/refman/5.7/en/)\.

## Temporary tablespace behavior in Aurora MySQL version 2<a name="AuroraMySQL.TempTables57"></a>

In MySQL 5\.7, the temporary tablespace is autoextending and increases in size as necessary to accommodate on\-disk temporary tables\. When temporary tables are dropped, freed space can be reused for new temporary tables, but the temporary tablespace remains at the extended size and doesn't shrink\. The temporary tablespace is dropped and re\-created when engine is restarted\.

In Aurora MySQL version 2, the following behavior applies:
+ For new Aurora MySQL DB clusters created with version 2\.10 and higher, the temporary tablespace is removed and re\-created when you restart the database\. This allows the dynamic resizing feature to reclaim the storage space\.
+ For existing Aurora MySQL DB clusters upgraded to:
  + Version 2\.10 or higher – The temporary tablespace is removed and re\-created when you restart the database\. This allows the dynamic resizing feature to reclaim the storage space\.
  + Version 2\.09 – Temporary table space isn't removed when you restart the database\.

You can check the size of the temporary tablespace on your Aurora MySQL version 2 DB cluster by using the following query:

```
SELECT
    FILE_NAME,
    TABLESPACE_NAME,
    ROUND((TOTAL_EXTENTS * EXTENT_SIZE) / 1024 / 1024 / 1024, 4) AS SIZE
FROM
    INFORMATION_SCHEMA.FILES
WHERE
    TABLESPACE_NAME = 'innodb_temporary';
```

For more information, see [The Temporary Tablespace](https://dev.mysql.com/doc/refman/5.7/en/innodb-temporary-tablespace.html) in the MySQL documentation\.

## Storage engine for on\-disk temporary tables<a name="AuroraMySQL.StorageEngine57"></a>

Aurora MySQL version 2 uses different storage engines for on\-disk internal temporary tables depending on the role of the instance\.
+ On the writer instance, on\-disk temporary tables use the InnoDB storage engine by default\. They're stored in the temporary tablespace in the Aurora cluster volume\.

  You can change this behavior on the writer instance by modifying the value for the DB parameter `internal_tmp_disk_storage_engine`\. For more information, see [Instance\-level parameters](AuroraMySQL.Reference.ParameterGroups.md#AuroraMySQL.Reference.Parameters.Instance)\.
+ On reader instances, on\-disk temporary tables use the MyISAM storage engine, which uses local storage\. That's because read\-only instances can't store any data on the Aurora cluster volume\.