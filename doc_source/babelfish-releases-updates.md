# Babelfish versions<a name="babelfish-releases-updates"></a>

Following, you can find information about Babelfish for Aurora PostgreSQL updates\. Babelfish is an option available with Aurora PostgreSQL version 13\.4 and higher releases\. Updates to Babelfish become available with certain new releases of the Aurora PostgreSQL database engine, as shown in the following table\.


| Aurora PostgreSQL version | Babelfish version | 
| --- | --- | 
|  Aurora PostgreSQL version 13\.5  |  Babelfish version 1\.1\.0  | 
|  Aurora PostgreSQL version 13\.4  |  Babelfish version 1\.0\.0  | 

If your Aurora PostgreSQL has automatic minor version upgrades \(AMVU\) turned on, the cluster is upgraded for you during your specified maintenance window\. By using this approach, you get the new Babelfish release automatically\. To learn more about AMVU, see [Automatic minor version upgrades for PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.Minor)\. 

 If your Aurora PostgreSQL DB cluster isn't set up for AMVU, you can upgrade manually\. For more information about upgrading an Aurora PostgreSQL DB cluster, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 

For information about how to obtain version details for Aurora PostgreSQL or Babelfish, see [Querying Babelfish to find Babelfish details](babelfish-information.md)\. 

**Topics**
+ [Babelfish version 1\.1\.0](#babelfish-releases-updates-bfish110)
+ [Babelfish version 1\.0\.0](#babelfish-releases-updates-bfish100)

## Babelfish version 1\.1\.0<a name="babelfish-releases-updates-bfish110"></a>

This release of Babelfish for Aurora PostgreSQL adds support for the following Microsoft SQL Server functionality and T\-SQL commands:
+ Support for creation of unique indexes or UNIQUE constraints on nullable columns\. To use this capability, you change the `escape_hatch_unique_constraint` to `'ignore'`\. For more information, see [Babelfish escape hatches](babelfish-strict.md#babelfish-escape_hatches)\.
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