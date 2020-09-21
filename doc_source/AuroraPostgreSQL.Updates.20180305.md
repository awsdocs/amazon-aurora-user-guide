# Engine Versions for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.20180305"></a>

Following, you can find information about supported versions of the Aurora with PostgreSQL compatibility database engine\. An Aurora database has two version numbers; the Aurora version number and the database engine version number\. To determine the version numbers of your Aurora PostgreSQL database, see [Identifying Your Version of Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.md#AuroraPostgreSQL.Updates.Versions)\. 

To determine which PostgreSQL engine versions are available in an AWS Region, use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\. For example:

```
aws rds describe-db-engine-versions --engine aurora-postgresql --query '*[].[EngineVersion]' --output text --region your-AWS-Region
```

For a list of AWS Regions, see [Aurora PostgreSQL Region Availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability.PostgreSQL)\.

**Topics**
+ [Version 3\.3, Compatible with PostgreSQL 11\.8](#AuroraPostgreSQL.Updates.20180305.33)
+ [Version 3\.2, Compatible with PostgreSQL 11\.7](#AuroraPostgreSQL.Updates.20180305.32)
+ [Version 3\.1, Compatible with PostgreSQL 11\.6](#AuroraPostgreSQL.Updates.20180305.31)
+ [Version 3\.0, Compatible with PostgreSQL 11\.4](#AuroraPostgreSQL.Updates.20180305.30)
+ [Version 2\.6, Compatible with PostgreSQL 10\.13](#AuroraPostgreSQL.Updates.20180305.26)
+ [Version 2\.5, Compatible with PostgreSQL 10\.12](#AuroraPostgreSQL.Updates.20180305.25)
+ [Version 2\.4, Compatible with PostgreSQL 10\.11](#AuroraPostgreSQL.Updates.20180305.24)
+ [Version 2\.3, Compatible with PostgreSQL 10\.7](#AuroraPostgreSQL.Updates.20180305.23)
+ [Version 2\.2, Compatible with PostgreSQL 10\.6](#AuroraPostgreSQL.Updates.20180305.22)
+ [Version 2\.1, Compatible with PostgreSQL 10\.5](#AuroraPostgreSQL.Updates.20180305.21)
+ [Version 2\.0, Compatible with PostgreSQL 10\.4](#AuroraPostgreSQL.Updates.20180305.20)
+ [Version 1\.8, Compatible with PostgreSQL 9\.6\.18](#AuroraPostgreSQL.Updates.20180305.18)
+ [Version 1\.7, Compatible with PostgreSQL 9\.6\.17](#AuroraPostgreSQL.Updates.20180305.17)
+ [Version 1\.6, Compatible with PostgreSQL 9\.6\.16](#AuroraPostgreSQL.Updates.20180305.16)
+ [Version 1\.5, Compatible with PostgreSQL 9\.6\.12](#AuroraPostgreSQL.Updates.20180305.15)
+ [Version 1\.4, Compatible with PostgreSQL 9\.6\.11](#AuroraPostgreSQL.Updates.20180305.14)
+ [Version 1\.3, Compatible with PostgreSQL 9\.6\.9](#AuroraPostgreSQL.Updates.20180305.13)
+ [Version 1\.2, Compatible with PostgreSQL 9\.6\.8](#AuroraPostgreSQL.Updates.20180305.12)
+ [Version 1\.1, Compatible with PostgreSQL 9\.6\.6 is deprecated](#AuroraPostgreSQL.Updates.20180305.11)
+ [Version 1\.0, Compatible with PostgreSQL 9\.6\.3 is deprecated](#AuroraPostgreSQL.Updates.20180305.10)

For information on extensions and modules, see [Extensions and Modules for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Extensions.md)\.

The following Aurora PostgreSQL versions are supported\. 

## Version 3\.3, Compatible with PostgreSQL 11\.8<a name="AuroraPostgreSQL.Updates.20180305.33"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 11\.8\. For more information about the improvements in release 11\.8, see [PostgreSQL Release 11\.8](https://www.postgresql.org/docs/11/release-11-8.html)\.

**Topics**
+ [Version 3\.3\.1](#AuroraPostgreSQL.Updates.20180305.331)
+ [Version 3\.3\.0](#AuroraPostgreSQL.Updates.20180305.330)

### Version 3\.3\.1<a name="AuroraPostgreSQL.Updates.20180305.331"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Version 3\.3\.0<a name="AuroraPostgreSQL.Updates.20180305.330"></a>

**New Features**

1. Added support for the RDKit extension version 3\.8\. 

   The RDKit extension provides modeling functions for cheminformatics\. Cheminformatics is storing, indexing, searching, retrieving, and applying information about chemical compounds\. For example, with the RDKit extension you can construct models of molecules, search for molecular structures, and read or create molecules in various notations\. You can also perform research on data loaded from the [ChEMBL website](https://www.ebi.ac.uk/chembl/) or a SMILES file\. The Simplified Molecular Input Line Entry System \(SMILES\) is a typographical notation for representing molecules and reactions\. For more information, see [The RDKit database cartridge](http://rdkit.org/docs_temp/Cartridge.html) in the RDKit documentation\. 

1. Added support for a minimum TLS version

   Support for a minimum Transport Layer Security \(TLS\) version is back ported from PostgreSQL 12\. It allows the Aurora PostgreSQL server to constrain the TLS protocols with which a client is allowed to connect via two new PostgreSQL parameters\. These parameters include [ssl\_min\_protocol\_version](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-SSL-MIN-PROTOCOL-VERSION)and [ssl\_max\_protocol\_version](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-SSL-MAX-PROTOCOL-VERSION)\. For example, to limit client connections to the Aurora PostgreSQL server to at least TLS 1\.2 protocol version, set the `ssl_min_protocol_version` to `TLSv1.2`\.

**Critical Stability Enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\.

**High Priority Stability Enhancements**

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary AWS Region\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\.

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 11\.8\.

**Additional Improvements and Enhancements**

1. Fixed a bug where the Aurora replica crashed when workloads with heavy subtransactions are made on the writer instance\.

1. Fixed a bug where the writer instance crashed due to a memory leak and the depletion of memory used to track active transactions\.

1. Fixed a bug that lead to a crash due to improper initialization when there is no free memory available during PostgreSQL backend startup\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

1. Fixed an out\-of\-memory problem when issuing the `CREATE EXTENSION` command with PostGIS when Database Activity Streams enabled\.

1. Fixed a bug where a `SELECT` query might incorrectly return the error Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that executed on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug where an Aurora PostgreSQL DB cluster with Database Activity Streams enabled might report the beginning of a potential loss window for activity records, but does not report the restoration of connectivity\.

## Version 3\.2, Compatible with PostgreSQL 11\.7<a name="AuroraPostgreSQL.Updates.20180305.32"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 11\.7\. For more information about the improvements in release 11\.7, see [PostgreSQL Release 11\.7](https://www.postgresql.org/docs/11/release-11-7.html)\.

**Topics**
+ [Version 3\.2\.4](#AuroraPostgreSQL.Updates.20180305.324)
+ [Version 3\.2\.3](#AuroraPostgreSQL.Updates.20180305.323)
+ [Version 3\.2\.2](#AuroraPostgreSQL.Updates.20180305.322)
+ [Version 3\.2\.1](#AuroraPostgreSQL.Updates.20180305.321)

### Version 3\.2\.4<a name="AuroraPostgreSQL.Updates.20180305.324"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Version 3\.2\.3<a name="AuroraPostgreSQL.Updates.20180305.323"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**
+ None

**High Priority Stability Enhancements**
+ None

**Additional Improvements and Enhancements**

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that ran on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug that might cause brief unavailability for heavy subtransaction workloads when multiple reader instances restart or rejoin the cluster\.

### Version 3\.2\.2<a name="AuroraPostgreSQL.Updates.20180305.322"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\. 

**High Priority Stability Enhancements**

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary region\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\. 

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 11\.7\.

**Additional Improvements and Enhancements**

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug where a SELECT query might incorrectly return the error, Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

### Version 3\.2\.1<a name="AuroraPostgreSQL.Updates.20180305.321"></a>

**New Features**

1. Added support for Amazon Aurora PostgreSQL Global Database\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\.

1. Added the ability to configure the recovery point objective \(RPO\) of a global database for Aurora PostgreSQL\. For more information, see [Managing Recovery for Aurora Global Databases](aurora-global-database-managing.md#aurora-global-database-manage-recovery)\.

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**  
None\.

**High Priority Stability Enhancements**

1. Improved performance and availability of read instances when applying DROP TABLE and TRUNCATE TABLE operations\.

1. Fixed a small but continuous memory leak in a diagnostic module that could lead to an out\-of\-memory condition on smaller DB instance types\.

1. Fixed a bug in the `PostGIS` extension which could lead to a database restart\. This has been reported to the PostGIS community as [https://trac.osgeo.org/postgis/ticket/4646](https://trac.osgeo.org/postgis/ticket/4646)\.

1. Fixed a bug where read requests might stop responding due to incorrect error handling in the storage engine\.

1. Fixed a bug that fails for some queries and results in the message ERROR: found xmin xxxxxx from before relfrozenxid yyyyyyy\. This could occur following the promotion of a read instance to a write instance\.

1. Fixed a bug where an Aurora serverless DB cluster might crash while rolling back a scale attempt\.

**Additional Improvements and Enhancements**

1. Improved performance for queries that read many rows from storage\.

1. Improved performance and availability of reader DB instances during heavy read workload\.

1. Enabled correlated IN and NOT IN subqueries to be transformed to joins when possible\.

1. Improved the filtering estimation for enhanced semi\-join filter pushdown by using multi\-column statistics or indexes when available\.

1. Improved read performance of the `pg_prewarm` extension\.

1. Fixed a bug where an Aurora serverless DB cluster might report the message ERROR: incorrect binary data format in bind parameter \.\.\. following a scale event\.

1. Fixed a bug where a serverless DB cluster might report the message ERROR: insufficient data left in message following a scale event\.

1. Fixed a bug where an Aurora serverless DB cluster can experience prolonged or failed scale attempts\.

1. Fixed a bug that resulted in the message ERROR: could not create file "base/xxxxxx/yyyyyyy" as a previous version still exists on disk: Success\. Please contact AWS customer support\. This can occur during object creation after PostgreSQL's 32\-bit object identifier has wrapped around\. 

1. Fixed a bug where the write\-ahead\-log \(WAL\) segment files for PostgreSQL logical replication were not deleted when changing the `wal_level` value from `logical` to `replica`\.

1. Fixed a bug in the `pg_hint_plan` extension where a multi\-statement query could lead to a crash when `enable_hint_table` is enabled\. This is tracked in the PostgreSQL community as [https://github.com/ossc-db/pg_hint_plan/issues/25](https://github.com/ossc-db/pg_hint_plan/issues/25)\.

1. Fixed a bug where JDBC clients might report the message java\.io\.IOException: Unexpected packet type: 75 following a scale event in an Aurora serverless DB cluster\.

1. Fixed a bug in PostgreSQL logical replication that resulted in the message ERROR: snapshot reference is not owned by resource owner TopTransaction\.

1. Changed the following extensions:
   + Updated `orafce` to version 3\.8
   + Updated `pgTAP` to version 1\.1 

1. Provided support for fault injection queries\.

## Version 3\.1, Compatible with PostgreSQL 11\.6<a name="AuroraPostgreSQL.Updates.20180305.31"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 11\.6\. For more information about the improvements in release 11\.6, see [PostgreSQL Release 11\.6](https://www.postgresql.org/docs/11/release-11-6.html)\.

This release contains multiple critical stability enhancements\. Amazon highly recommends upgrading your Aurora PostgreSQL clusters that use older PostgreSQL 11 engines to this release\.

**Topics**
+ [Version 3\.1\.3](#AuroraPostgreSQL.Updates.20180305.313)
+ [Version 3\.1\.2](#AuroraPostgreSQL.Updates.20180305.312)
+ [Version 3\.1\.1](#AuroraPostgreSQL.Updates.20180305.311)
+ [Version 3\.1\.0](#AuroraPostgreSQL.Updates.20180305.310)

### Version 3\.1\.3<a name="AuroraPostgreSQL.Updates.20180305.313"></a>

**Critical Stability Enhancements**
+ None 

**High Priority Stability Enhancements**

1. Fixed a bug where reads from storage might stop responding due to incorrect error handling\.

**Additional Improvements and Enhancements**
+ None

### Version 3\.1\.2<a name="AuroraPostgreSQL.Updates.20180305.312"></a>

This release contains a critical stability enhancement\. Amazon highly recommends updating your older Aurora PostgreSQL 11\-compatible clusters to this release\.

**Critical Stability Enhancements**
+ Fixed a bug whereby a reader DB instance might temporarily use stale data\. This could lead to wrong results such as too few or too many rows\. This error is not persisted on storage, and will clear when the database page containing the row has been evicted from cache\. This can happen when the primary DB instance enters a transaction snapshot overflow due to having more than 64 subtransactions in a single transaction\. Applications susceptible to this bug include those that use SQL savepoints or PostgreSQL exception handlers with more than 64 subtransactions in the top transaction\. 

**High Priority Stability Enhancements**
+ Fixed a bug that might cause a reader DB instance to crash causing unavailability while attempting to the join the DB cluster\. This can happen in some cases when the primary DB instance has a transaction snapshot overflow due to a high number of subtransactions\. In this situation the reader DB instance will be unable to join until the snapshot overflow has cleared\.

**Additional Improvements and Enhancements**
+ Fixed a bug that prevented Performance Insights from determining the query ID of a running statement\.

### Version 3\.1\.1<a name="AuroraPostgreSQL.Updates.20180305.311"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug whereby the DB instance might be briefly unavailable due to the self\-healing function of the underlying storage\. 

**High Priority Stability Enhancements**

1. Fixed a bug whereby the database engine might crash causing unavailability\. This occurred while scanning an included, non\-key column of a B\-Tree index\. This only applies to PostgreSQL 11 "included column" indexes\.

1. Fixed a bug that might cause the database engine to crash causing unavailability\. This occurred if a newly established database connection encountered a resource exhaustion\-related error during initialization after successful authentication\.

**Additional Improvements and Enhancements**

1. Provided a fix for the `pg_hint_plan` extension that could lead the database engine to crash causing unavailability\. The open source issue can be tracked at [https://github.com/ossc-db/pg_hint_plan/pull/45](https://github.com/ossc-db/pg_hint_plan/pull/45)\.

1. Fixed a bug where SQL of the form `ALTER FUNCTION ... OWNER TO ...` would incorrectly report `ERROR: improper qualified name (too many dotted names)`\.

1. Improved the performance of `GIN` index vacuum via prefetching\.

1. Fixed a bug in open source PostgreSQL that could lead to a database engine crash causing unavailability\. This occurred during parallel B\-Tree index scans\. This issue has been reported to the PostgreSQL community\. 

1. Improved the performance of in\-memory B\-Tree index scans\.

### Version 3\.1\.0<a name="AuroraPostgreSQL.Updates.20180305.310"></a>

You can find the following new features and improvements in this engine version\.

**New Features**

1. Support for exporting data to Amazon S3\. For more information, see [Exporting Data from an Aurora PostgreSQL DB Cluster to Amazon S3](postgresql-s3-export.md)\.

1. Support for Amazon Aurora Machine Learning\. For more information, see [Using Machine Learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.

1. SQL processing enhancements include:
   + Optimizations for `NOT IN` with the `apg_enable_not_in_transform` parameter\.
   + Semi\-join filter pushdown enhancements for hash joins with the `apg_enable_semijoin_push_down` parameter\.
   + Optimizations for redundant inner join removal with the `apg_enable_remove_redundant_inner_joins` parameter\.
   + Improved ANSI compatibility options with the `ansi_constraint_trigger_ordering`, `ansi_force_foreign_key_checks` and `ansi_qualified_update_set_target` parameters\.

   For more information, see [Amazon Aurora PostgreSQL Parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)\.

1. New and updated PostgreSQL extensions include:
   + The new `aws_ml` extension\. For more information, see [Using Machine Learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.
   + The new `aws_s3` extension\. For more information, see [Exporting Data from an Aurora PostgreSQL DB Cluster to Amazon S3](postgresql-s3-export.md)\.
   + Updates to the `apg_plan_mgmt` extension\. For more information, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)

**Critical Stability Enhancements**

1. Fixed a bug related to creating B\-tree indexes on temporary tables that in rare cases might result in longer recovery time, and impact availability\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance\. In rare cases, this bug causes a log write failure that might result in longer recovery time, and impact availability\. 

1. Fixed a bug related to handling of reads with high I/O latency that in rare cases might result in longer recovery time, and impact availability\.

**High Priority Stability Enhancements**

1. Fixed a bug related to logical replication whereby `wal` segments are not properly removed from storage\. This can result in storage bloat\. To monitor this, view the `TransactionLogDiskUsage` parameter\. 

1. Fixed multiple bugs, which cause Aurora to crash during prefetch operations on Btree indexes\.

1. Fixed a bug whereby an Aurora restart might time out when logical replication is used\.

1. Enhanced the validation checks performed on data blocks in the buffer cache\. This improves Aurora's detection of inconsistency\.

**Additional Improvements and Enhancements**

1. The query plan management extension `apg_plan_mgmt` has an improved algorithm for managing plan generation for highly partitioned tables\.

1. Reduced startup time on instances with large caches via improvements in the buffer cache recovery algorithm\.

1. Improved the performance of the read\-node\-apply process under high transaction rate workloads by using changes to PostgreSQL `LWLock` prioritization\. These changes prevent starvation of the read\-node\-apply process while the PostgreSQL `ProcArray` is under heavy contention\.

1. Improved handling of batch reads during vacuum, table scans, and index scans\. This results in greater throughput and lower CPU consumption\.

1. Fixed a bug whereby a read node might crash during the replay of a PostgreSQL `SLRU`\-truncate operation\.

1. Fixed a bug where in rare cases, database writes might stall following an error returned by one of the six copies of an Aurora log record\. 

1. Fixed a bug related to logical replication where an individual transaction larger than 1 GB in size might result in an engine crash\.

1. Fixed a memory leak on read nodes when cluster cache management is enabled\.

1. Fixed a bug whereby importing an RDS PostgreSQL snapshot might stop responding if the source snapshot contains a large number of unlogged relations\.

1. Fixed a bug whereby the Aurora storage daemon might crash under heavy I/O load\.

1. Fixed a bug related to `hot_standby_feedback` for read nodes whereby the read node might report the wrong transaction id epoch to the write node\. This can cause the write node to ignore the `hot_standby_feedback` and invalidate snapshots on the read node\.

1. Fixed a bug whereby storage errors that occur during `CREATE DATABASE` statements are not properly handled\. The bug would leave the resulting database inaccessible\. The correct behavior is to fail the database creation and return the appropriate error to the user\.

1. Improved handling of PostgreSQL snapshot overflow when a read node attempts to connect to a write node\. Prior to this change, if the write node was in a snapshot overflow state, the read node would be unable to join\. A message would appear in the PostgreSQL log file in the form, `DEBUG: recovery snapshot waiting for non-overflowed snapshot or until oldest active xid on standby is at least xxxxxxx (now yyyyyyy)`\. A snapshot overflow occurs when an individual transaction has created over 64 subtransactions\. 

1. Fixed a bug related to common table expressions whereby an error is incorrectly raised when a NOT IN class exists in a CTE\. The error is `CTE with NOT IN fails with ERROR: could not find CTE CTE-Name`\.

1. Fixed a bug related to an incorrect `last_error_timestamp` value in the `aurora_replica_status` table\.

1. Fixed a bug to avoid populating shared buffers with blocks belonging to temporary objects\. These blocks correctly reside in PostgreSQL backend local buffers\.

1. Changed the following extensions:
   + Updated `pg_hint_plan` to version 1\.3\.4\.
   + Added `plprofiler` version 4\.1\.
   + Added `pgTAP` version 1\.0\.0\.

## Version 3\.0, Compatible with PostgreSQL 11\.4<a name="AuroraPostgreSQL.Updates.20180305.30"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 11\.4\. For more information about the improvements in release 11\.4, see [PostgreSQL Release 11\.4](https://www.postgresql.org/docs/11/release-11-4.html)\.

You can find the following improvements in this engine update\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [Version 2\.3\.5](#AuroraPostgreSQL.Updates.20180305.235)\.

1. Partitioning – Partitioning improvements include support for hash partitioning, enabling creation of a default partition, and dynamic row movement to another partition based on the key column update\.

1. Performance – Performance improvements include parallelism while creating indexes, materialized views, hash joins, and sequential scans to make the operations perform better\.

1. Stored procedures – SQL stored procedures now added support for embedded transactions\.

1. Autovacuum improvements – To provide valuable logging, the parameter `rds.force_autovacuum_logging` is ON by default in conjunction with the `log_autovacuum_min_duration` parameter set to 10 seconds\. To increase autovacuum effectiveness, the values for the `autovacuum_max_workers` and `autovacuum_vacuum_cost_limit` parameters are computed based on host memory capacity to provide larger default values\.

1. Improved transaction timeout – The parameter `idle_in_transaction_session_timeout` is set to 12 hours\. Any session that has been idle more than 12 hours is terminated\.

1. The `tsearch2` module is no longer supported – If your application uses `tsearch2` functions, update it to use the equivalent functions provided by the core PostgreSQL engine\. For more information about the tsearch2 module, see [ PostgreSQL tsearch2](https://www.postgresql.org/docs/9.6/tsearch2.html)\.

1. The `chkpass` module is no longer supported – For more information about the chkpass module, see [PostgreSQL chkpass](https://www.postgresql.org/docs/10/chkpass.html)\. 

1. Updated the following extensions:
   + `address_standardizer` to version 2\.5\.1
   +  `address_standardizer_data_us` to version 2\.5\.1
   +  `btree_gin` to version 1\.3
   +  `citext` to version 1\.5
   +  `cube` to version 1\.4
   +  `hstore` to version 1\.5
   +  `ip4r` to version 2\.2
   +  `isn` to version 1\.2
   +  `orafce` to version 3\.7
   +  `pg_hint_plan` to version 1\.3\.4
   +  `pg_prewarm` to version 1\.2
   +  `pg_repack` to version 1\.4\.4
   +  `pg_trgm` to version 1\.4
   +  `pgaudit` to version 1\.3
   +  `pgrouting` to version 2\.6\.1
   +  `pgtap` to version 1\.0\.0
   +  `plcoffee` to version 2\.3\.8
   +  `plls` to version 2\.3\.8
   +  `plv8` to version 2\.3\.8
   +  `postgis` to version 2\.5\.1
   +  `postgis_tiger_geocoder` to version 2\.5\.1
   +  `postgis_topology` to version 2\.5\.1
   +  `rds_activity_stream` to version 1\.3

## Version 2\.6, Compatible with PostgreSQL 10\.13<a name="AuroraPostgreSQL.Updates.20180305.26"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.13\. For more information about the improvements in release 10\.13, see [ PostgreSQL Release 10\.13](https://www.postgresql.org/docs/10/release-10-13.html)\.

**Topics**
+ [Version 2\.6\.1](#AuroraPostgreSQL.Updates.20180305.261)
+ [Version 2\.6\.0](#AuroraPostgreSQL.Updates.20180305.260)

### Version 2\.6\.1<a name="AuroraPostgreSQL.Updates.20180305.261"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Version 2\.6\.0<a name="AuroraPostgreSQL.Updates.20180305.260"></a>

You can find the following improvements in this engine update\.

**New Features**

1. Added support for the RDKit extension version 3\.8\. 

   The RDKit extension provides modeling functions for cheminformatics\. Cheminformatics is storing, indexing, searching, retrieving, and applying information about chemical compounds\. For example, with the RDKit extension you can construct models of molecules, search for molecular structures, and read or create molecules in various notations\. You can also perform research on data loaded from the [ChEMBL website](https://www.ebi.ac.uk/chembl/) or a SMILES file\. The Simplified Molecular Input Line Entry System \(SMILES\) is a typographical notation for representing molecules and reactions\. For more information, see [The RDKit database cartridge](http://rdkit.org/docs_temp/Cartridge.html) in the RDKit documentation\. 

**Critical Stability Enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\.

**High Priority Stability Enhancements**

1. Fixed a bug when upgrading Aurora Global Database clusters from 10\.11\.

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary AWS Region\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\.

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 10\.13\.

**Additional Improvements and Enhancements**

1. Fixed a bug where the Aurora replica crashed when workloads with heavy subtransactions are made on the writer instance\.

1. Fixed a bug where the writer instance crashed due to a memory leak and the depletion of memory used to track active transactions\.

1. Fixed a bug that lead to a crash due to improper initialization when there is no free memory available during PostgreSQL backend startup\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

1. Fixed an out\-of\-memory problem when issuing the `CREATE EXTENSION` command with PostGIS when Database Activity Streams enabled\.

1. Fixed a bug where a `SELECT` query might incorrectly return the error Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that executed on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug where an Aurora PostgreSQL DB cluster with Database Activity Streams enabled might report the beginning of a potential loss window for activity records, but does not report the restoration of connectivity\.

## Version 2\.5, Compatible with PostgreSQL 10\.12<a name="AuroraPostgreSQL.Updates.20180305.25"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.12\. For more information about the improvements in release 10\.12, see [ PostgreSQL Release 10\.12](https://www.postgresql.org/docs/10/release-10-12.html)\.

**Topics**
+ [Version 2\.5\.4](#AuroraPostgreSQL.Updates.20180305.254)
+ [Version 2\.5\.3](#AuroraPostgreSQL.Updates.20180305.253)
+ [Version 2\.5\.2](#AuroraPostgreSQL.Updates.20180305.252)
+ [Version 2\.5\.1](#AuroraPostgreSQL.Updates.20180305.251)

### Version 2\.5\.4<a name="AuroraPostgreSQL.Updates.20180305.254"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Version 2\.5\.3<a name="AuroraPostgreSQL.Updates.20180305.253"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**
+ None

**High Priority Stability Enhancements**
+ None

**Additional Improvements and Enhancements**

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that ran on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug that might cause brief unavailability for heavy subtransaction workloads when multiple reader instances restart or rejoin the cluster\.

1. Fixed a bug in Aurora PostgreSQL Global Database where upgrading a secondary cluster might result in failure due to incorrect checksum versioning\. This might have required re\-creating the secondary clusters\.

### Version 2\.5\.2<a name="AuroraPostgreSQL.Updates.20180305.252"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\. 

**High Priority Stability Enhancements**

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary region\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\. 

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 10\.12\.

**Additional Improvements and Enhancements**

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug where a SELECT query might incorrectly return the error, Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

### Version 2\.5\.1<a name="AuroraPostgreSQL.Updates.20180305.251"></a>

**New Features**

1. Added support for Amazon Aurora PostgreSQL Global Database\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\.

1. Added the ability to configure the recovery point objective \(RPO\) of a global database for Aurora PostgreSQL\. For more information, see [Managing Recovery for Aurora Global Databases](aurora-global-database-managing.md#aurora-global-database-manage-recovery)\.

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**  
None\.

**High Priority Stability Enhancements**

1. Improved performance and availability of read instances when applying DROP TABLE and TRUNCATE TABLE operations\.

1. Fixed a small but continuous memory leak in a diagnostic module that could lead to an out\-of\-memory condition on smaller DB instance types\.

1. Fixed a bug in the `PostGIS` extension which could lead to a database restart\. This has been reported to the PostGIS community as [https://trac.osgeo.org/postgis/ticket/4646](https://trac.osgeo.org/postgis/ticket/4646)\.

1. Fixed a bug where read requests might stop responding due to incorrect error handling in the storage engine\.

1. Fixed a bug that fails for some queries and results in the message ERROR: found xmin xxxxxx from before relfrozenxid yyyyyyy\. This could occur following the promotion of a read instance to a write instance\.

1. Fixed a bug where an Aurora serverless DB cluster might crash while rolling back a scale attempt\.

**Additional Improvements and Enhancements**

1. Improved performance for queries that read many rows from storage\.

1. Improved performance and availability of reader DB instances during heavy read workload\.

1. Enabled correlated IN and NOT IN subqueries to be transformed to joins when possible\.

1. Improved read performance of the `pg_prewarm` extension\.

1. Fixed a bug where an Aurora serverless DB cluster might report the message ERROR: incorrect binary data format in bind parameter \.\.\. following a scale event\.

1. Fixed a bug where a serverless DB cluster might report the message ERROR: insufficient data left in message following a scale event\.

1. Fixed a bug where an Aurora serverless DB cluster may experience prolonged or failed scale attempts\.

1. Fixed a bug that resulted in the message ERROR: could not create file "base/xxxxxx/yyyyyyy" as a previous version still exists on disk: Success\. Please contact AWS customer support\. This can occur during object creation after PostgreSQL's 32\-bit object identifier has wrapped around\. 

1. Fixed a bug where the write\-ahead\-log \(WAL\) segment files for PostgreSQL logical replication were not deleted when changing the `wal_level` value from `logical` to `replica`\.

1. Fixed a bug in the `pg_hint_plan` extension where a multi\-statement query could lead to a crash when `enable_hint_table` is enabled\. This is tracked in the PostgreSQL community as [https://github.com/ossc-db/pg_hint_plan/issues/25](https://github.com/ossc-db/pg_hint_plan/issues/25)\.

1. Fixed a bug where JDBC clients might report the message java\.io\.IOException: Unexpected packet type: 75 following a scale event in an Aurora serverless DB cluster\.

1. Fixed a bug in PostgreSQL logical replication that resulted in the message ERROR: snapshot reference is not owned by resource owner TopTransaction\.

1. Changed the following extensions:
   + Updated `orafce` to version 3\.8

## Version 2\.4, Compatible with PostgreSQL 10\.11<a name="AuroraPostgreSQL.Updates.20180305.24"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.11\. For more information about the improvements in release 10\.11, see [PostgreSQL Release 10\.11](https://www.postgresql.org/docs/10/release-10-11.html)\.

This release contains multiple critical stability enhancements\. Amazon highly recommends upgrading your Aurora PostgreSQL clusters that use older PostgreSQL 10 engines to this release\.

**Topics**
+ [Version 2\.4\.3](#AuroraPostgreSQL.Updates.20180305.243)
+ [Version 2\.4\.2](#AuroraPostgreSQL.Updates.20180305.242)
+ [Version 2\.4\.1](#AuroraPostgreSQL.Updates.20180305.241)
+ [Version 2\.4\.0](#AuroraPostgreSQL.Updates.20180305.240)

### Version 2\.4\.3<a name="AuroraPostgreSQL.Updates.20180305.243"></a>

**Critical Stability Enhancements**
+ None 

**High Priority Stability Enhancements**

1. Fixed a bug where reads from storage might stop responding due to incorrect error handling\.

**Additional Improvements and Enhancements**
+ None

### Version 2\.4\.2<a name="AuroraPostgreSQL.Updates.20180305.242"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug whereby a reader DB instance might temporarily use stale data\. This could lead to wrong results such as too few or too many rows\. This error is not persisted on storage, and will clear when the database page containing the row has been evicted from cache\. This can happen when the primary DB instance enters a transaction snapshot overflow due to having more than 64 subtransactions in a single transaction\. Applications susceptible to this bug include those that use SQL savepoints or PostgreSQL exception handlers with more than 64 subtransactions in the top transaction\. 

**High Priority Stability Enhancements**

1. Fixed a bug that may cause a reader DB instance to crash causing unavailability while attempting to the join the DB cluster\. This can happen in some cases when the primary DB instance has a transaction snapshot overflow due to a high number of subtransactions\. In this situation the reader DB instance will be unable to join until the snapshot overflow has cleared\.

**Additional Improvements and Enhancements**

1. Fixed a bug that prevented Performance Insights from determining the query ID of a running statement\.

### Version 2\.4\.1<a name="AuroraPostgreSQL.Updates.20180305.241"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug whereby the DB instance might be briefly unavailable due to the self\-healing function of the underlying storage\. 

**High Priority Stability Enhancements**

1. Fixed a bug that might cause the database engine to crash causing unavailability\. This occurred if a newly established database connection encountered a resource exhaustion\-related error during initialization after successful authentication\.

**Additional Improvements and Enhancements**

1. Provided a fix for the `pg_hint_plan` extension that could lead the database engine to crash causing unavailability\. The open source issue can be tracked at [https://github.com/ossc-db/pg_hint_plan/pull/45](https://github.com/ossc-db/pg_hint_plan/pull/45)\.

1. Fixed a bug where SQL of the form `ALTER FUNCTION ... OWNER TO ...` would incorrectly report `ERROR: improper qualified name (too many dotted names)`\.

1. Improved the performance of `GIN` index vacuum via prefetching\.

1. Fixed a bug in open source PostgreSQL that could lead to a database engine crash causing unavailability\. This occurred during parallel B\-Tree index scans\. This issue has been reported to the PostgreSQL community\. 

1. Improved the performance of in\-memory B\-Tree index scans\.

1. Additional general improvements to the stability and availability of Aurora PostgreSQL\.

### Version 2\.4\.0<a name="AuroraPostgreSQL.Updates.20180305.240"></a>

You can find the following new features and improvements in this engine version\.

**New Features**

1. Support for exporting data to Amazon S3\. For more information, see [Exporting Data from an Aurora PostgreSQL DB Cluster to Amazon S3](postgresql-s3-export.md)\.

1. Support for Amazon Aurora Machine Learning\. For more information, see [Using Machine Learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.

1. SQL processing enhancements include:
   + Optimizations for `NOT IN` with the `apg_enable_not_in_transform` parameter\.
   + Semi\-join filter pushdown enhancements for hash joins with the `apg_enable_semijoin_push_down` parameter\.
   + Optimizations for redundant inner join removal with the `apg_enable_remove_redundant_inner_joins` parameter\.
   + Improved ANSI compatibility options with the `ansi_constraint_trigger_ordering`, `ansi_force_foreign_key_checks` and `ansi_qualified_update_set_target` parameters\.

   For more information, see [Amazon Aurora PostgreSQL Parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)\.

1. New and updated PostgreSQL extensions include:
   + The new `aws_ml` extension\. For more information, see [Using Machine Learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.
   + The new `aws_s3` extension\. For more information, see [Exporting Data from an Aurora PostgreSQL DB Cluster to Amazon S3](postgresql-s3-export.md)\.
   + Updates to the `apg_plan_mgmt` extension\. For more information, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)

**Critical Stability Enhancements**

1. Fixed a bug related to creating B\-tree indexes on temporary tables that in rare cases may result in longer recovery time, and impact availability\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance\. In rare cases, this bug causes a log write failure that may result in longer recovery time, and impact availability\. 

1. Fixed a bug related to handling of reads with high I/O latency that in rare cases may result in longer recovery time, and impact availability\.

**High Priority Stability Enhancements**

1. Fixed a bug related to logical replication whereby `wal` segments are not properly removed from storage\. This can result in storage bloat\. To monitor this, view the `TransactionLogDiskUsage` parameter\. 

1. Fixed multiple bugs, which cause Aurora to crash during prefetch operations on Btree indexes\.

1. Fixed a bug whereby an Aurora restart may timeout when logical replication is used\.

1. Enhanced the validation checks performed on data blocks in the buffer cache\. This improves Aurora's detection of inconsistency\.

**Additional Improvements and Enhancements**

1. The query plan management extension `apg_plan_mgmt` has an improved algorithm for managing plan generation for highly partitioned tables\.

1. Reduced startup time on instances with large caches via improvements in the buffer cache recovery algorithm\.

1. Improved the performance of the read\-node\-apply process under high transaction rate workloads by using changes to PostgreSQL `LWLock` prioritization\. These changes prevent starvation of the read\-node\-apply process while the PostgreSQL `ProcArray` is under heavy contention\.

1. Improved handling of batch reads during vacuum, table scans, and index scans\. This results in greater throughput and lower CPU consumption\.

1. Fixed a bug whereby a read node may crash during the replay of a PostgreSQL `SLRU`\-truncate operation\.

1. Fixed a bug where in rare cases, database writes may stall following an error returned by one of the six copies of an Aurora log record\. 

1. Fixed a bug related to logical replication where an individual transaction larger than 1 Gb in size may result in an engine crash\.

1. Fixed a memory leak on read nodes when cluster cache management is enabled\.

1. Fixed a bug whereby importing an RDS PostgreSQL snapshot might stop responding if the source snapshot contains a large number of unlogged relations\.

1. Fixed a bug whereby the Aurora storage daemon may crash under heavy I/O load\.

1. Fixed a bug related to `hot_standby_feedback` for read nodes whereby the read node may report the wrong transaction id epoch to the write node\. This can cause the write node to ignore the `hot_standby_feedback` and invalidate snapshots on the read node\.

1. Fixed a bug whereby storage errors that occur during `CREATE DATABASE` statements are not properly handled\. The bug would leave the resulting database inaccessible\. The correct behavior is to fail the database creation and return the appropriate error to the user\.

1. Improved handling of PostgreSQL snapshot overflow when a read node attempts to connect to a write node\. Prior to this change, if the write node was in a snapshot overflow state, the read node would be unable to join\. A message would appear in the PostgreSQL log file in the form, `DEBUG: recovery snapshot waiting for non-overflowed snapshot or until oldest active xid on standby is at least xxxxxxx (now yyyyyyy)`\. A snapshot overflow occurs when an individual transaction has created over 64 subtransactions\. 

1. Fixed a bug related to common table expressions whereby an error is incorrectly raised when a NOT IN class exists in a CTE\. The error is `CTE with NOT IN fails with ERROR: could not find CTE CTE-Name`\.

1. Fixed a bug related to an incorrect `last_error_timestamp` value in the `aurora_replica_status` table\.

1. Fixed a bug to avoid populating shared buffers with blocks belonging to temporary objects\. These blocks correctly reside in PostgreSQL backend local buffers\. 

1. Improved the performance of vacuum cleanup on GIN indexes\.

1. Fixed a bug where in rare cases Aurora may exhibit 100% CPU utilization while acting as a replica of an RDS PostgreSQL instance even when the replication stream is idle\. 

1. Backported a change from PostgreSQL 11 which improves the cleanup of orphaned temporary tables\. Without this change, it is possible that in rare cases orphaned temporary tables can lead to transaction ID wraparound\. For more information, see this [PostgreSQL community commit](https://github.com/postgres/postgres/commit/246a6c8f7b237cc1943efbbb8a7417da9288f5c4)\. 

1. Fixed a bug where a Writer instance may accept replication registration requests from Reader instances while having an uninitialized startup process\.

1. Changed the following extensions:
   + Updated `pg_hint_plan` to version 1\.3\.3\.
   + Added `plprofiler` version 4\.1\.

## Version 2\.3, Compatible with PostgreSQL 10\.7<a name="AuroraPostgreSQL.Updates.20180305.23"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.7\. For more information about the improvements in release 10\.7, see [PostgreSQL Release 10\.7](https://www.postgresql.org/docs/10/release-10-7.html)\.

**Topics**
+ [Version 2\.3\.5](#AuroraPostgreSQL.Updates.20180305.235)
+ [Version 2\.3\.3](#AuroraPostgreSQL.Updates.20180305.233)
+ [Version 2\.3\.1](#AuroraPostgreSQL.Updates.20180305.231)
+ [Version 2\.3\.0](#AuroraPostgreSQL.Updates.20180305.230)

### Version 2\.3\.5<a name="AuroraPostgreSQL.Updates.20180305.235"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Fixed a bug that could cause DB instance restarts\.

1. Fixed a bug that could cause a crash when the PostgreSQL backend exits while using logical replication\.

1. Fixed a bug that could cause a restart when reads occurred during failovers\.

1. Fixed a bug with the `wal2json` module for logical replication\.

1. Fixed a bug that could result in inconsistent metadata\.

### Version 2\.3\.3<a name="AuroraPostgreSQL.Updates.20180305.233"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Provided a backport fix for the PostgreSQL community security issue CVE\-2019\-10130\.

1. Provided a backport fix for the PostgreSQL community security issue CVE\-2019\-10164\.

1. Fixed a bug whereby data activity streaming could consume excessive CPU time\.

1. Fixed a bug whereby parallel threads scanning a B\-tree index might stop responding following a disk read\.

1. Fixed a bug where use of the `not in` predicate against a common table expression \(CTE\) could return the following error: "ERROR: bad levelsup for CTE"\.

1. Fixed a bug whereby the read node replay process might stop responding while applying a modification to a generalized search tree \(GiST\) index\.

1. Fixed a bug whereby visibility map pages could contain incorrect freeze bits following a failover to a read node\.

1. Optimized log traffic between the write node and read nodes during index maintenance\.

1. Fixed a bug whereby queries on read nodes may crash while performing a B\-tree index scan\. 

1. Fixed a bug whereby a query that has been optimized for redundant inner join removal could crash\.

1. The function `aurora_stat_memctx_usage` now reports the number of instances of a given context name\.

1. Fixed a bug whereby the function `aurora_stat_memctx_usage` reported incorrect results\.

1. Fixed a bug whereby the read node replay process could wait to stop conflicting queries beyond the configured `max_standby_streaming_delay` value\.

1. Additional information is now logged on read nodes when active connections conflict with the relay process\.

1. Provided a backport fix for the PostgreSQL community bug \#15677, where a crash could occur while deleting from a partitioned table\.

### Version 2\.3\.1<a name="AuroraPostgreSQL.Updates.20180305.231"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Fixed multiple bugs related to I/O prefetching that caused engine crashes\.

### Version 2\.3\.0<a name="AuroraPostgreSQL.Updates.20180305.230"></a>

You can find the following improvements in this engine update\.

**New Features**

1. Aurora PostgreSQL now performs I/O prefetching while scanning B\-tree indexes\. This results in significantly improved performance for B\-tree scans over uncached data\.

**Improvements**

1. Fixed a bug whereby read nodes may fail with the error "too many LWLocks taken"\.

1. Addressed numerous issues that caused read nodes to fail to startup while the cluster is under heavy write workload\.

1. Fixed a bug whereby usage of the `aurora_stat_memctx_usage()` function could lead to a crash\.

1. Improved the cache replacement strategy used by table scans to minimize thrashing of the buffer cache\.

## Version 2\.2, Compatible with PostgreSQL 10\.6<a name="AuroraPostgreSQL.Updates.20180305.22"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.6\. For more information about the improvements in release 10\.6, see [PostgreSQL Release 10\.6](https://www.postgresql.org/docs/10/release-10-6.html)\.

**Topics**
+ [Version 2\.2\.1](#AuroraPostgreSQL.Updates.20180305.221)
+ [Version 2\.2\.0](#AuroraPostgreSQL.Updates.20180305.220)

### Version 2\.2\.1<a name="AuroraPostgreSQL.Updates.20180305.221"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Improved stability of logical replication\.

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Version 2\.2\.0<a name="AuroraPostgreSQL.Updates.20180305.220"></a>

You can find the following improvements in this engine update\.

**New features**

1. Added the restricted password management feature\. Restricted password management enables you to restrict who can manage user passwords and password expiration changes by using the parameter `rds.restrict_password_commands` and the role `rds_password`\. For more information, see [Restricting Password Management](AuroraPostgreSQL.Security.md#RestrictPasswordMgmt)\. 

## Version 2\.1, Compatible with PostgreSQL 10\.5<a name="AuroraPostgreSQL.Updates.20180305.21"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.5\. For more information about the improvements in release 10\.5, see [PostgreSQL Release 10\.5](https://www.postgresql.org/docs/current/static/release-10-5.html)\. 

**Topics**
+ [Version 2\.1\.1](#AuroraPostgreSQL.Updates.20180305.211)
+ [Version 2\.1\.0](#AuroraPostgreSQL.Updates.20180305.210)

### Version 2\.1\.1<a name="AuroraPostgreSQL.Updates.20180305.211"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Version 2\.1\.0<a name="AuroraPostgreSQL.Updates.20180305.210"></a>

You can find the following improvements in this engine update\.

**New features**

1. General availability of Aurora Query Plan Management, which enables customers to track and manage any or all query plans used by their applications, to control query optimizer plan selection, and to ensure high and stable applicationperformance\. For more information, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

1. Updated the `libprotobuf` extension to version 1\.3\.0\. This is used by the PostGIS extension\.

1. Updated the `pg_similarity` extension to version 1\.0\.

1. Updated the `log_fdw` extension to version 1\.1\.

1. Updated the `pg_hint_plan` extension to version 1\.3\.1\.

**Improvements**

1. Network traffic between the writer and reader nodes is now compressed to reduce network utilization\. This reduces the chance of read node unavailability due to network saturation\.

1. Implemented a high performance, scalable subsystem for PostgreSQL subtransactions\. This improves the performance of applications which make extensive use of savepoints and `PL/pgSQL` exception handlers\.

1. The `rds_superuser` role can now set the following parameters on a per\-session, database, or role level: 
   + `log_duration`
   + `log_error_verbosity`
   + `log_executor_stats`
   + `log_lock_waits`
   +  `log_min_duration_statement`
   +  `log_min_error_statement`
   +  `log_min_messages`
   +  `log_parser_stats`
   +  `log_planner_stats`
   +  `log_replication_commands`
   +  `log_statement_stats`
   + `log_temp_files `

1. Fixed a bug whereby the SQL command "ALTER FUNCTION \.\.\. OWNER TO \.\.\." might fail with error "improper qualified name \(too many dotted names\)"\.

1. Fixed a bug whereby a crash could occur while committing a transaction with more than two million subtransactions\.

1. Fixed a bug in community PostgreSQL code related to GIN indexes which can cause the Aurora Storage volume to become unavailable\.

1. Fixed a bug whereby an Aurora PostgreSQL replica of an RDS for PostgreSQL instance might fail to start, reporting error: "PANIC: could not locate a valid checkpoint record"\.

1. Fixed a bug whereby passing an invalid parameter to the `aurora_stat_backend_waits` function could cause a crash\.

**Known issues**

1. The `pageinspect` extension is not supported in Aurora PostgreSQL\.

## Version 2\.0, Compatible with PostgreSQL 10\.4<a name="AuroraPostgreSQL.Updates.20180305.20"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 10\.4\. For more information about the improvements in release 10\.4, see [PostgreSQL Release 10\.4](https://www.postgresql.org/docs/10/release-10-4.html)\.

**Topics**
+ [Version 2\.0\.1](#AuroraPostgreSQL.Updates.20180305.201)
+ [Version 2\.0\.0](#AuroraPostgreSQL.Updates.20180305.200)

### Version 2\.0\.1<a name="AuroraPostgreSQL.Updates.20180305.201"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Version 2\.0\.0<a name="AuroraPostgreSQL.Updates.20180305.200"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [Version 1\.3, Compatible with PostgreSQL 9\.6\.9](#AuroraPostgreSQL.Updates.20180305.13)\.

1. The temporary file size limitation is user\-configurable\. You require the **rds\_superuser** role to modify the `temp_file_limit` parameter\.

1. Updated the `GDAL` library, which is used by the `PostGIS` extension\. 

1. Updated the `ip4r` extension to version 2\.1\.1\.

1. Updated the `pg_repack` extension to version 1\.4\.3\. 

1. Updated the `plv8` extension to version 2\.1\.2\.

1. Parallel queries – When you create a new Aurora PostgreSQL version 2\.0 instance, parallel queries are enabled for the `default.postgres10` parameter group\. The parameter `max_parallel_workers_per_gather` is set to 2 by default, but you can modify it to support your specific workload requirements\.

1. Fixed a bug whereby read nodes may crash following a specific type of free space change from the write node\.

## Version 1\.8, Compatible with PostgreSQL 9\.6\.18<a name="AuroraPostgreSQL.Updates.20180305.18"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.18\. For more information about the improvements in release 9\.6\.18, see [ PostgreSQL Release 9\.6\.18](https://www.postgresql.org/docs/9.6/release-9-6-18.html)\.

**Topics**
+ [Version 1\.8\.0](#AuroraPostgreSQL.Updates.20180305.180)

### Version 1\.8\.0<a name="AuroraPostgreSQL.Updates.20180305.180"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\.

**Additional Improvements and Enhancements**

1. Fixed a bug where the Aurora replica crashed when workloads with heavy subtransactions are made on the writer instance\.

1. Fixed a bug where the writer instance crashed due to a memory leak and the depletion of memory used to track active transactions\.

1. Fixed a bug that lead to a crash due to improper initialization when there is no free memory available during PostgreSQL backend startup\.

1. Fixed a crash during a BTree prefetch that occured under certain conditions that depended on the shape and data contained in the index\.

1. Fixed a bug where a `SELECT` query might incorrectly return the error Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

## Version 1\.7, Compatible with PostgreSQL 9\.6\.17<a name="AuroraPostgreSQL.Updates.20180305.17"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.17\. For more information about the improvements in release 9\.6\.17, see [ PostgreSQL Release 9\.6\.17](https://www.postgresql.org/docs/9.6/release-9-6-17.html)\.

**Topics**
+ [Version 1\.7\.3](#AuroraPostgreSQL.Updates.20180305.173)
+ [Version 1\.7\.2](#AuroraPostgreSQL.Updates.20180305.172)
+ [Version 1\.7\.1](#AuroraPostgreSQL.Updates.20180305.171)

### Version 1\.7\.3<a name="AuroraPostgreSQL.Updates.20180305.173"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**
+ None

**High Priority Stability Enhancements**
+ None

**Additional Improvements and Enhancements**

1. Fixed a bug that might cause brief unavailability for heavy subtransaction workloads when multiple reader instances restart or rejoin the cluster\.

### Version 1\.7\.2<a name="AuroraPostgreSQL.Updates.20180305.172"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\. 

**High Priority Stability Enhancements**  
None

**Additional Improvements and Enhancements**

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug where a SELECT query might incorrectly return the error, Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed an issue with the internal metrics collector that could result in erratic CPU spikes on database instances\.

### Version 1\.7\.1<a name="AuroraPostgreSQL.Updates.20180305.171"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**  
None\.

**High Priority Stability Enhancements**

1. Improved performance and availability of read instances when applying DROP TABLE and TRUNCATE TABLE operations\.

1. Fixed a small but continuous memory leak in a diagnostic module that could lead to an out\-of\-memory condition on smaller DB instance types\.

1. Fixed a bug in the `PostGIS` extension which could lead to a database restart\. This has been reported to the PostGIS community as [https://trac.osgeo.org/postgis/ticket/4646](https://trac.osgeo.org/postgis/ticket/4646)\.

1. Fixed a bug where read requests might stop responding due to incorrect error handling in the storage engine\.

1. Fixed a bug that fails for some queries and results in the message ERROR: found xmin xxxxxx from before relfrozenxid yyyyyyy\. This could occur following the promotion of a read instance to a write instance\.

**Additional Improvements and Enhancements**

1. Improved performance for queries that read many rows from storage\.

1. Improved performance and availability of reader DB instances during heavy read workload\.

1. Fixed a bug that resulted in the message ERROR: could not create file "base/xxxxxx/yyyyyyy" as a previous version still exists on disk: Success\. Please contact AWS customer support\. This can occur during object creation after PostgreSQL's 32\-bit object identifier has wrapped around\. 

1. Fixed a bug in the `pg_hint_plan` extension where a multi\-statement query could lead to a crash when `enable_hint_table` is enabled\. This is tracked in the PostgreSQL community as [https://github.com/ossc-db/pg_hint_plan/issues/25](https://github.com/ossc-db/pg_hint_plan/issues/25)\.

1. Changed the following extensions:
   + Updated `orafce` to version 3\.8

## Version 1\.6, Compatible with PostgreSQL 9\.6\.16<a name="AuroraPostgreSQL.Updates.20180305.16"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.16\. For more information about the improvements in release 9\.6\.16, see [PostgreSQL Release 9\.6\.16](https://www.postgresql.org/docs/9.6/release-9-6-16.html)\.

This release contains multiple critical stability enhancements\. Amazon highly recommends upgrading your Aurora PostgreSQL clusters that use older PostgreSQL 9\.6 engines to this release\.

**Topics**
+ [Version 1\.6\.3](#AuroraPostgreSQL.Updates.20180305.163)
+ [Version 1\.6\.2](#AuroraPostgreSQL.Updates.20180305.162)
+ [Version 1\.6\.1](#AuroraPostgreSQL.Updates.20180305.161)
+ [Version 1\.6\.0](#AuroraPostgreSQL.Updates.20180305.160)

### Version 1\.6\.3<a name="AuroraPostgreSQL.Updates.20180305.163"></a>

**Critical Stability Enhancements**
+ None 

**High Priority Stability Enhancements**

1. Fixed a bug where reads from storage might stop responding due to incorrect error handling\.

**Additional Improvements and Enhancements**
+ None

### Version 1\.6\.2<a name="AuroraPostgreSQL.Updates.20180305.162"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. Fixed a bug whereby a reader DB instance might temporarily use stale data\. This could lead to wrong results such as too few or too many rows\. This error is not persisted on storage, and will clear when the database page containing the row has been evicted from cache\. This can happen when the primary DB instance enters a transaction snapshot overflow due to having more than 64 subtransactions in a single transaction\. Applications susceptible to this bug include those that use SQL savepoints or PostgreSQL exception handlers with more than 64 subtransactions in the top transaction\. 

**High Priority Stability Enhancements**

1. Fixed a bug that may cause a reader DB instance to crash causing unavailability while attempting to the join the DB cluster\. This can happen in some cases when the primary DB instance has a transaction snapshot overflow due to a high number of subtransactions\. In this situation the reader DB instance will be unable to join until the snapshot overflow has cleared\.

**Additional Improvements and Enhancements**

1. Fixed a bug that prevented Performance Insights from determining the query ID of a running statement\.

### Version 1\.6\.1<a name="AuroraPostgreSQL.Updates.20180305.161"></a>

You can find the following improvements in this engine update\.

**Critical Stability Enhancements**

1. None

**High Priority Stability Enhancements**

1. Fixed a bug that might cause the database engine to crash causing unavailability\. This occurred if a newly established database connection encountered a resource exhaustion\-related error during initialization after successful authentication\.

**Additional Improvements and Enhancements**

1. Provided general improvements to the stability and availability of Aurora PostgreSQL\.

### Version 1\.6\.0<a name="AuroraPostgreSQL.Updates.20180305.160"></a>

You can find the following new features and improvements in this engine version\.

**New Features**

1. Updates to the `apg_plan_mgmt` extension\. For more information, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)

**Critical Stability Enhancements**

1. Fixed a bug related to creating B\-tree indexes on temporary tables that in rare cases may result in longer recovery time, and impact availability\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance\. In rare cases, this bug causes a log write failure that may result in longer recovery time, and impact availability\. 

1. Fixed a bug related to handling of reads with high I/O latency that in rare cases may result in longer recovery time, and impact availability\.

**High Priority Stability Enhancements**

1. Fixed multiple bugs, which cause Aurora to crash during prefetch operations on Btree indexes\.

1. Enhanced the validation checks performed on data blocks in the buffer cache\. This improves Aurora's detection of inconsistency\.

**Additional Improvements and Enhancements**

1. The query plan management extension `apg_plan_mgmt` has an improved algorithm for managing plan generation for highly partitioned tables\.

1. Reduced startup time on instances with large caches via improvements in the buffer cache recovery algorithm\.

1. Improved the performance of the read\-node\-apply process under high transaction rate workloads by using changes to PostgreSQL `LWLock` prioritization\. These changes prevent starvation of the read\-node\-apply process while the PostgreSQL `ProcArray` is under heavy contention\.

1. Fixed a bug whereby a read node may crash during the replay of a PostgreSQL `SLRU`\-truncate operation\.

1. Fixed a bug where in rare cases, database writes might stall following an error returned by one of the six copies of an Aurora log record\. 

1. Fixed a memory leak on read nodes when cluster cache management is enabled\.

1. Fixed a bug whereby importing an RDS PostgreSQL snapshot might stop responding if the source snapshot contains a large number of unlogged relations\.

1. Fixed a bug related to `hot_standby_feedback` for read nodes whereby the read node may report the wrong transaction id epoch to the write node\. This can cause the write node to ignore the `hot_standby_feedback` and invalidate snapshots on the read node\.

1. Fixed a bug whereby storage errors that occur during `CREATE DATABASE` statements are not properly handled\. The bug would leave the resulting database inaccessible\. The correct behavior is to fail the database creation and return the appropriate error to the user\.

1. Improved handling of PostgreSQL snapshot overflow when a read node attempts to connect to a write node\. Prior to this change, if the write node was in a snapshot overflow state, the read node would be unable to join\. A message would appear in the PostgreSQL log file in the form, `DEBUG: recovery snapshot waiting for non-overflowed snapshot or until oldest active xid on standby is at least xxxxxxx (now yyyyyyy)`\. A snapshot overflow occurs when an individual transaction has created over 64 subtransactions\. 

1. Fixed a bug related to common table expressions whereby an error is incorrectly raised when a NOT IN class exists in a CTE\. The error is `CTE with NOT IN fails with ERROR: could not find CTE CTE-Name`\.

1. Fixed a bug related to an incorrect `last_error_timestamp` value in the `aurora_replica_status` table\.

1. Fixed a bug to avoid populating shared buffers with blocks belonging to temporary objects\. These blocks correctly reside in PostgreSQL backend local buffers\. 

1. Fixed a bug where in rare cases Aurora may exhibit 100% CPU utilization while acting as a replica of an RDS PostgreSQL instance even when the replication stream is idle\. 

1. Backported a change from PostgreSQL 11 which improves the cleanup of orphaned temporary tables\. Without this change, it is possible that in rare cases orphaned temporary tables can to lead to transaction ID wraparound\. For more information, see this [PostgreSQL community commit](https://github.com/postgres/postgres/commit/246a6c8f7b237cc1943efbbb8a7417da9288f5c4)\. 

1. Fixed a bug where a Writer instance may accept replication registration requests from Reader instances while having an uninitialized startup process\.

1. Changed the following extensions:
   + Updated `pg_hint_plan` to version 1\.2\.5\.

## Version 1\.5, Compatible with PostgreSQL 9\.6\.12<a name="AuroraPostgreSQL.Updates.20180305.15"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.12\. For more information about the improvements in release 9\.6\.12, see [PostgreSQL Release 9\.6\.12](https://www.postgresql.org/docs/9.6/release-9-6-12.html)\.

**Topics**
+ [Version 1\.5\.3](#AuroraPostgreSQL.Updates.20180305.153)
+ [Version 1\.5\.2](#AuroraPostgreSQL.Updates.20180305.152)
+ [Version 1\.5\.1](#AuroraPostgreSQL.Updates.20180305.151)
+ [Version 1\.5\.0](#AuroraPostgreSQL.Updates.20180305.150)

### Version 1\.5\.3<a name="AuroraPostgreSQL.Updates.20180305.153"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Fixed a bug that could cause DB instance restarts\.

1. Fixed a bug that could cause a restart when reads occurred during failovers\. 

1. Fixed a bug that could result in inconsistent metadata\.

### Version 1\.5\.2<a name="AuroraPostgreSQL.Updates.20180305.152"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Provided a backport fix for the PostgreSQL community security issue CVE\-2019\-10130\.

1. Fixed a bug whereby the read node replay process might stop responding while applying a modification to a generalized search tree \(GiST\) index\.

1. Fixed a bug whereby visibility map pages may contain incorrect freeze bits following a failover to a read node\.

1. Fixed a bug whereby the error "relation `relation-name` does not exist" is incorrectly reported\.

1. Optimized log traffic between the write node and read nodes during index maintenance\.

1. Fixed a bug whereby queries on read nodes may crash while performing a B\-tree index scan\. 

1. The function `aurora_stat_memctx_usage` now reports the number of instances of a given context name\.

1. Fixed a bug whereby the function `aurora_stat_memctx_usage` reported incorrect results\.

1. Fixed a bug whereby the read node replay process may wait to stop conflicting queries beyond the configured `max_standby_streaming_delay`\.

1. Additional information is now logged on read nodes when active connections conflict with the relay process\.

### Version 1\.5\.1<a name="AuroraPostgreSQL.Updates.20180305.151"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. Fixed multiple bugs related to I/O prefetching, which caused engine crashes\.

### Version 1\.5\.0<a name="AuroraPostgreSQL.Updates.20180305.150"></a>

You can find the following improvements in this engine update\.

**New features**

1. Aurora PostgreSQL now performs I/O prefetching while scanning B\-tree indexes\. This results in significantly improved performance for B\-tree scans over uncached data\.

**Improvements**

1. Addressed numerous issues that caused read nodes to fail to startup while the cluster is under heavy write workload\.

1. Fixed a bug whereby usage of the `aurora_stat_memctx_usage()` function could lead to a crash\.

1. Improved the cache replacement strategy used by table scans to minimize thrashing of the buffer cache\.

## Version 1\.4, Compatible with PostgreSQL 9\.6\.11<a name="AuroraPostgreSQL.Updates.20180305.14"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.11\. For more information about the improvements in release 9\.6\.11, see [PostgreSQL Release 9\.6\.11](https://www.postgresql.org/docs/9.6/release-9-6-11.html)\. 

You can find the following improvements in this engine update\.

**New features**

1. Support is added for the `pg_similarity` extension version 1\.0\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [Version 1\.3, Compatible with PostgreSQL 9\.6\.9](#AuroraPostgreSQL.Updates.20180305.13)\.

1. Network traffic between the writer and reader nodes is now compressed to reduce network utilization\. This reduces the chance of read node unavailability due to network saturation\.

1. Performance of subtransactions has improved under high concurrency workloads\.

1. An update for the `pg_hint_plan` extension to version 1\.2\.3\.

1. Fixed an issue where on a busy system, a commit with millions of subtransactions \(and sometimes with commit timestamps enabled\) can cause Aurora to crash\.

1. Fixed an issue where an `INSERT` statement with `VALUES` could fail with the message "Attempting to read past EOF of relation"\.

1. An upgrade of the `apg_plan_mgmt` extension to version 1\.0\.1\. For details, see [The Aurora PostgreSQL apg\_plan\_mgmt Extension Version 1\.0\.1](AuroraPostgreSQL.Extensions.md#AuroraPostgreSQL.Extensions.apg_plan_mgmt.101)\.

   The `apg_plan_mgmt` extension is used with query plan management\. For more about how to install, upgrade, and use the `apg_plan_mgmt` extension, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

## Version 1\.3, Compatible with PostgreSQL 9\.6\.9<a name="AuroraPostgreSQL.Updates.20180305.13"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.9\. For more information about the improvements in version 9\.6\.9, see [PostgreSQL Release 9\.6\.9](https://www.postgresql.org/docs/9.6/release-9-6-9.html)\.

**Topics**
+ [Version 1\.3\.2](#AuroraPostgreSQL.Updates.20180305.132)
+ [Version 1\.3\.0](#AuroraPostgreSQL.Updates.20180305.130)

### Version 1\.3\.2<a name="AuroraPostgreSQL.Updates.20180305.132"></a>

You can find the following improvements in this engine update\.

**New features**

1. Added the `ProcArrayGroupUpdate` wait event\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Version 1\.3\.0<a name="AuroraPostgreSQL.Updates.20180305.130"></a>

You can find the following improvements in this engine update\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [Version 1\.2, Compatible with PostgreSQL 9\.6\.8](#AuroraPostgreSQL.Updates.20180305.12)\.

1. Updated the GDAL library, which is used by the `PostGIS` extension\. 

1. Updated the following PostgreSQL extensions: 
   + `ip4r` updated to version 2\.1\.1\.
   + `pgaudit` updated to version 1\.1\.1\. 
   + `pg_repack` updated to version 1\.4\.3\. 
   + `plv8` updated to version 2\.1\.2\.

1. Fixed an issue in the monitoring system that could incorrectly cause a failover when local disk usage is high\.

1. Fixed a bug whereby Aurora PostgreSQL can repeatedly crash, reporting:

   `PANIC: new_record_total_len (8201) must be less than BLCKSZ (8192), rmid (6), info (32)`

1. Fixed a bug whereby an Aurora PostgreSQL read node might be unable to rejoin a cluster due to recovery of a large buffer cache\. This issue is unlikely to occur on instances other than **r4\.16xlarge\.**

1. Fixed a bug whereby inserting into an empty GIN index leaf page imported from pre\-9\.4 engine versions can cause the Aurora storage volume to become unavailable\.

1. Fixed a bug whereby, in rare circumstances, a crash during transaction commit could result in the loss of `CommitTs` data for the committing transaction\. The actual durability of the transaction was not impacted by this bug\.

1. Fixed a bug in the `PostGIS` extension whereby `PostGIS` can crash in the function `gserialized_gist_picksplit_2d()`\.

1. Improved the stability of read\-only nodes during heavy write traffic on instances smaller than **r4\.8xl**\. This specifically addresses a situation where the network bandwidth between the writer and the reader is constrained\.

1. Fixed a bug whereby an Aurora PostgreSQL instance acting as a replication target of an RDS for PostgreSQL instance crashed with the following error:

   `FATAL: could not open file "base/16411/680897_vm": No such file or directory" during "xlog redo at 782/3122D540 for Storage/TRUNCATE"`

1. Fixed a memory leak on read\-only nodes whereby the heap size for the "aurora wal replay process" will continue to grow\. This is observable via Enhanced Monitoring\.

1. Fixed a bug whereby Aurora PostgreSQL can fail to start, with the following message reported in the PostgreSQL log:

   `FATAL: Storage initialization failed.`

1. Fixed a performance limitation on heavy write workloads that caused waits on the `LWLock:buffer_content` and `IO:ControlFileSyncUpdate` events\.

1. Fixed a bug whereby read nodes could crash following a specific type of free space change from the write node\.

## Version 1\.2, Compatible with PostgreSQL 9\.6\.8<a name="AuroraPostgreSQL.Updates.20180305.12"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.8\. For more information about the improvements in version 9\.6\.8, see [PostgreSQL Release 9\.6\.8](https://www.postgresql.org/docs/9.6/release-9-6-8.html)\. 

**Topics**
+ [Version 1\.2\.2](#AuroraPostgreSQL.Updates.20180305.122)
+ [Version 1\.2\.0](#AuroraPostgreSQL.Updates.20180305.120)

### Version 1\.2\.2<a name="AuroraPostgreSQL.Updates.20180305.122"></a>

You can find the following improvements in this engine update\.

**New features**

1. Added the `ProcArrayGroupUpdate` wait event\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported would be of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Version 1\.2\.0<a name="AuroraPostgreSQL.Updates.20180305.120"></a>

You can find the following improvements in this engine update\.

**New features**

1. Introduced the `aurora_stat_memctx_usage()` function\. This function reports internal memory context usage for each PostgreSQL backend\. You can use this function to help determine why certain backends are consuming large amounts of memory\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [Version 1\.1, Compatible with PostgreSQL 9\.6\.6 is deprecated](#AuroraPostgreSQL.Updates.20180305.11)\.

1. Updates the following PostgreSQL extensions:
   + `pg_hint_plan` updated to version 1\.2\.2
   + `plv8` updated to version 2\.1\.0

1. Improves efficiency of traffic between writer and reader nodes\.

1. Improves connection establishment performance\.

1. Improve the diagnostic data provided in the PostgreSQL error log when an out\-of\-memory error is encountered\.

1. Multiple fixes to improve the reliability and performance of snapshot import from Amazon RDS for PostgreSQL to Aurora with PostgreSQL compatibility\.

1. Multiple fixes to improve the reliability and performance of Aurora PostgreSQL read nodes\.

1. Fixes a bug in which an otherwise idle instance can generate unnecessary read traffic on an Aurora storage volume\.

1. Fixes a bug in which duplicate sequence values can be encountered during insert\. The problem only occurs when migrating a snapshot from RDS for PostgreSQL to Aurora PostgreSQL\. The fix prevents the problem from being introduced when performing the migration\. Instances migrated before this release can still encounter duplicate key errors\.

1. Fixes a bug in which an RDS for PostgreSQL instance migrated to Aurora PostgreSQL using replication can run out of memory doing insert/update of GIST indexes, or cause other issues with GIST indexes\.

1. Fixes a bug in which vacuum can fail to update the corresponding `pg_database.datfrozenxid` value for a database\.

1. Fixes a bug in which a crash while creating a new MultiXact \(contended row level lock\) can cause Aurora PostgreSQL to stop responding indefinitely on the first access to the same relation after the engine restarts\.

1. Fixes a bug in which a PostgreSQL backend can't be terminated or canceled while invoking an `fdw` call\.

1. Fixes a bug in which one vCPU is fully utilized at all times by the Aurora storage daemon\. This issue is especially noticeable on smaller instance classes, such as r4\.large, where it can lead to 25–50 percent CPU usage when idle\.

1. Fixes a bug in which an Aurora PostgreSQL writer node can fail over spuriously\.

1. Fixes a bug in which, in a rare scenario, an Aurora PostgreSQL read node can report: 

   "FATAL: lock buffer\_io is not held"

1. Fixes a bug in which stale relcache entries can halt vacuuming of relations and push the system close to transaction ID wraparound\. The fix is a port of a PostgreSQL community patch scheduled to be released in a future minor version\.

1. Fixes a bug in which a failure while extending a relation can cause Aurora to crash while scanning the partially extended relation\.

## Version 1\.1, Compatible with PostgreSQL 9\.6\.6 is deprecated<a name="AuroraPostgreSQL.Updates.20180305.11"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.6\. For more information about the improvements in version 9\.6\.6 see, [PostgreSQL Release 9\.6\.6](https://www.postgresql.org/docs/9.6/static/release-9-6-6.html)\.

You can find the following improvements in this engine update:

**New features**

1. Introduced the `aurora_stat_utils` extension\. This extension includes two functions:
   + aurora\_wait\_report\(\) function for wait event monitoring
   + aurora\_log\_report\(\) for log record write monitoring

1. Added support for the following extensions:
   + orafce 3\.6\.1
   + pgrouting 2\.4\.2
   + postgresql\-hll 2\.10\.2
   + prefix 1\.2\.6

**Improvements**

1. This release contains all fixes, features, and improvements present in [Version 1\.0\.11](#AuroraPostgreSQL.Updates.20180305.1011)

1. Updates for the following PostgreSQL extensions:
   + postgis extension updated to version 2\.3\.4
   + geos library updated to version 3\.6\.2
   + pg\_repack updated to version 1\.4\.2

1. Access to the pg\_statistic relation enabled\.

1. Disabled the 'effective\_io\_concurrency' guc parameter, as it does not apply to Aurora storage\.

1. Changed the 'hot\_standby\_feedback' guc parameter to not\-modifiable and set the value to '1'\.

1. Improved heap page read performance during a vacuum operation\.

1. Improved performance of snapshot conflict resolution on read nodes\.

1. Improved performance of transaction snapshot acquisition on read nodes\.

1. Improved write performance for GIN meta page updates\.

1. Improved buffer cache recovery performance during startup\.

1. Fixes a bug that caused a database engine crash at startup while recovering prepared transactions\.

1. Fixes a bug that could result in the inability to start a read node when there are a large number of prepared transactions\.

1. Fixes a bug that could cause a read node to report:

   ERROR: could not access status of transaction 6080077 

   DETAIL:\* \*Could not open file "pg\_subtrans/005C": No such file or directory\.

1. Fixes a bug that could cause the error below when replicating from RDS PostgreSQL to Aurora PostgreSQL:

   FATAL: lock buffer\_content is not held

   CONTEXT: xlog redo at 46E/F1330870 for Storage/TRUNCATE: base/13322/8058750 to 0 blocks flags 7

1. Fixes a bug that could cause Aurora PostgreSQL to stop responding while replaying a multixact WAL record when replicating from RDS PostgreSQL to Aurora PostgreSQL\.

1. Multiple improvements to the reliability of importing snapshots from RDS PostgreSQL to Aurora PostgreSQL\.

## Version 1\.0, Compatible with PostgreSQL 9\.6\.3 is deprecated<a name="AuroraPostgreSQL.Updates.20180305.10"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.3\. For more information about the improvements in version 9\.6\.3 see, [PostgreSQL Release 9\.6\.3](https://www.postgresql.org/docs/9.6/release-9-6-3.html)\.

This version includes the following patch versions:

**Topics**
+ [Version 1\.0\.11](#AuroraPostgreSQL.Updates.20180305.1011)
+ [Version 1\.0\.10](#AuroraPostgreSQL.Updates.20180305.1010)
+ [Version 1\.0\.9](#AuroraPostgreSQL.Updates.20180305.109)
+ [Version 1\.0\.8](#AuroraPostgreSQL.Updates.20180305.108)
+ [Version 1\.0\.7](#AuroraPostgreSQL.Updates.20180305.107)

### Version 1\.0\.11<a name="AuroraPostgreSQL.Updates.20180305.1011"></a>

You can find the following improvements in this engine update:

1. Fixes an issue with parallel query processing that can lead to incorrect results\.

1. Fixes an issue with visibility map handling during replication from Amazon RDS for PostgreSQL that can cause the Aurora storage volume to become unavailable\. 

1. Corrects the pg\-repack extension\.

1. Implements improvements to maintain fresh nodes\.

1. Fixes issues that can lead to an engine crash\.

### Version 1\.0\.10<a name="AuroraPostgreSQL.Updates.20180305.1010"></a>

This update includes a new feature\. You can now replicate an Amazon RDS PostgreSQL DB instance to Aurora PostgreSQL\. For more information, see [Replication with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Replication.md)\.

You can find the following improvements in this engine update:

1. Adds error logging when a cache exists and a parameter change results in a mismatched buffer cache, storage format, or size\.

1. Fixes an issue that causes an engine reboot if there is an incompatible parameter value for huge pages\. 

1. Improves handling of multiple truncate table statements during a replay of a write ahead log \(WAL\) on a read node\.

1. Reduces static memory overhead to reduce out\-of\-memory errors\.

1. Fixes an issue that can lead to out\-of\-memory errors while performing an insert with a GiST index\.

1. Improves snapshot import from RDS PostgreSQL, removing the requirement that a vacuum be performed on uninitialized pages\.

1. Fixes an issue that causes prepared transactions to return to the previous state following an engine crash\.

1. Implements improvements to prevent read nodes from becoming stale\.

1. Implements improvements to reduce downtime with an engine restart\.

1. Fixes issues that can cause an engine crash\.

### Version 1\.0\.9<a name="AuroraPostgreSQL.Updates.20180305.109"></a>

In this engine update, we fix an issue that can cause the Aurora storage volume to become unavailable when importing a snapshot from RDS PostgreSQL that contained uninitialized pages\.

### Version 1\.0\.8<a name="AuroraPostgreSQL.Updates.20180305.108"></a>

You can find the following improvements in this engine update:

1. Fixes an issue that prevented the engine from starting if the `shared_preload_libraries` instance parameter contained `pg_hint_plan`\. 

1. Fixes the error "Attempt to fetch heap block XXX is beyond end of heap \(YYY blocks\)," which can occur during parallel scans\. 

1. Improves the effectiveness of prefetching on reads for a vacuum\.

1. Fixes issues with snapshot import from RDS PostgreSQL, which can fail if there are incompatible pg\_internal\.init files in the source snapshot\.

1. Fixes an issue that can cause a read node to crash with the message "aurora wal replay process \(PID XXX\) was terminated by signal 11: Segmentation fault"\. This issue occurs when the reader applied a visibility map change for an uncached visibility map page\.

### Version 1\.0\.7<a name="AuroraPostgreSQL.Updates.20180305.107"></a>

This is the first generally available release of Amazon Aurora with PostgreSQL compatibility\.