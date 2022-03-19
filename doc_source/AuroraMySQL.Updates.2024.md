# Aurora MySQL database engine updates 2018\-09\-21 \(version 2\.02\.4\) \(deprecated\)<a name="AuroraMySQL.Updates.2024"></a>

**Version:** 2\.02\.4

Aurora MySQL 2\.02\.4 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster, you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\. When restoring a MySQL 5\.6\-compatible snapshot, you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 2\.01\.\*, and 2\.02\.\* into Aurora MySQL 2\.02\.4\. You can also perform an in\-place upgrade from Aurora MySQL 2\.01\.\* or 2\.02\.\* to Aurora MySQL 2\.02\.4\.

We don't allow in\-place upgrade of Aurora MySQL 1\.\* clusters into Aurora MySQL 2\.02\.4 or restore to Aurora MySQL 2\.02\.4 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

The performance schema is disabled for this release of Aurora MySQL 5\.7\. Upgrade to Aurora 2\.03 for performance schema support\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.2024.Improvements"></a>
+ Fixed a stability issue related to Full Text Search indexes on tables restored from an Aurora MySQL 5\.6 snapshot\.

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2024.Patches"></a>
+  `BUG#13651665 INNODB MAY BE UNABLE TO LOAD TABLE DEFINITION AFTER RENAME` 
+  `BUG#21371070 INNODB: CANNOT ALLOCATE 0 BYTES.` 
+  `BUG#21378944 FTS ASSERT ENC.SRC_ILIST_PTR != NULL, FTS_OPTIMIZE_WORD(), OPTIMIZE TABLE` 
+  `BUG#21508537 ASSERTION FAILURE UT_A(!VICTIM_TRX->READ_ONLY)` 
+  `BUG#21983865 UNEXPECTED DEADLOCK WITH INNODB_AUTOINC_LOCK_MODE=0` 
+  `BUG#22679185 INVALID INNODB FTS DOC ID DURING INSERT` 
+  `BUG#22899305 GCOLS: ASSERTION: !(COL->PRTYPE & 256).` 
+  `BUG#22956469 MEMORY LEAK INTRODUCED IN 5.7.8 IN MEMORY/INNODB/OS0FILE` 
+  `BUG#22996488 CRASH IN FTS_SYNC_INDEX WHEN DOING DDL IN A LOOP` 
+  `BUG#23014521 GCOL:INNODB: ASSERTION: !IS_V` 
+  `BUG#23021168 REPLICATION STOPS AFTER TRX IS ROLLED BACK ASYNC` 
+  `BUG#23052231 ASSERTION: ADD_AUTOINC < DICT_TABLE_GET_N_USER_COLS` 
+  `BUG#23149683 ROTATE INNODB MASTER KEY WITH KEYRING_OKV_CONF_DIR MISSING: SIGSEGV; SIGNAL 11` 
+  `BUG#23762382 INSERT VALUES QUERY WITH JOIN IN A SELECT CAUSES INCORRECT BEHAVIOR` 
+  `BUG#25209512 CURRENT_TIMESTAMP PRODUCES ZEROS IN TRIGGER` 
+  `BUG#26626277 BUG IN "INSERT... ON DUPLICATE KEY UPDATE" QUERY` 
+  `BUG#26734162 INCORRECT BEHAVIOR WITH INSERT OF BLOB + ON DUPLICATE KEY UPDATE` 
+  `BUG#27460607 INCORRECT WHEN INSERT SELECT's SOURCE TABLE IS EMPTY` 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2024.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. These functions are available for MySQL 5\.7\-compatible clusters in Aurora MySQL 2\.06 and higher\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2024.Compatibility"></a>

Aurora MySQL 2\.02\.4 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.02\.4 does not currently support the following MySQL 5\.7 features:
+ Global transaction identifiers \(GTIDs\)\. Aurora MySQL supports GTIDs in version 2\.04 and higher\.
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement
+ X Protocol