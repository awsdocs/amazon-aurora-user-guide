# Babelfish versions<a name="babelfish-releases-updates"></a>

Following, you can find information about Babelfish for Aurora PostgreSQL updates\. Babelfish is an option available with Aurora PostgreSQL version 13\.4 and higher releases\. Updates to Babelfish become available with certain new releases of the Aurora PostgreSQL database engine, as shown in the following table\.


| Aurora PostgreSQL version | Babelfish version | 
| --- | --- | 
|  Aurora PostgreSQL version 13\.6  |  Babelfish version 1\.2\.0  | 
|  Aurora PostgreSQL version 13\.5  |  Babelfish version 1\.1\.0  | 
|  Aurora PostgreSQL version 13\.4  |  Babelfish version 1\.0\.0  | 

If your Aurora PostgreSQL has automatic minor version upgrades \(AMVU\) turned on, the cluster is upgraded for you during your specified maintenance window\. By using this approach, you get the new Babelfish release automatically\. To learn more about AMVU, see [Automatic minor version upgrades for PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.Minor)\. 

 If your Aurora PostgreSQL DB cluster isn't set up for AMVU, you can upgrade manually\. For more information about upgrading an Aurora PostgreSQL DB cluster, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 

For information about how to obtain version details for Aurora PostgreSQL or Babelfish, see [Querying Babelfish to find version details](babelfish-information.md)\. 

**Topics**
+ [Babelfish version 1\.2\.0](#babelfish-releases-updates-bfish120)
+ [Babelfish version 1\.1\.0](#babelfish-releases-updates-bfish110)
+ [Babelfish version 1\.0\.0](#babelfish-releases-updates-bfish100)

For a list of features supported in each Babelfish release, see [Supported functionality in Babelfish by version](babelfish-compatibility.supported-functionality-table.md)\.

## Babelfish version 1\.2\.0<a name="babelfish-releases-updates-bfish120"></a>

This release of Babelfish for Aurora PostgreSQL is supported by Aurora PostgreSQL version 13\.6 and higher releases\. In addition to the new features listed in this section, Babelfish for Aurora PostgreSQL version 1\.2\.0 adds several features that currently have limited implementations\. These features are available for use but don't yet have complete parity with T\-SQL syntax or Microsoft SQL Server\. For more information, see [Features with limited implementation](babelfish-compatibility.md#babelfish-compatibility.tsql.limited-implementation)\. 

**New features**
+ Casing \(upper\-case, lower\-case\) of column names as created with T\-SQL is now retained\. That is, `SELECT * FROM table` returns the column names using the same casing as used when the table was created at the TDS endpoint\.
+ INSTEAD\-OF triggers are now supported on tables\. This support is for tables only, not views\. 
+ Support for the following system\-defined global variables:
  + @@DBTS
  + @@LOCK\_TIMEOUT
  + @@SERVICENAME 
+ Support for syntax SET LOCK\_TIMEOUT 
+ Support for the following datatypes:
  + TIMESTAMP
  + ROWVERSION
+ Support for the following built\-in functions:
  + COLUMNS\_UPDATED
  + UPDATE
  + FULLTEXTSERVICEPROPERTY
  + ISJSON
  + JSON\_QUERY
  + JSON\_VALUE
  + HAS\_DBACCESS
  + SUSER\_SID
  + SUSER\_SNAME
  + IS\_SRVROLEMEMBER
+ Full support for the CHECKSUM function\. This function now supports \* and multiple columns \(`CHECKSUM ( * | expression [ ,...n ] )`\. 
+ Full support for the SCHEMA\_ID function\. This function can now be used without any arguments \(`SCHEMA_ID ( [ schema_name ] )`\)\. 
+ Support for DROP IF EXISTS with SCHEMA, DATABASE, and USER objects\. 
+ Support for the following values for CONNECTIONPROPERTY: 
  +  physical\_net\_transport
  + client\_net\_address
+ Support for the following values for SERVERPROPERTY:
  + EditionID
  + EngineEdition
  + LicenseType
  + ProductVersion
  + ProductMajorVersion
  + ProductMinorVersion
  + IsIntegratedSecurityOnly
  + IsLocalDB
  + IsAdvancedAnalyticsInstalled
  + IsBigDataCluster
  + IsPolyBaseInstalled
  + IsFullTextInstalled
  + IsXTPSupported
+ Support for the following catalogs:
  + sys\.dm\_os\_host\_info
  + sys\.dm\_exec\_sessions
  + sys\.dm\_exec\_connections
  + sys\.endpoints
  + sys\.table\_types
  + sys\.database\_principals
  + sys\.sysprocesses 
  + sys\.sysconfigures
  + sys\.syscurconfigs
  + sys\.configurations
+ Support for the following INFORMATION\_SCHEMA catalogs:
  + TABLES
  + COLUMNS
  + DOMAINS
  + TABLE\_CONSTRAINTS
+ Support for the following system stored procedures:
  + sp\_table\_privileges
  + sp\_column\_privileges
  + sp\_special\_columns
  + sp\_fkeys
  + sp\_pkeys
  + sp\_stored\_procedures
  + xp\_qv
  + sp\_describe\_undeclared\_parameters
  + sp\_helpuser
+ Limited support for creating, altering, and dropping database principals \(USER objects\)\. Limitations for CREATE/ALTER/DROP syntax with USER objects are as follows:
  + For CREATE USER, you can specify the FOR/FROM LOGIN and DEFAULT\_SCHEMA options only\.
  + For ALTER USER, you can specify DEFAULT\_SCHEMA option only\.
+ Support for granting and revoking \(GRANT/REVOKE\) permisions for database principals only \(not database roles\)\. Support includes GRANT OPTION and REVOKE\.\.CASCADE options for the following:
  + SELECT
  + INSERT
  + UPDATE
  + DELETE
  + REFERENCES
  + EXECUTE
  + ALL \[PRIVILEGES\]
+ Support for WITH AUTHORIZATION on CREATE SCHEMA\.
+ Support for the following new escape hatches and escape hatch functionality:
  + Ability to restore the default settings for escape hatches\. You can restore all the default settings for your Babelfish DB instance by passing `default` as the second argument to the `sp_babelfish_configure` stored procedure\.
  + Support for a new escape hatch, `escape_hatch_ignore_dup_key` \(default=strict\)\. This escape hatch controls the IGNORE\_DUP\_KEY option in CREATE/ALTER TABLE and CREATE INDEX statements\. When IGNORE\_DUP\_KEY=ON, an error is raised unless escape\_hatch\_ignore\_dup\_key is set to `'ignore'`\. 
  + Support for the `ignore` option on the `escape_hatch_storage_options` escape hatch\. When this escape hatch is set to `ignore`, Babelfish ignores errors raised in the following cases:
    + Ignores errors raised in the ON clause in a CREATE DATABASE statement\. 
    + Ignores errors raised by CREATE INDEX when used with SORT\_IN\_TEMPDB, DROP\_EXISTING, or ONLINE options\. 

  For more information about escape hatches, see [Managing Babelfish error handling](babelfish-strict.md)\.
+ The msdb system database is always present, and has dbid=4\.

For more information, see the [Babelfish for PostgreSQL](https://babelfishpg.org/docs) documentation\.

## Babelfish version 1\.1\.0<a name="babelfish-releases-updates-bfish110"></a>

This release of Babelfish for Aurora PostgreSQL is supported by Aurora PostgreSQL version 13\.5 and higher releases\. This release of Babelfish for Aurora PostgreSQL adds support for the following Microsoft SQL Server functionality and T\-SQL commands:
+ Support for creation of unique indexes or UNIQUE constraints on nullable columns\. To use this capability, you change the `escape_hatch_unique_constraint` to `'ignore'`\. For more information, see [Modifying Babelfish escape hatch settings](babelfish-strict.md#babelfish-escape_hatches)\.
+ Support for referencing transition tables from triggers with multiple DML actions\.
+ Support for identifiers that have leading dot characters\.
+ Support for the COLUMNPROPERTY function \(limited to CharMaxLen and AllowsNull properties\)\. 
+ Support for the following system\-defined @@ variables:
  + @@CURSOR\_ROWS
  + @@LOCK\_TIMEOUT
  + @@MAX\_CONNECTIONS
  + @@MICROSOFTVERSION
  + @@NESTLEVEL
  + @@PROCID
+ Support for the following built\-in functions: 
  + CHOOSE
  + CONCAT\_WS
  + CURSOR\_STATUS
  + DATEFROMPARTS
  + DATETIMEFROMPARTS
  + ORIGINAL\_LOGIN
  + SCHEMA\_NAME \(now fully supported\)
  + SESSION\_USER
  + SQUARE
  + TRIGGER\_NESTLEVEL supported \(but only without arguments\)
+ Support for the following system stored procedures: 
  + sp\_columns
  + sp\_columns\_100
  + sp\_columns\_managed
  + sp\_cursor
  + sp\_cursor\_list
  + sp\_cursorclose
  + sp\_cursorexecute
  + sp\_cursorfetch
  + sp\_cursoropen
  + sp\_cursoroption
  + sp\_cursorprepare
  + sp\_cursorprepexec
  + sp\_cursorunprepare
  + sp\_databases
  + sp\_datatype\_info
  + sp\_datatype\_info\_100
  + sp\_describe\_cursor
  + sp\_describe\_first\_result\_set
  + sp\_describe\_undeclared\_parameters
  + sp\_oledb\_ro\_usrname
  + sp\_pkeys
  + sp\_prepare
  + sp\_statistics
  + sp\_statistics\_100
  + sp\_tablecollations\_100
  + sp\_tables
  + sp\_unprepare

For more information, see the [Babelfish for PostgreSQL](https://babelfishpg.org/docs) documentation\.

## Babelfish version 1\.0\.0<a name="babelfish-releases-updates-bfish100"></a>

Version 1\.0\.0 is the initial release of Babelfish for Aurora PostgreSQL\. 