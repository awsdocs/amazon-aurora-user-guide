# Engine versions for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.20180305"></a>

Following, you can find information about supported versions of the Aurora PostgreSQL\-Compatible Edition database engine\. An Aurora database has two version numbers; the Aurora version number and the database engine version number\. To determine the version numbers of your Aurora PostgreSQL database, see [Identifying your version of Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.md#AuroraPostgreSQL.Updates.Versions)\. 

To determine which PostgreSQL engine versions are available in an AWS Region, use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\. For example:

```
aws rds describe-db-engine-versions --engine aurora-postgresql --query '*[].[EngineVersion]' --output text --region your-AWS-Region
```

For a list of AWS Regions, see [Aurora PostgreSQL Region availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability.PostgreSQL)\.

**Topics**
+ [PostgreSQL 12\.4, Aurora PostgreSQL release 4\.0](#AuroraPostgreSQL.Updates.20180305.40)
+ [PostgreSQL 11\.9, Aurora PostgreSQL release 3\.4](#AuroraPostgreSQL.Updates.20180305.34)
+ [PostgreSQL 11\.8, Aurora PostgreSQL release 3\.3](#AuroraPostgreSQL.Updates.20180305.33)
+ [PostgreSQL 11\.7, Aurora PostgreSQL release 3\.2](#AuroraPostgreSQL.Updates.20180305.32)
+ [PostgreSQL 11\.6, Aurora PostgreSQL release 3\.1](#AuroraPostgreSQL.Updates.20180305.31)
+ [PostgreSQL 11\.4, Aurora PostgreSQL release 3\.0 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.30)
+ [PostgreSQL 10\.14, Aurora PostgreSQL release 2\.7](#AuroraPostgreSQL.Updates.20180305.27)
+ [PostgreSQL 10\.13, Aurora PostgreSQL release 2\.6](#AuroraPostgreSQL.Updates.20180305.26)
+ [PostgreSQL 10\.12, Aurora PostgreSQL release 2\.5](#AuroraPostgreSQL.Updates.20180305.25)
+ [PostgreSQL 10\.11, Aurora PostgreSQL release 2\.4](#AuroraPostgreSQL.Updates.20180305.24)
+ [PostgreSQL 10\.7, Aurora PostgreSQL release 2\.3 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.23)
+ [PostgreSQL 10\.6, Aurora PostgreSQL release 2\.2 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.22)
+ [PostgreSQL 10\.5, Aurora PostgreSQL release 2\.1 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.21)
+ [PostgreSQL 10\.4, Aurora PostgreSQL release 2\.0 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.20)
+ [PostgreSQL 9\.6\.19, Aurora PostgreSQL release 1\.9](#AuroraPostgreSQL.Updates.20180305.19)
+ [PostgreSQL 9\.6\.18, Aurora PostgreSQL release 1\.8](#AuroraPostgreSQL.Updates.20180305.18)
+ [PostgreSQL 9\.6\.17, Aurora PostgreSQL release 1\.7](#AuroraPostgreSQL.Updates.20180305.17)
+ [PostgreSQL 9\.6\.16, Aurora PostgreSQL release 1\.6](#AuroraPostgreSQL.Updates.20180305.16)
+ [PostgreSQL 9\.6\.12, Aurora PostgreSQL release 1\.5 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.15)
+ [PostgreSQL 9\.6\.11, Aurora PostgreSQL release 1\.4 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.14)
+ [PostgreSQL 9\.6\.9, Aurora PostgreSQL release 1\.3 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.13)
+ [PostgreSQL 9\.6\.8, Aurora PostgreSQL release 1\.2 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.12)
+ [PostgreSQL 9\.6\.6 Aurora PostgreSQL release 1\.1 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.11)
+ [PostgreSQL 9\.6\.3, Aurora PostgreSQL release 1\.0 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.10)

For information on extensions and modules, see [Extensions and modules for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Extensions.md)\.

The following Aurora PostgreSQL versions are supported\. 

## PostgreSQL 12\.4, Aurora PostgreSQL release 4\.0<a name="AuroraPostgreSQL.Updates.20180305.40"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 12\.4\. For more information about the improvements in PostgreSQL 12\.4, see [PostgreSQL release 12\.4](https://www.postgresql.org/docs/12/release-12-4.html)\.

**Topics**
+ [Aurora PostgreSQL release 4\.0\.1](#AuroraPostgreSQL.Updates.20180305.401)
+ [Aurora PostgreSQL release 4\.0\.0](#AuroraPostgreSQL.Updates.20180305.400)

### Aurora PostgreSQL release 4\.0\.1<a name="AuroraPostgreSQL.Updates.20180305.401"></a>

**New features**

1. This release adds support for the [ Graviton2 db\.r6g instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora) to the PostgreSQL engine version 12\.4\.

**Critical stability enhancements**

1. Fixed a bug that caused a read replica to unsuccessfully restart repeatedly in rare cases\.

1. Fixed a bug where a cluster became unavailable when attempting to create more than 16 read replicas or Aurora global database secondary AWS Regions\. The cluster became available again when the new read replica or secondary AWS Region was removed\.

**Additional improvements and enhancements**

1. Fixed a bug that when under heavy load, snapshot import, COPY import, or Amazon S3 import stopped responding in rare cases\. 

1. Fixed a bug where a read replica might not join the cluster when the writer was very busy with a write\-intensive workload\.

1. Fixed a bug where a cluster could be unavailable briefly when a high\-volume S3 import was running\. 

1. Fixed a bug that caused a cluster to take several minutes to restart if a logical replication stream was terminated while handling many complex transactions\.

1. Fixed the Just\-in\-Time \(JIT\) compilation, which was incorrectly enabled by default in Aurora PostgreSQL release 4\.0\.0\. 

1. Disallowed the use of both AWS Identity and Access Management \(IAM\) and Kerberos authentication for the same user\.

### Aurora PostgreSQL release 4\.0\.0<a name="AuroraPostgreSQL.Updates.20180305.400"></a>

**New features**

1. This version supports a major version upgrade from [PostgreSQL 11\.7, Aurora PostgreSQL release 3\.2 ](#AuroraPostgreSQL.Updates.20180305.32) and later versions\.

**Additional improvements and enhancements**

1. Contains several improvements that were announced for PostgreSQL releases [12\.0](https://www.postgresql.org/docs/12/release-12.html), [12\.1](https://www.postgresql.org/docs/12/release-12-1.html), [12\.2](https://www.postgresql.org/docs/12/release-12-2.html), [12\.3](https://www.postgresql.org/docs/12/release-12-3.html), and [12\.4](https://www.postgresql.org/docs/12/release-12-4.html)\.

1. Contains all fixes, features, and improvements present in [PostgreSQL 11\.9, Aurora PostgreSQL release 3\.4 ](#AuroraPostgreSQL.Updates.20180305.34)\.

1. Backported fixes for the following PostgreSQL community security issues:
   + [CVE\-2020\-25694](https://nvd.nist.gov/vuln/detail/CVE-2020-25694)
   + [CVE\-2020\-25695](https://nvd.nist.gov/vuln/detail/CVE-2020-25695)
   + [CVE\-2020\-25696](https://nvd.nist.gov/vuln/detail/CVE-2020-25696)

1. Updated the following extensions:
   + `address_standardizer` to version 3\.0\.2 
   + `address_standardizer_data_us` to version 3\.0\.2 
   + `amcheck` to version 1\.2 
   + `citext` to version 1\.6 
   + `hll` to version 2\.14 
   + `hstore` to version 1\.6 
   + `ip4r` to version 2\.4 
   + `pg_repack` to version 1\.4\.5 
   + `pg_stat_statements` to version 1\.7 
   + `pgaudit` to version 1\.4 
   + `pglogical` to version 2\.3\.2 
   + `pgrouting` to version 3\.0\.3 
   + `plv8` to version 2\.3\.14 
   + `postGIS` to version 3\.0\.2 
   + `postgis_tiger_geocoder` to version 3\.0\.2 
   + `postgis_topology` to version 3\.0\.2

## PostgreSQL 11\.9, Aurora PostgreSQL release 3\.4<a name="AuroraPostgreSQL.Updates.20180305.34"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 11\.9\. For more information about the improvements in PostgreSQL 11\.9, see [PostgreSQL release 11\.9](https://www.postgresql.org/docs/11/release-11-9.html)\.

**Topics**
+ [Aurora PostgreSQL release 3\.4\.1](#AuroraPostgreSQL.Updates.20180305.341)
+ [Aurora PostgreSQL release 3\.4\.0](#AuroraPostgreSQL.Updates.20180305.340)

### Aurora PostgreSQL release 3\.4\.1<a name="AuroraPostgreSQL.Updates.20180305.341"></a>

**Critical stability enhancements**

1. Fixed a bug that caused a read replica to unsuccessfully restart repeatedly in rare cases\.

1. Fixed a bug where a cluster became unavailable when attempting to create more than 16 read replicas or Aurora global database secondary AWS Regions\. The cluster became available again when the new read replica or secondary AWS Region was removed\.

**Additional improvements and enhancements**

1. Fixed a bug that when under heavy load, snapshot import, COPY import, or S3 import stopped responding in rare cases\. 

1. Fixed a bug where a read replica might not join the cluster when the writer was very busy with a write\-intensive workload\.

1. Fixed a bug where a cluster could be unavailable briefly when a high\-volume S3 import was running\. 

1. Fixed a bug that caused a cluster to take several minutes to restart if a logical replication stream was terminated while handling many complex transactions\.

1. Disallowed the use of both IAM and Kerberos authentication for the same user\.

### Aurora PostgreSQL release 3\.4\.0<a name="AuroraPostgreSQL.Updates.20180305.340"></a>

**New features**

1. Aurora PostgreSQL now supports invocation of AWS Lambda functions\. This includes the new `aws_lambda` extension\. For more information, see [Invoking an AWS Lambda function from an Aurora PostgreSQL DB cluster ](AuroraPostgreSQL-Lambda.md)\. 

1. The db\.r6g instance classes are now available in preview for Aurora\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\.

**Critical stability enhancements**
+  None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that could result in the error message ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

1. Fixed a bug where in some cases, DB clusters that have logical replication enabled did not remove truncated WAL segment files from storage\. This resulted in volume size growth\.

1. Backported fixes for the following PostgreSQL community security issues:
   + [CVE\-2020\-25694](https://nvd.nist.gov/vuln/detail/CVE-2020-25694)
   + [CVE\-2020\-25695](https://nvd.nist.gov/vuln/detail/CVE-2020-25695)
   + [CVE\-2020\-25696](https://nvd.nist.gov/vuln/detail/CVE-2020-25696)

1. Fixed a bug in the `pg_stat_statements` extension that caused excessive CPU consumption\.

**Additional improvements and enhancements**

1. You can now use `ps_replication_slot_advance` to advance a logical replication slot for the roles `rds_replication` and `rds_superuser`\.

1. Improved the asynchronous mode performance of database activity streams\.

1. Reduced the delay when publishing to CloudWatch the `rpo_lag_in_msec` metric for Aurora global database clusters\.

1. Aurora PostgreSQL no longer falls behind on a read node when the backend is blocked writing to the database client\.

1. Fixed a bug that in rare cases caused a brief period of unavailability on a read replica when the storage volume grew\.

1. Fixed a bug when creating a database that could return the following: ERROR: could not create directory on local disk

1. Updated data grid files to fix errors or incorrect transformation results from the `ST_Transform` method of the `PostGIS` extension\.

1. Fixed a bug where in some cases replaying `XLOG_BTREE_REUSE_PAGE` records on Aurora reader instances caused unnecessary replay lag\.

1. Fixed a small memory leak in a b\-tree index that could lead to an out of memory condition\.

1. Fixed a bug in the `GiST` index that could result in an out of memory condition after promoting an Aurora read replica\.

1. Fixed an S3 import bug that reported ERROR: HTTP 403\. Permission denied when importing data from a file inside an S3 subfolder\.

1. Fixed a bug in the `aws_s3` extension for pre\-signed URL handling that could result in the error message S3 bucket names with a period \(\.\) are not supported\.

1. Fixed a bug in the `aws_s3` extension where an import might be blocked indefinitely if an exclusive lock was taken on the relation prior to beginning the operation\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance that uses `GiST` indexes\. In rare cases, this bug caused a brief period of unavailability after promoting the Aurora cluster\.

1. Fixed a bug in database activity streams where customers were not notified of the end of an outage\.

1. Updated the `pg_audit` extension to version 1\.3\.1\.

## PostgreSQL 11\.8, Aurora PostgreSQL release 3\.3<a name="AuroraPostgreSQL.Updates.20180305.33"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 11\.8\. For more information about the improvements in PostgreSQL 11\.8, see [PostgreSQL release 11\.8](https://www.postgresql.org/docs/11/release-11-8.html)\.

**Topics**
+ [Aurora PostgreSQL release 3\.3\.2](#AuroraPostgreSQL.Updates.20180305.332)
+ [Aurora PostgreSQL release 3\.3\.1](#AuroraPostgreSQL.Updates.20180305.331)
+ [Aurora PostgreSQL release 3\.3\.0](#AuroraPostgreSQL.Updates.20180305.330)

### Aurora PostgreSQL release 3\.3\.2<a name="AuroraPostgreSQL.Updates.20180305.332"></a>

**Critical stability enhancements**

1. None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that could result in the error message ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

1. Fixed a bug where in some cases, DB clusters that have logical replication enabled did not remove truncated WAL segment files from storage\. This resulted in volume size growth\.

1. Fixed an issue with creating a global database cluster in a secondary region\.

1. Backported fixes for the following PostgreSQL community security issues:
   + [CVE\-2020\-25694](https://nvd.nist.gov/vuln/detail/CVE-2020-25694)
   + [CVE\-2020\-25695](https://nvd.nist.gov/vuln/detail/CVE-2020-25695)
   + [CVE\-2020\-25696](https://nvd.nist.gov/vuln/detail/CVE-2020-25696)

1. Fixed a bug in the `pg_stat_statements` extension that caused excessive CPU consumption\.

**Additional improvements and enhancements**

1. Aurora PostgreSQL no longer falls behind on a read node when the backend is blocked writing to the database client\.

1. Reduced the delay when publishing to CloudWatch the `rpo_lag_in_msec` metric for Aurora global database clusters\.

1. Fixed a bug where a `DROP DATABASE` statement didn't remove any relation files\.

1. Fixed a bug where in some cases replaying `XLOG_BTREE_REUSE_PAGE` records on Aurora reader instances caused unnecessary replay lag\.

1. Fixed a small memory leak in a b\-tree index that could lead to an out of memory condition\.

1. Fixed a bug in the `aurora_replica_status()` function where the `server_id` field was sometimes truncated\.

1. Fixed a bug where a log record was incorrectly processed causing the Aurora replica to crash\.

1. Fixed an S3 import bug that reported ERROR: HTTP 403\. Permission denied when importing data from a file inside an S3 subfolder\.

1. You can now use `ps_replication_slot_advance` to advance a logical replication slot for the roles `rds_replication` and `rds_superuser`\.

1. Improved performance of the asynchronous mode for database activity streams\.

1. Fixed a bug in the `aws_s3` extension that could result in the error message S3 bucket names with a period \(\.\) are not supported\.

1. Fixed a race condition that caused valid imports to intermittently fail\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance that uses GiST indexes\. In rare cases, this bug caused a brief period of unavailability after promoting the Aurora DB cluster\.

1. Fixed a bug in the `aws_s3` extension where an import may be blocked indefinitely if an exclusive lock was taken on the relation prior to beginning the operation\.

### Aurora PostgreSQL release 3\.3\.1<a name="AuroraPostgreSQL.Updates.20180305.331"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Aurora PostgreSQL release 3\.3\.0<a name="AuroraPostgreSQL.Updates.20180305.330"></a>

**New features**

1. Added support for the RDKit extension version 3\.8\. 

   The RDKit extension provides modeling functions for cheminformatics\. Cheminformatics is storing, indexing, searching, retrieving, and applying information about chemical compounds\. For example, with the RDKit extension you can construct models of molecules, search for molecular structures, and read or create molecules in various notations\. You can also perform research on data loaded from the [ChEMBL website](https://www.ebi.ac.uk/chembl/) or a SMILES file\. The Simplified Molecular Input Line Entry System \(SMILES\) is a typographical notation for representing molecules and reactions\. For more information, see [The RDKit database cartridge](https://rdkit.org/docs/Cartridge.html) in the RDKit documentation\. 

1. Added support for a minimum TLS version

   Support for a minimum Transport Layer Security \(TLS\) version is back ported from PostgreSQL 12\. It allows the Aurora PostgreSQL server to constrain the TLS protocols with which a client is allowed to connect via two new PostgreSQL parameters\. These parameters include [ssl\_min\_protocol\_version](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-SSL-MIN-PROTOCOL-VERSION)and [ssl\_max\_protocol\_version](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-SSL-MAX-PROTOCOL-VERSION)\. For example, to limit client connections to the Aurora PostgreSQL server to at least TLS 1\.2 protocol version, set the `ssl_min_protocol_version` to `TLSv1.2`\.

1. Added support for the `pglogical` extension version 2\.2\.2\.

   The `pglogical` extension is a logical streaming replication system that provides additional features beyond what's available in PostgreSQL native logical replication\. Features include conflict handling, row filtering, DDL/sequence replication and delayed apply\. You can use the `pglogical` extension to set up replication between Aurora PostgreSQL clusters, between RDS PostgreSQL and Aurora PostgreSQL, and with PostgreSQL databases running outside of RDS\.

1.  Aurora dynamically resizes your cluster storage space\. With dynamic resizing, the storage space for your Aurora DB cluster automatically decreases when you remove data from the DB cluster\. For more information, see [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling)\. 
**Note**  
 The dynamic resizing feature is being deployed in phases to the AWS Regions where Aurora is available\. Depending on the Region where your cluster is, this feature might not be available yet\. For more information, see [the What's New announcement](https://aws.amazon.com/about-aws/whats-new/2020/10/amazon-aurora-enables-dynamic-resizing-database-storage-space/)\. 

**Critical stability enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\.

**High priority stability enhancements**

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary AWS Region\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 11\.8\.

**Additional improvements and enhancements**

1. Fixed a bug where the Aurora replica crashed when workloads with heavy subtransactions are made on the writer instance\.

1. Fixed a bug where the writer instance crashed due to a memory leak and the depletion of memory used to track active transactions\.

1. Fixed a bug that lead to a crash due to improper initialization when there is no free memory available during PostgreSQL backend startup\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

1. Fixed an out\-of\-memory problem when issuing the `CREATE EXTENSION` command with PostGIS when Database Activity Streams enabled\.

1. Fixed a bug where a `SELECT` query might incorrectly return the error Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that executed on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug where an Aurora PostgreSQL DB cluster with Database Activity Streams enabled might report the beginning of a potential loss window for activity records, but does not report the restoration of connectivity\.

1. Fixed a bug with the[aws\_s3\.table\_import\_from\_s3](AuroraPostgreSQL.Migrating.md#aws_s3.table_import_from_s3) function where a `COPY` from S3 failed with the error HTTP error code: 248\.

## PostgreSQL 11\.7, Aurora PostgreSQL release 3\.2<a name="AuroraPostgreSQL.Updates.20180305.32"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 11\.7\. For more information about the improvements in PostgreSQL 11\.7, see [PostgreSQL release 11\.7](https://www.postgresql.org/docs/11/release-11-7.html)\.

**Topics**
+ [Aurora PostgreSQL release 3\.2\.6](#AuroraPostgreSQL.Updates.20180305.326)
+ [Aurora PostgreSQL release 3\.2\.4](#AuroraPostgreSQL.Updates.20180305.324)
+ [Aurora PostgreSQL release 3\.2\.3](#AuroraPostgreSQL.Updates.20180305.323)
+ [Aurora PostgreSQL release 3\.2\.2](#AuroraPostgreSQL.Updates.20180305.322)
+ [Aurora PostgreSQL release 3\.2\.1](#AuroraPostgreSQL.Updates.20180305.321)

### Aurora PostgreSQL release 3\.2\.6<a name="AuroraPostgreSQL.Updates.20180305.326"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**
+ None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that might result in the error message, ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

**Additional improvements and enhancements**

1. Fixed a bug that in rare cases caused brief read replica unavailability when storage volume grew\.

1. Aurora PostgreSQL Serverless now supports execution of queries on all connections during a scale event\.

1. Fixed a bug in Aurora PostgreSQL Serverless where a leaked lock resulted in a prolonged scale event\.

1. Fixed a bug where the `aurora_replica_status` function showed truncated server identifiers\.

1. Fixed a bug in Aurora PostgreSQL Serverless where connections being migrated during a scale event disconnected with the message: "ERROR: could not open relation with OID \.\.\.\.

1. Fixed a small memory leak in a b\-tree index that could lead to an out of memory condition\.

1. Fixed a bug in a GiST index that might result in an out of memory condition after promoting an Aurora Read Replica\.

1. Improved performance for Database Activity Streams\.

1. Fixed a bug in Database Activity Streams where customers were not notified when an outage ended\.

1. Fixed a bug in the `aws_s3` extension for pre\-signed URL handling that could have resulted in the error message S3 bucket names with a period \(\.\) are not supported\.

1. Fixed a bug in the `aws_s3` extension where incorrect error handling could lead to failures during the import process\.

1. Fixed a bug in the `aws_s3` extension where an import may be blocked indefinitely if an exclusive lock was taken on the relation prior to beginning the operation\.

### Aurora PostgreSQL release 3\.2\.4<a name="AuroraPostgreSQL.Updates.20180305.324"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Aurora PostgreSQL release 3\.2\.3<a name="AuroraPostgreSQL.Updates.20180305.323"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**
+ None

**High priority stability enhancements**
+ None

**Additional improvements and enhancements**

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that ran on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug that might cause brief unavailability for heavy subtransaction workloads when multiple reader instances restart or rejoin the cluster\.

### Aurora PostgreSQL release 3\.2\.2<a name="AuroraPostgreSQL.Updates.20180305.322"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\. 

**High priority stability enhancements**

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary region\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\. 

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 11\.7\.

**Additional improvements and enhancements**

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug where a SELECT query might incorrectly return the error, Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

### Aurora PostgreSQL release 3\.2\.1<a name="AuroraPostgreSQL.Updates.20180305.321"></a>

**New features**

1. Added support for Amazon Aurora PostgreSQL Global Database\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

1. Added the ability to configure the recovery point objective \(RPO\) of a global database for Aurora PostgreSQL\. For more information, see [Managing RPOs for Aurora PostgreSQL–based global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-manage-recovery)\.

You can find the following improvements in this release\.

**Critical stability enhancements**  
None\.

**High priority stability enhancements**

1. Improved performance and availability of read instances when applying DROP TABLE and TRUNCATE TABLE operations\.

1. Fixed a small but continuous memory leak in a diagnostic module that could lead to an out\-of\-memory condition on smaller DB instance types\.

1. Fixed a bug in the `PostGIS` extension which could lead to a database restart\. This has been reported to the PostGIS community as [https://trac.osgeo.org/postgis/ticket/4646](https://trac.osgeo.org/postgis/ticket/4646)\.

1. Fixed a bug where read requests might stop responding due to incorrect error handling in the storage engine\.

1. Fixed a bug that fails for some queries and results in the message ERROR: found xmin xxxxxx from before relfrozenxid yyyyyyy\. This could occur following the promotion of a read instance to a write instance\.

1. Fixed a bug where an Aurora serverless DB cluster might crash while rolling back a scale attempt\.

**Additional improvements and enhancements**

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

## PostgreSQL 11\.6, Aurora PostgreSQL release 3\.1<a name="AuroraPostgreSQL.Updates.20180305.31"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 11\.6\. For more information about the improvements in PostgreSQL 11\.6, see [PostgreSQL release 11\.6](https://www.postgresql.org/docs/11/release-11-6.html)\.

This release contains multiple critical stability enhancements\. Amazon highly recommends upgrading your Aurora PostgreSQL clusters that use older PostgreSQL 11 engines to this release\.

**Topics**
+ [Aurora PostgreSQL release 3\.1\.3](#AuroraPostgreSQL.Updates.20180305.313)
+ [Aurora PostgreSQL release 3\.1\.2](#AuroraPostgreSQL.Updates.20180305.312)
+ [Aurora PostgreSQL release 3\.1\.1](#AuroraPostgreSQL.Updates.20180305.311)
+ [Aurora PostgreSQL release 3\.1\.0](#AuroraPostgreSQL.Updates.20180305.310)

### Aurora PostgreSQL release 3\.1\.3<a name="AuroraPostgreSQL.Updates.20180305.313"></a>

**New features**

1. Aurora PostgreSQL now supports the PostgreSQL [vacuum\_truncate](https://www.postgresql.org/docs/current/sql-createtable.html#RELOPTION-VACUUM-TRUNCATE) storage parameter to manage vacuum truncation for specific tables\. Set this [storage parameter](https://www.postgresql.org/docs/current/sql-createtable.html#SQL-CREATETABLE-STORAGE-PARAMETERS) to false for a table to prevent the [VACUUM](https://www.postgresql.org/docs/current/sql-vacuum.html) SQL command from truncating the table's trailing empty pages\.

**Critical stability enhancements**
+ None 

**High priority stability enhancements**

1. Fixed a bug where reads from storage might stop responding due to incorrect error handling\.

**Additional improvements and enhancements**
+ None

### Aurora PostgreSQL release 3\.1\.2<a name="AuroraPostgreSQL.Updates.20180305.312"></a>

This release contains a critical stability enhancement\. Amazon highly recommends updating your older Aurora PostgreSQL 11\-compatible clusters to this release\.

**Critical stability enhancements**
+ Fixed a bug whereby a reader DB instance might temporarily use stale data\. This could lead to wrong results such as too few or too many rows\. This error is not persisted on storage, and will clear when the database page containing the row has been evicted from cache\. This can happen when the primary DB instance enters a transaction snapshot overflow due to having more than 64 subtransactions in a single transaction\. Applications susceptible to this bug include those that use SQL savepoints or PostgreSQL exception handlers with more than 64 subtransactions in the top transaction\. 

**High priority stability enhancements**
+ Fixed a bug that might cause a reader DB instance to crash causing unavailability while attempting to the join the DB cluster\. This can happen in some cases when the primary DB instance has a transaction snapshot overflow due to a high number of subtransactions\. In this situation the reader DB instance will be unable to join until the snapshot overflow has cleared\.

**Additional improvements and enhancements**
+ Fixed a bug that prevented Performance Insights from determining the query ID of a running statement\.

### Aurora PostgreSQL release 3\.1\.1<a name="AuroraPostgreSQL.Updates.20180305.311"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug whereby the DB instance might be briefly unavailable due to the self\-healing function of the underlying storage\. 

**High priority stability enhancements**

1. Fixed a bug whereby the database engine might crash causing unavailability\. This occurred while scanning an included, non\-key column of a B\-Tree index\. This only applies to PostgreSQL 11 "included column" indexes\.

1. Fixed a bug that might cause the database engine to crash causing unavailability\. This occurred if a newly established database connection encountered a resource exhaustion\-related error during initialization after successful authentication\.

**Additional improvements and enhancements**

1. Provided a fix for the `pg_hint_plan` extension that could lead the database engine to crash causing unavailability\. The open source issue can be tracked at [https://github.com/ossc-db/pg_hint_plan/pull/45](https://github.com/ossc-db/pg_hint_plan/pull/45)\.

1. Fixed a bug where SQL of the form `ALTER FUNCTION ... OWNER TO ...` incorrectly reported `ERROR: improper qualified name (too many dotted names)`\.

1. Improved the performance of `GIN` index vacuum via prefetching\.

1. Fixed a bug in open source PostgreSQL that could lead to a database engine crash causing unavailability\. This occurred during parallel B\-Tree index scans\. This issue has been reported to the PostgreSQL community\. 

1. Improved the performance of in\-memory B\-Tree index scans\.

### Aurora PostgreSQL release 3\.1\.0<a name="AuroraPostgreSQL.Updates.20180305.310"></a>

You can find the following new features and improvements in this engine version\.

**New features**

1. Support for exporting data to Amazon S3\. For more information, see [Exporting data from an Aurora PostgreSQL DB cluster to Amazon S3](postgresql-s3-export.md)\.

1. Support for Amazon Aurora Machine Learning\. For more information, see [Using machine learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.

1. SQL processing enhancements include:
   + Optimizations for `NOT IN` with the `apg_enable_not_in_transform` parameter\.
   + Semi\-join filter pushdown enhancements for hash joins with the `apg_enable_semijoin_push_down` parameter\.
   + Optimizations for redundant inner join removal with the `apg_enable_remove_redundant_inner_joins` parameter\.
   + Improved ANSI compatibility options with the `ansi_constraint_trigger_ordering`, `ansi_force_foreign_key_checks` and `ansi_qualified_update_set_target` parameters\.

   For more information, see [Amazon Aurora PostgreSQL parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)\.

1. New and updated PostgreSQL extensions include:
   + The new `aws_ml` extension\. For more information, see [Using machine learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.
   + The new `aws_s3` extension\. For more information, see [Exporting data from an Aurora PostgreSQL DB cluster to Amazon S3](postgresql-s3-export.md)\.
   + Updates to the `apg_plan_mgmt` extension\. For more information, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)

**Critical stability enhancements**

1. Fixed a bug related to creating B\-tree indexes on temporary tables that in rare cases might result in longer recovery time, and impact availability\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance\. In rare cases, this bug causes a log write failure that might result in longer recovery time, and impact availability\. 

1. Fixed a bug related to handling of reads with high I/O latency that in rare cases might result in longer recovery time, and impact availability\.

**High priority stability enhancements**

1. Fixed a bug related to logical replication whereby `wal` segments are not properly removed from storage\. This can result in storage bloat\. To monitor this, view the `TransactionLogDiskUsage` parameter\. 

1. Fixed multiple bugs, which cause Aurora to crash during prefetch operations on Btree indexes\.

1. Fixed a bug whereby an Aurora restart might time out when logical replication is used\.

1. Enhanced the validation checks performed on data blocks in the buffer cache\. This improves Aurora's detection of inconsistency\.

**Additional improvements and enhancements**

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

1. Fixed a bug whereby storage errors that occur during `CREATE DATABASE` statements are not properly handled\. The bug left the resulting database inaccessible\. The correct behavior is to fail the database creation and return the appropriate error to the user\.

1. Improved handling of PostgreSQL snapshot overflow when a read node attempts to connect to a write node\. Prior to this change, if the write node was in a snapshot overflow state, the read node was unable to join\. A message appeared in the PostgreSQL log file in the form `DEBUG: recovery snapshot waiting for non-overflowed snapshot or until oldest active xid on standby is at least xxxxxxx (now yyyyyyy)`\. A snapshot overflow occurs when an individual transaction has created over 64 subtransactions\. 

1. Fixed a bug related to common table expressions whereby an error is incorrectly raised when a NOT IN class exists in a CTE\. The error is `CTE with NOT IN fails with ERROR: could not find CTE CTE-Name`\.

1. Fixed a bug related to an incorrect `last_error_timestamp` value in the `aurora_replica_status` table\.

1. Fixed a bug to avoid populating shared buffers with blocks belonging to temporary objects\. These blocks correctly reside in PostgreSQL backend local buffers\.

1. Changed the following extensions:
   + Updated `pg_hint_plan` to version 1\.3\.4\.
   + Added `plprofiler` version 4\.1\.
   + Added `pgTAP` version 1\.0\.0\.

## PostgreSQL 11\.4, Aurora PostgreSQL release 3\.0 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.30"></a>

**Note**  
The PostgreSQL engine version 11\.4 with the Aurora PostgreSQL release 3\.0 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 11\.4\. For more information about the improvements in PostgreSQL 11\.4, see [PostgreSQL release 11\.4](https://www.postgresql.org/docs/11/release-11-4.html)\.

You can find the following improvements in this release\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [Aurora PostgreSQL release 2\.3\.5](#AuroraPostgreSQL.Updates.20180305.235)\.

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

## PostgreSQL 10\.14, Aurora PostgreSQL release 2\.7<a name="AuroraPostgreSQL.Updates.20180305.27"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.14\. For more information about the improvements in PostgreSQL 10\.14, see [ PostgreSQL release 10\.14](https://www.postgresql.org/docs/10/release-10-14.html)\.

**Topics**
+ [Aurora PostgreSQL release 2\.7\.1](#AuroraPostgreSQL.Updates.20180305.271)
+ [Aurora PostgreSQL release 2\.7\.0](#AuroraPostgreSQL.Updates.20180305.270)

### Aurora PostgreSQL release 2\.7\.1<a name="AuroraPostgreSQL.Updates.20180305.271"></a>

**Critical stability enhancements**

1. Fixed a bug that caused a read replica to unsuccessfully restart repeatedly in rare cases\.

1. Fixed a bug where a cluster became unavailable when attempting to create more than 16 read replicas or Aurora global database secondary AWS Regions\. The cluster became available again when the new read replica or secondary AWS Region was removed\.

**Additional improvements and enhancements**

1. Fixed a bug that when under heavy load, snapshot import, COPY import, or S3 import stopped responding in rare cases\. 

1. Fixed a bug where a read replica might not join the cluster when the writer was very busy with a write\-intensive workload\.

1. Fixed a bug that caused a cluster to take several minutes to restart if a logical replication stream was terminated while handling many complex transactions\.

1. Disallowed the use of both IAM and Kerberos authentication for the same user\.

### Aurora PostgreSQL release 2\.7\.0<a name="AuroraPostgreSQL.Updates.20180305.270"></a>

**Critical stability enhancements**
+ None

**High priority stability enhancements**

1. Backported fixes for the following PostgreSQL community security issues:
   + [CVE\-2020\-25694](https://nvd.nist.gov/vuln/detail/CVE-2020-25694)
   + [CVE\-2020\-25695](https://nvd.nist.gov/vuln/detail/CVE-2020-25695)
   + [CVE\-2020\-25696](https://nvd.nist.gov/vuln/detail/CVE-2020-25696)

1. Fixed a bug in Aurora PostgreSQL replication that could result in the error message ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

1. Fixed a bug where in some cases, DB clusters that have logical replication enabled did not remove truncated WAL segment files from storage\. This resulted in volume size growth\.

1. Fixed a bug in the `pg_stat_statements` extension that caused excessive CPU consumption\.

**Additional improvements and enhancements**

1. Improved the asynchronous mode performance of database activity streams\.

1. Aurora Serverless v1 for PostgreSQL now supports query execution on all connections during a scale event\.

1. Reduced the delay when publishing to CloudWatch the `rpo_lag_in_msec` metric for Aurora global database clusters\.

1. Fixed a bug in Serverless clusters where transaction processing was unnecessarily suspended for long periods when creating a scale point\.

1. Fixed a bug in Aurora Serverless v1 for PostgreSQL where a leaked lock resulted in a prolonged scale event\.

1. Fixed a bug in Aurora Serverless v1 for PostgreSQL where connections being migrated during a scale event was disconnected with the following message: ERROR: could not open relation with OID \.\.\.

1. Aurora PostgreSQL no longer falls behind on a read node when the backend is blocked writing to the database client\.

1. Fixed a bug that in rare cases caused a brief period of unavailability on a read replica when the storage volume grew\.

1. Fixed a bug when creating a database that could return the following error: ERROR: could not create directory on local disk

1. Fixed a bug where in some cases replaying `XLOG_BTREE_REUSE_PAGE` records on Aurora reader instances caused unnecessary replay lag\.

1. Fixed a bug in the `GiST` index that could result in an out of memory condition after promoting an Aurora read replica\.

1. Fixed a bug where the `aurora_replica_status` function showed truncated server identifiers\.

1. Fixed an S3 import bug that reported ERROR: HTTP 403\. Permission denied when importing data from a file inside an S3 subfolder\.

1. Fixed a bug in the `aws_s3` extension for pre\-signed URL handling that could result in the error message S3 bucket names with a period \(\.\) are not supported\.

1. Fixed a bug in the `aws_s3` extension where an import might be blocked indefinitely if an exclusive lock was taken on the relation prior to beginning the operation\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance that uses `GiST` indexes\. In rare cases, this bug caused a brief period of unavailability after promoting the Aurora cluster\.

1. Fixed a bug in database activity streams where customers were not notified of the end of an outage\.

## PostgreSQL 10\.13, Aurora PostgreSQL release 2\.6<a name="AuroraPostgreSQL.Updates.20180305.26"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.13\. For more information about the improvements in PostgreSQL 10\.13, see [ PostgreSQL release 10\.13](https://www.postgresql.org/docs/10/release-10-13.html)\.

**Topics**
+ [Aurora PostgreSQL release 2\.6\.2](#AuroraPostgreSQL.Updates.20180305.262)
+ [Aurora PostgreSQL release 2\.6\.1](#AuroraPostgreSQL.Updates.20180305.261)
+ [Aurora PostgreSQL release 2\.6\.0](#AuroraPostgreSQL.Updates.20180305.260)

### Aurora PostgreSQL release 2\.6\.2<a name="AuroraPostgreSQL.Updates.20180305.262"></a>

**Critical stability enhancements**

1. None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that could result in the error message ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

1. Fixed a bug where in some cases, DB clusters that have logical replication enabled did not remove truncated WAL segment files from storage\. This resulted in volume size growth\.

1. Fixed an issue with creating a global database cluster in a secondary region\.

1. Backported fixes for the following PostgreSQL community security issues:
   + [CVE\-2020\-25694](https://nvd.nist.gov/vuln/detail/CVE-2020-25694)
   + [CVE\-2020\-25695](https://nvd.nist.gov/vuln/detail/CVE-2020-25695)
   + [CVE\-2020\-25696](https://nvd.nist.gov/vuln/detail/CVE-2020-25696)

1. Fixed a bug in the `pg_stat_statements` extension that caused excessive CPU consumption\.

**Additional improvements and enhancements**

1. Aurora PostgreSQL no longer falls behind on a read node when the backend is blocked writing to the database client\.

1. Reduced the delay when publishing to CloudWatch the `rpo_lag_in_msec` metric for Aurora global database clusters\.

1. Fixed a bug where a `DROP DATABASE` statement didn't remove any relation files\.

1. Fixed a bug where in some cases replaying `XLOG_BTREE_REUSE_PAGE` records on Aurora reader instances caused unnecessary replay lag\.

1. Fixed a small memory leak in a b\-tree index that could lead to an out of memory condition\.

1. Fixed a bug in the `aurora_replica_status()` function where the `server_id` field was sometimes truncated\.

1. Fixed a bug where a log record was incorrectly processed causing the Aurora replica to crash\.

1. Fixed an S3 import bug that reported ERROR: HTTP 403\. Permission denied when importing data from a file inside an S3 subfolder\.

1. Improved performance of the asynchronous mode for database activity streams\.

1. Fixed a bug in the `aws_s3` extension that could result in the error message S3 bucket names with a period \(\.\) are not supported\.

1. Fixed a race condition that caused valid imports to intermittently fail\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance that uses GiST indexes\. In rare cases, this bug caused a brief period of unavailability after promoting the Aurora DB cluster\.

1. Fixed a bug in the `aws_s3` extension where an import may be blocked indefinitely if an exclusive lock was taken on the relation prior to beginning the operation\.

### Aurora PostgreSQL release 2\.6\.1<a name="AuroraPostgreSQL.Updates.20180305.261"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Aurora PostgreSQL release 2\.6\.0<a name="AuroraPostgreSQL.Updates.20180305.260"></a>

You can find the following improvements in this release\.



**New features**

1. Added support for the RDKit extension version 3\.8\. 

   The RDKit extension provides modeling functions for cheminformatics\. Cheminformatics is storing, indexing, searching, retrieving, and applying information about chemical compounds\. For example, with the RDKit extension you can construct models of molecules, search for molecular structures, and read or create molecules in various notations\. You can also perform research on data loaded from the [ChEMBL website](https://www.ebi.ac.uk/chembl/) or a SMILES file\. The Simplified Molecular Input Line Entry System \(SMILES\) is a typographical notation for representing molecules and reactions\. For more information, see [The RDKit database cartridge](https://rdkit.org/docs/Cartridge.html) in the RDKit documentation\. 

1. Added support for the `pglogical` extension version 2\.2\.2\.

   The `pglogical` extension is a logical streaming replication system that provides additional features beyond what's available in PostgreSQL native logical replication\. Features include conflict handling, row filtering, DDL/sequence replication and delayed apply\. You can use the `pglogical` extension to set up replication between Aurora PostgreSQL clusters, between RDS PostgreSQL and Aurora PostgreSQL, and with PostgreSQL databases running outside of RDS\.

1.  Aurora dynamically resizes your cluster storage space\. With dynamic resizing, the storage space for your Aurora DB cluster automatically decreases when you remove data from the DB cluster\. For more information, see [Storage scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.StorageScaling)\. 
**Note**  
 The dynamic resizing feature is being deployed in phases to the AWS Regions where Aurora is available\. Depending on the Region where your cluster is, this feature might not be available yet\. For more information, see [the What's New announcement](https://aws.amazon.com/about-aws/whats-new/2020/10/amazon-aurora-enables-dynamic-resizing-database-storage-space/)\. 

**Critical stability enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\.

**High priority stability enhancements**

1. Fixed a bug when upgrading Aurora Global Database clusters from 10\.11\.

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary AWS Region\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 10\.13\.

**Additional improvements and enhancements**

1. Fixed a bug where the Aurora replica crashed when workloads with heavy subtransactions are made on the writer instance\.

1. Fixed a bug where the writer instance crashed due to a memory leak and the depletion of memory used to track active transactions\.

1. Fixed a bug that lead to a crash due to improper initialization when there is no free memory available during PostgreSQL backend startup\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

1. Fixed an out\-of\-memory problem when issuing the `CREATE EXTENSION` command with PostGIS when Database Activity Streams enabled\.

1. Fixed a bug where a `SELECT` query might incorrectly return the error Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that executed on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug where an Aurora PostgreSQL DB cluster with Database Activity Streams enabled might report the beginning of a potential loss window for activity records, but does not report the restoration of connectivity\.

## PostgreSQL 10\.12, Aurora PostgreSQL release 2\.5<a name="AuroraPostgreSQL.Updates.20180305.25"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.12\. For more information about the improvements in PostgreSQL 10\.12, see [ PostgreSQL release 10\.12](https://www.postgresql.org/docs/10/release-10-12.html)\.

**Topics**
+ [Aurora PostgreSQL release 2\.5\.6](#AuroraPostgreSQL.Updates.20180305.256)
+ [Aurora PostgreSQL release 2\.5\.4](#AuroraPostgreSQL.Updates.20180305.254)
+ [Aurora PostgreSQL release 2\.5\.3](#AuroraPostgreSQL.Updates.20180305.253)
+ [Aurora PostgreSQL release 2\.5\.2](#AuroraPostgreSQL.Updates.20180305.252)
+ [Aurora PostgreSQL release 2\.5\.1](#AuroraPostgreSQL.Updates.20180305.251)

### Aurora PostgreSQL release 2\.5\.6<a name="AuroraPostgreSQL.Updates.20180305.256"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**
+ None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that might result in the error message, ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

**Additional improvements and enhancements**

1. Fixed a bug that in rare cases caused brief read replica unavailability when storage volume grew\.

1. Aurora PostgreSQL Serverless now supports execution of queries on all connections during a scale event\.

1. Fixed a bug in Aurora PostgreSQL Serverless where a leaked lock resulted in a prolonged scale event\.

1. Fixed a bug where the `aurora_replica_status` function showed truncated server identifiers\.

1. Fixed a bug in Aurora PostgreSQL Serverless where connections being migrated during a scale event disconnected with the message: "ERROR: could not open relation with OID \.\.\.\.

1. Fixed a bug in a GiST index that might result in an out of memory condition after promoting an Aurora Read Replica\.

1. Improved performance for Database Activity Streams\.

1. Fixed a bug in Database Activity Streams where customers were not notified when an outage ended\.

1. Fixed a bug in the `aws_s3` extension for pre\-signed URL handling that could have resulted in the error message S3 bucket names with a period \(\.\) are not supported\.

1. Fixed a bug in the `aws_s3` extension where incorrect error handling could lead to failures during the import process\.

1. Fixed a bug in the `aws_s3` extension where an import may be blocked indefinitely if an exclusive lock was taken on the relation prior to beginning the operation\.

### Aurora PostgreSQL release 2\.5\.4<a name="AuroraPostgreSQL.Updates.20180305.254"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug which materializes when the `NOT EXISTS` operator incorrectly returns TRUE which can only happen under the following unusual set of circumstances:
   + A query is using the `NOT EXISTS` operator\.
   + The column or columns being evaluated against the outer query in the `NOT EXISTS` subquery contain a NULL value\.
   + There is not a another predicate in the subquery that removes the need for the evaluation of the NULL values\.
   + The filter used in the subquery does not use an index seek for its execution\.
   + The operator is not converted to a join by the query optimizer\.

### Aurora PostgreSQL release 2\.5\.3<a name="AuroraPostgreSQL.Updates.20180305.253"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**
+ None

**High priority stability enhancements**
+ None

**Additional improvements and enhancements**

1. Fixed a bug in Aurora PostgreSQL Serverless where queries that ran on previously idle connections got delayed until the scale operation completed\.

1. Fixed a bug that might cause brief unavailability for heavy subtransaction workloads when multiple reader instances restart or rejoin the cluster\.

1. Fixed a bug in Aurora PostgreSQL Global Database where upgrading a secondary cluster might result in failure due to incorrect checksum versioning\. This might have required re\-creating the secondary clusters\.

### Aurora PostgreSQL release 2\.5\.2<a name="AuroraPostgreSQL.Updates.20180305.252"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\. 

**High priority stability enhancements**

1. Fixed a bug in Aurora Global Database that could cause delays in upgrading the database engine in a secondary region\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\. 

1. Fixed a bug that in rare cases caused delays in upgrading a database to engine version 10\.12\.

**Additional improvements and enhancements**

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug where a SELECT query might incorrectly return the error, Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where an Aurora PostgreSQL Serverless DB cluster might return the following error after a scaling event: ERROR: prepared statement "S\_6" already exists\.

### Aurora PostgreSQL release 2\.5\.1<a name="AuroraPostgreSQL.Updates.20180305.251"></a>

**New features**

1. Added support for Amazon Aurora PostgreSQL Global Database\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

1. Added the ability to configure the recovery point objective \(RPO\) of a global database for Aurora PostgreSQL\. For more information, see [Managing RPOs for Aurora PostgreSQL–based global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-manage-recovery)\.

You can find the following improvements in this release\.

**Critical stability enhancements**  
None\.

**High priority stability enhancements**

1. Improved performance and availability of read instances when applying DROP TABLE and TRUNCATE TABLE operations\.

1. Fixed a small but continuous memory leak in a diagnostic module that could lead to an out\-of\-memory condition on smaller DB instance types\.

1. Fixed a bug in the `PostGIS` extension which could lead to a database restart\. This has been reported to the PostGIS community as [https://trac.osgeo.org/postgis/ticket/4646](https://trac.osgeo.org/postgis/ticket/4646)\.

1. Fixed a bug where read requests might stop responding due to incorrect error handling in the storage engine\.

1. Fixed a bug that fails for some queries and results in the message ERROR: found xmin xxxxxx from before relfrozenxid yyyyyyy\. This could occur following the promotion of a read instance to a write instance\.

1. Fixed a bug where an Aurora serverless DB cluster might crash while rolling back a scale attempt\.

**Additional improvements and enhancements**

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

## PostgreSQL 10\.11, Aurora PostgreSQL release 2\.4<a name="AuroraPostgreSQL.Updates.20180305.24"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.11\. For more information about the improvements in PostgreSQL 10\.11, see [PostgreSQL release 10\.11](https://www.postgresql.org/docs/10/release-10-11.html)\.

This release contains multiple critical stability enhancements\. Amazon highly recommends upgrading your Aurora PostgreSQL clusters that use older PostgreSQL 10 engines to this release\.

**Topics**
+ [Aurora PostgreSQL release 2\.4\.3](#AuroraPostgreSQL.Updates.20180305.243)
+ [Aurora PostgreSQL release 2\.4\.2](#AuroraPostgreSQL.Updates.20180305.242)
+ [Aurora PostgreSQL release 2\.4\.1](#AuroraPostgreSQL.Updates.20180305.241)
+ [Aurora PostgreSQL release 2\.4\.0](#AuroraPostgreSQL.Updates.20180305.240)

### Aurora PostgreSQL release 2\.4\.3<a name="AuroraPostgreSQL.Updates.20180305.243"></a>

**New features**

1. Aurora PostgreSQL now supports the PostgreSQL [vacuum\_truncate](https://www.postgresql.org/docs/current/sql-createtable.html#RELOPTION-VACUUM-TRUNCATE) storage parameter to manage vacuum truncation for specific tables\. Set this [storage parameter](https://www.postgresql.org/docs/current/sql-createtable.html#SQL-CREATETABLE-STORAGE-PARAMETERS) to false for a table to prevent the [VACUUM](https://www.postgresql.org/docs/current/sql-vacuum.html) SQL command from truncating the table's trailing empty pages\.

**Critical stability enhancements**
+ None 

**High priority stability enhancements**

1. Fixed a bug where reads from storage might stop responding due to incorrect error handling\.

**Additional improvements and enhancements**
+ None

### Aurora PostgreSQL release 2\.4\.2<a name="AuroraPostgreSQL.Updates.20180305.242"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug whereby a reader DB instance might temporarily use stale data\. This could lead to wrong results such as too few or too many rows\. This error is not persisted on storage, and will clear when the database page containing the row has been evicted from cache\. This can happen when the primary DB instance enters a transaction snapshot overflow due to having more than 64 subtransactions in a single transaction\. Applications susceptible to this bug include those that use SQL savepoints or PostgreSQL exception handlers with more than 64 subtransactions in the top transaction\. 

**High priority stability enhancements**

1. Fixed a bug that may cause a reader DB instance to crash causing unavailability while attempting to the join the DB cluster\. This can happen in some cases when the primary DB instance has a transaction snapshot overflow due to a high number of subtransactions\. In this situation the reader DB instance will be unable to join until the snapshot overflow has cleared\.

**Additional improvements and enhancements**

1. Fixed a bug that prevented Performance Insights from determining the query ID of a running statement\.

### Aurora PostgreSQL release 2\.4\.1<a name="AuroraPostgreSQL.Updates.20180305.241"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug whereby the DB instance might be briefly unavailable due to the self\-healing function of the underlying storage\. 

**High priority stability enhancements**

1. Fixed a bug that might cause the database engine to crash causing unavailability\. This occurred if a newly established database connection encountered a resource exhaustion\-related error during initialization after successful authentication\.

**Additional improvements and enhancements**

1. Provided a fix for the `pg_hint_plan` extension that could lead the database engine to crash causing unavailability\. The open source issue can be tracked at [https://github.com/ossc-db/pg_hint_plan/pull/45](https://github.com/ossc-db/pg_hint_plan/pull/45)\.

1. Fixed a bug where SQL of the form `ALTER FUNCTION ... OWNER TO ...` incorrectly reported `ERROR: improper qualified name (too many dotted names)`\.

1. Improved the performance of `GIN` index vacuum via prefetching\.

1. Fixed a bug in open source PostgreSQL that could lead to a database engine crash causing unavailability\. This occurred during parallel B\-Tree index scans\. This issue has been reported to the PostgreSQL community\. 

1. Improved the performance of in\-memory B\-Tree index scans\.

1. Additional general improvements to the stability and availability of Aurora PostgreSQL\.

### Aurora PostgreSQL release 2\.4\.0<a name="AuroraPostgreSQL.Updates.20180305.240"></a>

You can find the following new features and improvements in this engine version\.

**New features**

1. Support for exporting data to Amazon S3\. For more information, see [Exporting data from an Aurora PostgreSQL DB cluster to Amazon S3](postgresql-s3-export.md)\.

1. Support for Amazon Aurora Machine Learning\. For more information, see [Using machine learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.

1. SQL processing enhancements include:
   + Optimizations for `NOT IN` with the `apg_enable_not_in_transform` parameter\.
   + Semi\-join filter pushdown enhancements for hash joins with the `apg_enable_semijoin_push_down` parameter\.
   + Optimizations for redundant inner join removal with the `apg_enable_remove_redundant_inner_joins` parameter\.
   + Improved ANSI compatibility options with the `ansi_constraint_trigger_ordering`, `ansi_force_foreign_key_checks` and `ansi_qualified_update_set_target` parameters\.

   For more information, see [Amazon Aurora PostgreSQL parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)\.

1. New and updated PostgreSQL extensions include:
   + The new `aws_ml` extension\. For more information, see [Using machine learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)\.
   + The new `aws_s3` extension\. For more information, see [Exporting data from an Aurora PostgreSQL DB cluster to Amazon S3](postgresql-s3-export.md)\.
   + Updates to the `apg_plan_mgmt` extension\. For more information, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)

**Critical stability enhancements**

1. Fixed a bug related to creating B\-tree indexes on temporary tables that in rare cases may result in longer recovery time, and impact availability\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance\. In rare cases, this bug causes a log write failure that may result in longer recovery time, and impact availability\. 

1. Fixed a bug related to handling of reads with high I/O latency that in rare cases may result in longer recovery time, and impact availability\.

**High priority stability enhancements**

1. Fixed a bug related to logical replication whereby `wal` segments are not properly removed from storage\. This can result in storage bloat\. To monitor this, view the `TransactionLogDiskUsage` parameter\. 

1. Fixed multiple bugs, which cause Aurora to crash during prefetch operations on Btree indexes\.

1. Fixed a bug whereby an Aurora restart may timeout when logical replication is used\.

1. Enhanced the validation checks performed on data blocks in the buffer cache\. This improves Aurora's detection of inconsistency\.

**Additional improvements and enhancements**

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

1. Fixed a bug whereby storage errors that occur during `CREATE DATABASE` statements are not properly handled\. The bug left the resulting database inaccessible\. The correct behavior is to fail the database creation and return the appropriate error to the user\.

1. Improved handling of PostgreSQL snapshot overflow when a read node attempts to connect to a write node\. Prior to this change, if the write node was in a snapshot overflow state, the read node was unable to join\. A message appeared in the PostgreSQL log file in the form `DEBUG: recovery snapshot waiting for non-overflowed snapshot or until oldest active xid on standby is at least xxxxxxx (now yyyyyyy)`\. A snapshot overflow occurs when an individual transaction has created over 64 subtransactions\. 

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

## PostgreSQL 10\.7, Aurora PostgreSQL release 2\.3 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.23"></a>

**Note**  
The PostgreSQL engine version 10\.7 with the Aurora PostgreSQL release 2\.3 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.7\. For more information about the improvements in PostgreSQL 10\.7, see [PostgreSQL release 10\.7](https://www.postgresql.org/docs/10/release-10-7.html)\.

**Topics**
+ [Aurora PostgreSQL release 2\.3\.5](#AuroraPostgreSQL.Updates.20180305.235)
+ [Aurora PostgreSQL release 2\.3\.3](#AuroraPostgreSQL.Updates.20180305.233)
+ [Aurora PostgreSQL release 2\.3\.1](#AuroraPostgreSQL.Updates.20180305.231)
+ [Aurora PostgreSQL release 2\.3\.0](#AuroraPostgreSQL.Updates.20180305.230)

### Aurora PostgreSQL release 2\.3\.5<a name="AuroraPostgreSQL.Updates.20180305.235"></a>

You can find the following improvements in this release\.

**Improvements**

1. Fixed a bug that could cause DB instance restarts\.

1. Fixed a bug that could cause a crash when the PostgreSQL backend exits while using logical replication\.

1. Fixed a bug that could cause a restart when reads occurred during failovers\.

1. Fixed a bug with the `wal2json` module for logical replication\.

1. Fixed a bug that could result in inconsistent metadata\.

### Aurora PostgreSQL release 2\.3\.3<a name="AuroraPostgreSQL.Updates.20180305.233"></a>

You can find the following improvements in this release\.

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

### Aurora PostgreSQL release 2\.3\.1<a name="AuroraPostgreSQL.Updates.20180305.231"></a>

You can find the following improvements in this release\.

**Improvements**

1. Fixed multiple bugs related to I/O prefetching that caused engine crashes\.

### Aurora PostgreSQL release 2\.3\.0<a name="AuroraPostgreSQL.Updates.20180305.230"></a>

You can find the following improvements in this release\.

**New features**

1. Aurora PostgreSQL now performs I/O prefetching while scanning B\-tree indexes\. This results in significantly improved performance for B\-tree scans over uncached data\.

**Improvements**

1. Fixed a bug whereby read nodes may fail with the error "too many LWLocks taken"\.

1. Addressed numerous issues that caused read nodes to fail to startup while the cluster is under heavy write workload\.

1. Fixed a bug whereby usage of the `aurora_stat_memctx_usage()` function could lead to a crash\.

1. Improved the cache replacement strategy used by table scans to minimize thrashing of the buffer cache\.

## PostgreSQL 10\.6, Aurora PostgreSQL release 2\.2 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.22"></a>

**Note**  
The PostgreSQL engine version 10\.6 with the Aurora PostgreSQL release 2\.2 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.6\. For more information about the improvements in PostgreSQL 10\.6, see [PostgreSQL release 10\.6](https://www.postgresql.org/docs/10/release-10-6.html)\.

**Topics**
+ [Aurora PostgreSQL release 2\.2\.1](#AuroraPostgreSQL.Updates.20180305.221)
+ [Aurora PostgreSQL release 2\.2\.0](#AuroraPostgreSQL.Updates.20180305.220)

### Aurora PostgreSQL release 2\.2\.1<a name="AuroraPostgreSQL.Updates.20180305.221"></a>

You can find the following improvements in this release\.

**Improvements**

1. Improved stability of logical replication\.

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Aurora PostgreSQL release 2\.2\.0<a name="AuroraPostgreSQL.Updates.20180305.220"></a>

You can find the following improvements in this release\.

**New features**

1. Added the restricted password management feature\. Restricted password management enables you to restrict who can manage user passwords and password expiration changes by using the parameter `rds.restrict_password_commands` and the role `rds_password`\. For more information, see [Restricting password management](AuroraPostgreSQL.Security.md#RestrictPasswordMgmt)\. 

## PostgreSQL 10\.5, Aurora PostgreSQL release 2\.1 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.21"></a>

**Note**  
The PostgreSQL engine version 10\.5 with the Aurora PostgreSQL release 2\.1 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.5\. For more information about the improvements in PostgreSQL 10\.5, see [PostgreSQL release 10\.5](https://www.postgresql.org/docs/current/static/release-10-5.html)\. 

**Topics**
+ [Aurora PostgreSQL release 2\.1\.1](#AuroraPostgreSQL.Updates.20180305.211)
+ [Aurora PostgreSQL release 2\.1\.0](#AuroraPostgreSQL.Updates.20180305.210)

### Aurora PostgreSQL release 2\.1\.1<a name="AuroraPostgreSQL.Updates.20180305.211"></a>

You can find the following improvements in this release\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Aurora PostgreSQL release 2\.1\.0<a name="AuroraPostgreSQL.Updates.20180305.210"></a>

You can find the following improvements in this release\.

**New features**

1. General availability of Aurora Query Plan Management, which enables customers to track and manage any or all query plans used by their applications, to control query optimizer plan selection, and to ensure high and stable applicationperformance\. For more information, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

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

## PostgreSQL 10\.4, Aurora PostgreSQL release 2\.0 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.20"></a>

**Note**  
The PostgreSQL engine version 10\.4 with the Aurora PostgreSQL release 2\.0 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 10\.4\. For more information about the improvements in PostgreSQL 10\.4, see [PostgreSQL release 10\.4](https://www.postgresql.org/docs/10/release-10-4.html)\.

**Topics**
+ [Aurora PostgreSQL release 2\.0\.1](#AuroraPostgreSQL.Updates.20180305.201)
+ [Aurora PostgreSQL release 2\.0\.0](#AuroraPostgreSQL.Updates.20180305.200)

### Aurora PostgreSQL release 2\.0\.1<a name="AuroraPostgreSQL.Updates.20180305.201"></a>

You can find the following improvements in this release\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Aurora PostgreSQL release 2\.0\.0<a name="AuroraPostgreSQL.Updates.20180305.200"></a>

You can find the following improvements in this release\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [PostgreSQL 9\.6\.9, Aurora PostgreSQL release 1\.3 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.13)\.

1. The temporary file size limitation is user\-configurable\. You require the **rds\_superuser** role to modify the `temp_file_limit` parameter\.

1. Updated the `GDAL` library, which is used by the `PostGIS` extension\. 

1. Updated the `ip4r` extension to version 2\.1\.1\.

1. Updated the `pg_repack` extension to version 1\.4\.3\. 

1. Updated the `plv8` extension to version 2\.1\.2\.

1. Parallel queries – When you create a new Aurora PostgreSQL version 2\.0 instance, parallel queries are enabled for the `default.postgres10` parameter group\. The parameter `max_parallel_workers_per_gather` is set to 2 by default, but you can modify it to support your specific workload requirements\.

1. Fixed a bug whereby read nodes may crash following a specific type of free space change from the write node\.

## PostgreSQL 9\.6\.19, Aurora PostgreSQL release 1\.9<a name="AuroraPostgreSQL.Updates.20180305.19"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.19\. For more information about the improvements in PostgreSQL 9\.6\.19, see [ PostgreSQL release 9\.6\.19](https://www.postgresql.org/docs/9.6/release-9-6-19.html)\.

**Topics**
+ [Aurora PostgreSQL release 1\.9\.1](#AuroraPostgreSQL.Updates.20180305.191)
+ [Aurora PostgreSQL release 1\.9\.0](#AuroraPostgreSQL.Updates.20180305.190)

### Aurora PostgreSQL release 1\.9\.1<a name="AuroraPostgreSQL.Updates.20180305.191"></a>

**Critical stability enhancements**

1. Fixed a bug that caused a read replica to unsuccessfully restart repeatedly in rare cases\.

**Additional improvements and enhancements**

1. Fixed a bug that when under heavy load, snapshot import, COPY import, or S3 import stopped responding in rare cases\. 

1. Fixed a bug where a read replica might not join the cluster when the writer was very busy with a write\-intensive workload\.

### Aurora PostgreSQL release 1\.9\.0<a name="AuroraPostgreSQL.Updates.20180305.190"></a>

**Critical stability enhancements**
+ None

**High priority stability enhancements**

1. Backported a fix for the PostgreSQL community security issues CVE\-2020\-25694, CVE\-2020\-25695, and CVE\-2020\-25696\.

1. Fixed a bug in Aurora PostgreSQL replication that might result in the following error message: ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound

**Additional improvements and enhancements**

1. Aurora PostgreSQL no longer falls behind on a read node when the backend is blocked writing to the database client\.

1. Fixed a bug that in rare cases caused a brief period of unavailability on a read replica when the storage volume grew\.

1. Fixed a bug when creating a database that could return the following error: ERROR: could not create directory on local disk

1. Fixed a bug in the `GiST` index that could result in an out of memory condition after promoting an Aurora read replica\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance that uses `GiST` indexes\. In rare cases, this bug caused a brief period of unavailability after promoting the Aurora cluster\.

## PostgreSQL 9\.6\.18, Aurora PostgreSQL release 1\.8<a name="AuroraPostgreSQL.Updates.20180305.18"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.18\. For more information about the improvements in PostgreSQL 9\.6\.18, see [ PostgreSQL release 9\.6\.18](https://www.postgresql.org/docs/9.6/release-9-6-18.html)\.

**Topics**
+ [Aurora PostgreSQL release 1\.8\.2](#AuroraPostgreSQL.Updates.20180305.182)
+ [Aurora PostgreSQL release 1\.8\.0](#AuroraPostgreSQL.Updates.20180305.180)

There is no version 1\.8\.1\.

### Aurora PostgreSQL release 1\.8\.2<a name="AuroraPostgreSQL.Updates.20180305.182"></a>

**Critical stability enhancements**

1. None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that could result in the error message ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

1. Backported fixes for the following PostgreSQL community security issues:
   + [CVE\-2020\-25694](https://nvd.nist.gov/vuln/detail/CVE-2020-25694)
   + [CVE\-2020\-25695](https://nvd.nist.gov/vuln/detail/CVE-2020-25695)
   + [CVE\-2020\-25696](https://nvd.nist.gov/vuln/detail/CVE-2020-25696)

**Additional improvements and enhancements**

1. Aurora PostgreSQL no longer falls behind on a read node when the backend is blocked writing to the database client\.

1. Fixed a bug where a `DROP DATABASE` statement didn't remove any relation files\.

1. Fixed a small memory leak in a b\-tree index that could lead to an out of memory condition\.

1. Fixed a bug in the `aurora_replica_status()` function where the `server_id` field was sometimes truncated\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance that uses GiST indexes\. In rare cases, this bug caused a brief period of unavailability after promoting the Aurora DB cluster\.

### Aurora PostgreSQL release 1\.8\.0<a name="AuroraPostgreSQL.Updates.20180305.180"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\.

**Additional improvements and enhancements**

1. Fixed a bug where the Aurora replica crashed when workloads with heavy subtransactions are made on the writer instance\.

1. Fixed a bug where the writer instance crashed due to a memory leak and the depletion of memory used to track active transactions\.

1. Fixed a bug that lead to a crash due to improper initialization when there is no free memory available during PostgreSQL backend startup\.

1. Fixed a crash during a BTree prefetch that occurred under certain conditions that depended on the shape and data contained in the index\.

1. Fixed a bug where a `SELECT` query might incorrectly return the error Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

## PostgreSQL 9\.6\.17, Aurora PostgreSQL release 1\.7<a name="AuroraPostgreSQL.Updates.20180305.17"></a>

This release of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.17\. For more information about the improvements in PostgreSQL 9\.6\.17, see [ PostgreSQL release 9\.6\.17](https://www.postgresql.org/docs/9.6/release-9-6-17.html)\.

**Topics**
+ [Aurora PostgreSQL release 1\.7\.6](#AuroraPostgreSQL.Updates.20180305.176)
+ [Aurora PostgreSQL release 1\.7\.3](#AuroraPostgreSQL.Updates.20180305.173)
+ [Aurora PostgreSQL release 1\.7\.2](#AuroraPostgreSQL.Updates.20180305.172)
+ [Aurora PostgreSQL release 1\.7\.1](#AuroraPostgreSQL.Updates.20180305.171)

### Aurora PostgreSQL release 1\.7\.6<a name="AuroraPostgreSQL.Updates.20180305.176"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**
+ None

**High priority stability enhancements**

1. Fixed a bug in Aurora PostgreSQL replication that might result in the error message, ERROR: MultiXactId nnnn has not been created yet \-\- apparent wraparound\.

**Additional improvements and enhancements**

1. Fixed a bug that in rare cases caused brief read replica unavailability when storage volume grew\.

1. Fixed a bug in a b\-tree index read optimization that might have caused a brief period of unavailability\.

1. Fixed a bug in a GiST index that might result in an out of memory condition after promoting an Aurora Read Replica\.

### Aurora PostgreSQL release 1\.7\.3<a name="AuroraPostgreSQL.Updates.20180305.173"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**
+ None

**High priority stability enhancements**
+ None

**Additional improvements and enhancements**

1. Fixed a bug that might cause brief unavailability for heavy subtransaction workloads when multiple reader instances restart or rejoin the cluster\.

### Aurora PostgreSQL release 1\.7\.2<a name="AuroraPostgreSQL.Updates.20180305.172"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**

1. Fixed a bug related to heap page extend that in rare cases resulted in longer recovery time and impacted availability\. 

**High Priority Stability Enhancements**  
None

**Additional improvements and enhancements**

1. Fixed a bug where the database might be unavailable briefly due to error handling in database storage growth\.

1. Fixed a bug where a SELECT query might incorrectly return the error, Attempting to read past EOF of relation rrrr\. blockno=bbb nblocks=nnn\.

1. Fixed an issue with the internal metrics collector that could result in erratic CPU spikes on database instances\.

### Aurora PostgreSQL release 1\.7\.1<a name="AuroraPostgreSQL.Updates.20180305.171"></a>

You can find the following improvements in this release\.

**Critical stability enhancements**  
None\.

**High priority stability enhancements**

1. Improved performance and availability of read instances when applying DROP TABLE and TRUNCATE TABLE operations\.

1. Fixed a small but continuous memory leak in a diagnostic module that could lead to an out\-of\-memory condition on smaller DB instance types\.

1. Fixed a bug in the `PostGIS` extension which could lead to a database restart\. This has been reported to the PostGIS community as [https://trac.osgeo.org/postgis/ticket/4646](https://trac.osgeo.org/postgis/ticket/4646)\.

1. Fixed a bug where read requests might stop responding due to incorrect error handling in the storage engine\.

1. Fixed a bug that fails for some queries and results in the message ERROR: found xmin xxxxxx from before relfrozenxid yyyyyyy\. This could occur following the promotion of a read instance to a write instance\.

**Additional improvements and enhancements**

1. Improved performance for queries that read many rows from storage\.

1. Improved performance and availability of reader DB instances during heavy read workload\.

1. Fixed a bug that resulted in the message ERROR: could not create file "base/xxxxxx/yyyyyyy" as a previous version still exists on disk: Success\. Please contact AWS customer support\. This can occur during object creation after PostgreSQL's 32\-bit object identifier has wrapped around\. 

1. Fixed a bug in the `pg_hint_plan` extension where a multi\-statement query could lead to a crash when `enable_hint_table` is enabled\. This is tracked in the PostgreSQL community as [https://github.com/ossc-db/pg_hint_plan/issues/25](https://github.com/ossc-db/pg_hint_plan/issues/25)\.

1. Changed the following extensions:
   + Updated `orafce` to version 3\.8

## PostgreSQL 9\.6\.16, Aurora PostgreSQL release 1\.6<a name="AuroraPostgreSQL.Updates.20180305.16"></a>

This version of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.16\. For more information about the improvements in release 9\.6\.16, see [PostgreSQL release 9\.6\.16](https://www.postgresql.org/docs/9.6/release-9-6-16.html)\.

This release contains multiple critical stability enhancements\. Amazon highly recommends upgrading your Aurora PostgreSQL clusters that use older PostgreSQL 9\.6 engines to this release\.

**Topics**
+ [Aurora PostgreSQL release 1\.6\.3](#AuroraPostgreSQL.Updates.20180305.163)
+ [Aurora PostgreSQL release 1\.6\.2](#AuroraPostgreSQL.Updates.20180305.162)
+ [Aurora PostgreSQL release 1\.6\.1](#AuroraPostgreSQL.Updates.20180305.161)
+ [Aurora PostgreSQL release 1\.6\.0](#AuroraPostgreSQL.Updates.20180305.160)

### Aurora PostgreSQL release 1\.6\.3<a name="AuroraPostgreSQL.Updates.20180305.163"></a>

**New features**

1. Aurora PostgreSQL now supports the PostgreSQL [vacuum\_truncate](https://www.postgresql.org/docs/current/sql-createtable.html#RELOPTION-VACUUM-TRUNCATE) storage parameter to manage vacuum truncation for specific tables\. Set this [storage parameter](https://www.postgresql.org/docs/current/sql-createtable.html#SQL-CREATETABLE-STORAGE-PARAMETERS) to false when creating or altering a table to prevent the [VACUUM](https://www.postgresql.org/docs/current/sql-vacuum.html) SQL command from truncating the table's trailing empty pages\.

**Critical stability enhancements**
+ None 

**High priority stability enhancements**

1. Fixed a bug where reads from storage might stop responding due to incorrect error handling\.

**Additional improvements and enhancements**
+ None

### Aurora PostgreSQL release 1\.6\.2<a name="AuroraPostgreSQL.Updates.20180305.162"></a>

You can find the following improvements in this engine update\.

**Critical stability enhancements**

1. Fixed a bug whereby a reader DB instance might temporarily use stale data\. This could lead to wrong results such as too few or too many rows\. This error is not persisted on storage, and will clear when the database page containing the row has been evicted from cache\. This can happen when the primary DB instance enters a transaction snapshot overflow due to having more than 64 subtransactions in a single transaction\. Applications susceptible to this bug include those that use SQL savepoints or PostgreSQL exception handlers with more than 64 subtransactions in the top transaction\. 

**High priority stability enhancements**

1. Fixed a bug that may cause a reader DB instance to crash causing unavailability while attempting to the join the DB cluster\. This can happen in some cases when the primary DB instance has a transaction snapshot overflow due to a high number of subtransactions\. In this situation the reader DB instance will be unable to join until the snapshot overflow has cleared\.

**Additional improvements and enhancements**

1. Fixed a bug that prevented Performance Insights from determining the query ID of a running statement\.

### Aurora PostgreSQL release 1\.6\.1<a name="AuroraPostgreSQL.Updates.20180305.161"></a>

You can find the following improvements in this engine update\.

**Critical stability enhancements**

1. None

**High priority stability enhancements**

1. Fixed a bug that might cause the database engine to crash causing unavailability\. This occurred if a newly established database connection encountered a resource exhaustion\-related error during initialization after successful authentication\.

**Additional improvements and enhancements**

1. Provided general improvements to the stability and availability of Aurora PostgreSQL\.

### Aurora PostgreSQL release 1\.6\.0<a name="AuroraPostgreSQL.Updates.20180305.160"></a>

You can find the following new features and improvements in this engine version\.

**New features**

1. Updates to the `apg_plan_mgmt` extension\. For more information, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)

**Critical stability enhancements**

1. Fixed a bug related to creating B\-tree indexes on temporary tables that in rare cases may result in longer recovery time, and impact availability\.

1. Fixed a bug related to replication when Aurora PostgreSQL is acting as a physical replica of an RDS PostgreSQL instance\. In rare cases, this bug causes a log write failure that may result in longer recovery time, and impact availability\. 

1. Fixed a bug related to handling of reads with high I/O latency that in rare cases may result in longer recovery time, and impact availability\.

**High priority stability enhancements**

1. Fixed multiple bugs, which cause Aurora to crash during prefetch operations on Btree indexes\.

1. Enhanced the validation checks performed on data blocks in the buffer cache\. This improves Aurora's detection of inconsistency\.

**Additional improvements and enhancements**

1. The query plan management extension `apg_plan_mgmt` has an improved algorithm for managing plan generation for highly partitioned tables\.

1. Reduced startup time on instances with large caches via improvements in the buffer cache recovery algorithm\.

1. Improved the performance of the read\-node\-apply process under high transaction rate workloads by using changes to PostgreSQL `LWLock` prioritization\. These changes prevent starvation of the read\-node\-apply process while the PostgreSQL `ProcArray` is under heavy contention\.

1. Fixed a bug whereby a read node may crash during the replay of a PostgreSQL `SLRU`\-truncate operation\.

1. Fixed a bug where in rare cases, database writes might stall following an error returned by one of the six copies of an Aurora log record\. 

1. Fixed a memory leak on read nodes when cluster cache management is enabled\.

1. Fixed a bug whereby importing an RDS PostgreSQL snapshot might stop responding if the source snapshot contains a large number of unlogged relations\.

1. Fixed a bug related to `hot_standby_feedback` for read nodes whereby the read node may report the wrong transaction id epoch to the write node\. This can cause the write node to ignore the `hot_standby_feedback` and invalidate snapshots on the read node\.

1. Fixed a bug whereby storage errors that occur during `CREATE DATABASE` statements are not properly handled\. The bug left the resulting database inaccessible\. The correct behavior is to fail the database creation and return the appropriate error to the user\.

1. Improved handling of PostgreSQL snapshot overflow when a read node attempts to connect to a write node\. Prior to this change, if the write node was in a snapshot overflow state, the read node was unable to join\. A message appear in the PostgreSQL log file in the form `DEBUG: recovery snapshot waiting for non-overflowed snapshot or until oldest active xid on standby is at least xxxxxxx (now yyyyyyy)`\. A snapshot overflow occurs when an individual transaction has created over 64 subtransactions\. 

1. Fixed a bug related to common table expressions whereby an error is incorrectly raised when a NOT IN class exists in a CTE\. The error is `CTE with NOT IN fails with ERROR: could not find CTE CTE-Name`\.

1. Fixed a bug related to an incorrect `last_error_timestamp` value in the `aurora_replica_status` table\.

1. Fixed a bug to avoid populating shared buffers with blocks belonging to temporary objects\. These blocks correctly reside in PostgreSQL backend local buffers\. 

1. Fixed a bug where in rare cases Aurora may exhibit 100% CPU utilization while acting as a replica of an RDS PostgreSQL instance even when the replication stream is idle\. 

1. Backported a change from PostgreSQL 11 which improves the cleanup of orphaned temporary tables\. Without this change, it is possible that in rare cases orphaned temporary tables can to lead to transaction ID wraparound\. For more information, see this [PostgreSQL community commit](https://github.com/postgres/postgres/commit/246a6c8f7b237cc1943efbbb8a7417da9288f5c4)\. 

1. Fixed a bug where a Writer instance may accept replication registration requests from Reader instances while having an uninitialized startup process\.

1. Changed the following extensions:
   + Updated `pg_hint_plan` to version 1\.2\.5\.

## PostgreSQL 9\.6\.12, Aurora PostgreSQL release 1\.5 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.15"></a>

**Note**  
The PostgreSQL engine version 9\.6\.12 with the Aurora PostgreSQL release 1\.5 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.12\. For more information about the improvements in PostgreSQL 9\.6\.12, see [PostgreSQL release 9\.6\.12](https://www.postgresql.org/docs/9.6/release-9-6-12.html)\.

**Topics**
+ [Aurora PostgreSQL release 1\.5\.3](#AuroraPostgreSQL.Updates.20180305.153)
+ [Aurora PostgreSQL release 1\.5\.2](#AuroraPostgreSQL.Updates.20180305.152)
+ [Aurora PostgreSQL release 1\.5\.1](#AuroraPostgreSQL.Updates.20180305.151)
+ [Aurora PostgreSQL release 1\.5\.0](#AuroraPostgreSQL.Updates.20180305.150)

### Aurora PostgreSQL release 1\.5\.3<a name="AuroraPostgreSQL.Updates.20180305.153"></a>

You can find the following improvements in this release\.

**Improvements**

1. Fixed a bug that could cause DB instance restarts\.

1. Fixed a bug that could cause a restart when reads occurred during failovers\. 

1. Fixed a bug that could result in inconsistent metadata\.

### Aurora PostgreSQL release 1\.5\.2<a name="AuroraPostgreSQL.Updates.20180305.152"></a>

You can find the following improvements in this release\.

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

### Aurora PostgreSQL release 1\.5\.1<a name="AuroraPostgreSQL.Updates.20180305.151"></a>

You can find the following improvements in this release\.

**Improvements**

1. Fixed multiple bugs related to I/O prefetching, which caused engine crashes\.

### Aurora PostgreSQL release 1\.5\.0<a name="AuroraPostgreSQL.Updates.20180305.150"></a>

You can find the following improvements in this release\.

**New features**

1. Aurora PostgreSQL now performs I/O prefetching while scanning B\-tree indexes\. This results in significantly improved performance for B\-tree scans over uncached data\.

**Improvements**

1. Addressed numerous issues that caused read nodes to fail to startup while the cluster is under heavy write workload\.

1. Fixed a bug whereby usage of the `aurora_stat_memctx_usage()` function could lead to a crash\.

1. Improved the cache replacement strategy used by table scans to minimize thrashing of the buffer cache\.

## PostgreSQL 9\.6\.11, Aurora PostgreSQL release 1\.4 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.14"></a>

**Note**  
The PostgreSQL engine version 9\.6\.11 with the Aurora PostgreSQL release 1\.4 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.11\. For more information about the improvements in PostgreSQL 9\.6\.11, see [PostgreSQL release 9\.6\.11](https://www.postgresql.org/docs/9.6/release-9-6-11.html)\. 

You can find the following improvements in this release\.

**New features**

1. Support is added for the `pg_similarity` extension version 1\.0\.

1. Aurora PostgreSQL now supports the PostgreSQL [vacuum\_truncate](https://www.postgresql.org/docs/current/sql-createtable.html#RELOPTION-VACUUM-TRUNCATE) storage parameter to manage vacuum truncation for specific tables\. Set this [storage parameter](https://www.postgresql.org/docs/current/sql-createtable.html#SQL-CREATETABLE-STORAGE-PARAMETERS) to false when creating or altering a table to prevent the [VACUUM](https://www.postgresql.org/docs/current/sql-vacuum.html) SQL command from truncating the table's trailing empty pages\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [PostgreSQL 9\.6\.9, Aurora PostgreSQL release 1\.3 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.13)\.

1. Network traffic between the writer and reader nodes is now compressed to reduce network utilization\. This reduces the chance of read node unavailability due to network saturation\.

1. Performance of subtransactions has improved under high concurrency workloads\.

1. An update for the `pg_hint_plan` extension to version 1\.2\.3\.

1. Fixed an issue where on a busy system, a commit with millions of subtransactions \(and sometimes with commit timestamps enabled\) can cause Aurora to crash\.

1. Fixed an issue where an `INSERT` statement with `VALUES` could fail with the message "Attempting to read past EOF of relation"\.

1. An upgrade of the `apg_plan_mgmt` extension to version 1\.0\.1\. For details, see [The Aurora PostgreSQL apg\_plan\_mgmt extension version 1\.0\.1](AuroraPostgreSQL.Extensions.md#AuroraPostgreSQL.Extensions.apg_plan_mgmt.101)\.

   The `apg_plan_mgmt` extension is used with query plan management\. For more about how to install, upgrade, and use the `apg_plan_mgmt` extension, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

## PostgreSQL 9\.6\.9, Aurora PostgreSQL release 1\.3 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.13"></a>

**Note**  
The PostgreSQL engine version 9\.6\.9 with the Aurora PostgreSQL release 1\.3 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

This release of Aurora PostgreSQL is compatible with PostgreSQL 9\.6\.9\. For more information about the improvements in PostgreSQL 9\.6\.9, see [PostgreSQL release 9\.6\.9](https://www.postgresql.org/docs/9.6/release-9-6-9.html)\.

**Topics**
+ [Aurora PostgreSQL release 1\.3\.2](#AuroraPostgreSQL.Updates.20180305.132)
+ [Aurora PostgreSQL release 1\.3\.0](#AuroraPostgreSQL.Updates.20180305.130)

### Aurora PostgreSQL release 1\.3\.2<a name="AuroraPostgreSQL.Updates.20180305.132"></a>

You can find the following improvements in this release\.

**New features**

1. Added the `ProcArrayGroupUpdate` wait event\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Aurora PostgreSQL release 1\.3\.0<a name="AuroraPostgreSQL.Updates.20180305.130"></a>

You can find the following improvements in this release\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [PostgreSQL 9\.6\.8, Aurora PostgreSQL release 1\.2 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.12)\.

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

## PostgreSQL 9\.6\.8, Aurora PostgreSQL release 1\.2 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.12"></a>

**Note**  
The PostgreSQL engine version 9\.6\.8 with the Aurora PostgreSQL release 1\.2 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

For more information about PostgreSQL 9\.6\.8, see [PostgreSQL release 9\.6\.8](https://www.postgresql.org/docs/9.6/release-9-6-8.html)\. 

**Topics**
+ [Aurora PostgreSQL release 1\.2\.2](#AuroraPostgreSQL.Updates.20180305.122)
+ [Aurora PostgreSQL release 1\.2\.0](#AuroraPostgreSQL.Updates.20180305.120)

### Aurora PostgreSQL release 1\.2\.2<a name="AuroraPostgreSQL.Updates.20180305.122"></a>

You can find the following improvements in this release\.

**New features**

1. Added the `ProcArrayGroupUpdate` wait event\.

**Improvements**

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "CLOG segment 123 does not exist: No such file or directory"\.

1. Increased the supported size of IAM passwords to 8KB\.

1. Improved consistency of performance under high throughput write workloads\.

1. Fixed a bug which could cause a read replica to crash during a restart\.

1. Fixed a bug which could cause an error running queries\. The message reported was of the form "SQL ERROR: Attempting to read past EOF of relation"\.

1. Fixed a bug which could cause an increase in memory usage after a restart\.

1. Fixed a bug which could cause a transaction with a large number of subtransactions to fail\.

1. Merged a patch from community PostgreSQL which addresses potential failures when using GIN indexes\. For more information see [ https://git\.postgresql\.org/gitweb/?p=postgresql\.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commit;h=f9e66f2fbbb49a493045c8d8086a9b15d95b8f18)\.

1. Fixed a bug which could cause a snapshot import from RDS for PostgreSQL to fail\.

### Aurora PostgreSQL release 1\.2\.0<a name="AuroraPostgreSQL.Updates.20180305.120"></a>

You can find the following improvements in this release\.

**New features**

1. Introduced the `aurora_stat_memctx_usage()` function\. This function reports internal memory context usage for each PostgreSQL backend\. You can use this function to help determine why certain backends are consuming large amounts of memory\.

**Improvements**

1. This release contains all fixes, features, and improvements present in [PostgreSQL 9\.6\.6 Aurora PostgreSQL release 1\.1 \(unsupported\)](#AuroraPostgreSQL.Updates.20180305.11)\.

1. Updates the following PostgreSQL extensions:
   + `pg_hint_plan` updated to version 1\.2\.2
   + `plv8` updated to version 2\.1\.0

1. Improves efficiency of traffic between writer and reader nodes\.

1. Improves connection establishment performance\.

1. Improve the diagnostic data provided in the PostgreSQL error log when an out\-of\-memory error is encountered\.

1. Multiple fixes to improve the reliability and performance of snapshot import from Amazon RDS for PostgreSQL to Aurora PostgreSQL\-Compatible Edition\.

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

## PostgreSQL 9\.6\.6 Aurora PostgreSQL release 1\.1 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.11"></a>

**Note**  
The PostgreSQL engine version 9\.6\.6 with the Aurora PostgreSQL release 1\.1 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

For more information about PostgreSQL 9\.6\.6 see, [PostgreSQL release 9\.6\.6](https://www.postgresql.org/docs/9.6/static/release-9-6-6.html)\.

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

1. This release contains all fixes, features, and improvements present in [Aurora PostgreSQL release 1\.0\.11](#AuroraPostgreSQL.Updates.20180305.1011)

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

## PostgreSQL 9\.6\.3, Aurora PostgreSQL release 1\.0 \(unsupported\)<a name="AuroraPostgreSQL.Updates.20180305.10"></a>

**Note**  
The PostgreSQL engine version 9\.6\.3 with the Aurora PostgreSQL release 1\.0 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

For more information about PostgreSQL 9\.6\.3 see, [PostgreSQL release 9\.6\.3](https://www.postgresql.org/docs/9.6/release-9-6-3.html)\.

This version includes the following patch releases:

**Topics**
+ [Aurora PostgreSQL release 1\.0\.11](#AuroraPostgreSQL.Updates.20180305.1011)
+ [Aurora PostgreSQL release 1\.0\.10](#AuroraPostgreSQL.Updates.20180305.1010)
+ [Aurora PostgreSQL release 1\.0\.9](#AuroraPostgreSQL.Updates.20180305.109)
+ [Aurora PostgreSQL release 1\.0\.8](#AuroraPostgreSQL.Updates.20180305.108)
+ [Aurora PostgreSQL release 1\.0\.7](#AuroraPostgreSQL.Updates.20180305.107)

### Aurora PostgreSQL release 1\.0\.11<a name="AuroraPostgreSQL.Updates.20180305.1011"></a>

You can find the following improvements in this engine update:

1. Fixes an issue with parallel query processing that can lead to incorrect results\.

1. Fixes an issue with visibility map handling during replication from Amazon RDS for PostgreSQL that can cause the Aurora storage volume to become unavailable\. 

1. Corrects the pg\-repack extension\.

1. Implements improvements to maintain fresh nodes\.

1. Fixes issues that can lead to an engine crash\.

### Aurora PostgreSQL release 1\.0\.10<a name="AuroraPostgreSQL.Updates.20180305.1010"></a>

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

### Aurora PostgreSQL release 1\.0\.9<a name="AuroraPostgreSQL.Updates.20180305.109"></a>

In this engine update, we fix an issue that can cause the Aurora storage volume to become unavailable when importing a snapshot from RDS PostgreSQL that contained uninitialized pages\.

### Aurora PostgreSQL release 1\.0\.8<a name="AuroraPostgreSQL.Updates.20180305.108"></a>

You can find the following improvements in this engine update:

1. Fixes an issue that prevented the engine from starting if the `shared_preload_libraries` instance parameter contained `pg_hint_plan`\. 

1. Fixes the error "Attempt to fetch heap block XXX is beyond end of heap \(YYY blocks\)," which can occur during parallel scans\. 

1. Improves the effectiveness of prefetching on reads for a vacuum\.

1. Fixes issues with snapshot import from RDS PostgreSQL, which can fail if there are incompatible pg\_internal\.init files in the source snapshot\.

1. Fixes an issue that can cause a read node to crash with the message "aurora wal replay process \(PID XXX\) was terminated by signal 11: Segmentation fault"\. This issue occurs when the reader applied a visibility map change for an uncached visibility map page\.

### Aurora PostgreSQL release 1\.0\.7<a name="AuroraPostgreSQL.Updates.20180305.107"></a>

This is the first generally available release of Amazon Aurora PostgreSQL\-Compatible Edition\.