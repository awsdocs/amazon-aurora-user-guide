# Differences between Aurora PostgreSQL with Babelfish and SQL Server<a name="babelfish-compatibility"></a>

Babelfish provides support for T\-SQL and Microsoft SQL Server behavior by supporting SQL Server data types, syntax, and functions for Aurora PostgreSQL\. This approach allows Aurora to support both Aurora PostgreSQL and SQL Server SQL dialects\. Also, Babelfish supports the SQL Server wire\-level protocol \(TDS\), allowing a SQL Server application to communicate natively with Aurora PostgreSQL\. Doing this helps migrate database objects, stored procedures, and application code with fewer changes\.

Although Babelfish doesn't offer complete support for T\-SQL, you can use Aurora PostgreSQL SQL commands to perform many of the tasks normally handled by these commands\. For example, suppose that you regularly use a specific T\-SQL command that isn't supported by Babelfish\. In this case, you can connect to the Aurora PostgreSQL port and use a PostgreSQL SQL command instead\. For more information, see [SQL Commands](https://www.postgresql.org/docs/14/sql-commands.html) in the PostgreSQL documentation\.

Aurora PostgreSQL offers functionality to replace many commonly used SQL Server features\. Some examples of SQL Server functionality that can be replaced by the PostgreSQL functionality available in Aurora PostgreSQL follow\. In the table, references are to the PostgreSQL documentation\. 


| SQL Server functionality | Comparable PostgreSQL functionality | 
| --- | --- | 
|  SQL Server bulk copy  |  PostgreSQL [COPY](https://www.postgresql.org/docs/current/populate.html) \(optimized for fast data loading\)  | 
|  SQL Server GROUP BY clauses \(not supported in Babelfish\)  |  PostgreSQL [GROUPING SETS](https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUP)  | 
|  SQL Server JSON support  |  PostgreSQL [JSON functions and operators](https://www.postgresql.org/docs/current/functions-json.html)  | 
|  SQL Server XML support  |  PostgreSQL [XML functions](https://www.postgresql.org/docs/current/functions-xml.html)  | 
|  SQL Server full\-text search  |  PostgreSQL [full\-text search](https://www.postgresql.org/docs/current/textsearch.html)  | 
|  SQL Server GEOGRAPHY data type  |  [PostGIS](https://postgis.net/) extension \(for working with geographical data\)  | 

To help with cluster management in Aurora PostgreSQL, you can use its scalability, high\-availability with failover support, and built\-in replication\. For more information about these capabilities, see [Managing performance and scaling for Aurora DB clusters](Aurora.Managing.Performance.md), [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md), and [Replication with Amazon Aurora](Aurora.Replication.md)\. You also have access to other AWS tools and utilities:
+ [ Amazon CloudWatch](http://aws.amazon.com/cloudwatch/) is a monitoring and observability service that provides you with data and actionable insights\.
+ [ Amazon RDS Performance Insights](http://aws.amazon.com/rds/performance-insights/) is a database performance tuning and monitoring feature that helps you quickly assess the load on your database\.
+ [ Amazon RDS Multi\-AZ deployments](http://aws.amazon.com/rds/features/multi-az/) provide enhanced availability and durability for your database cluster\.
+ [ Amazon RDS global databases](http://aws.amazon.com/rds/aurora/global-database/) allow a single Amazon Aurora database to span multiple AWS Regions, offering scalable, cross\-Region replication\.
+ Automatic software patching keeps your database up\-to\-date with the latest security and feature patches when they become available\.
+ [Overview of Amazon RDS event notification](USER_Events.overview.md) Amazon RDS events notify you by email or SMS message of important database events, such as an automated failover\.

**Topics**
+ [T\-SQL limitations and unsupported functionality](#babelfish-compatibility.tsql.limitations)
+ [Unsupported functionality in Babelfish](#babelfish-compatibility.tsql.limitations-unsupported)

## T\-SQL limitations and unsupported functionality<a name="babelfish-compatibility.tsql.limitations"></a>

Following, you can find a table of limitations or partially supported T\-SQL syntax for Babelfish\. Unless otherwise specified, these limitations apply to Babelfish 1\.0\.0\. For more information about Babelfish releases, see [Babelfish versions](babelfish-releases-updates.md)\. 


| Functionality or syntax | Notes | 
| --- | --- | 
| @@version | The format of the value returned by `@@version` is slightly different from the value returned by SQL Server\. Your code might not work correctly if it depends on the formatting of `@@version`\. | 
| Aggregate functions \(partially supported\) | APPROX\_COUNT\_DISTINCT, CHECKSUM\_AGG, GROUPING\_ID, ROWCOUNT\_BIG, STDEV, STDEVP, VAR, and VARP aren't supported\. | 
|  ALTER TABLE  | Supports adding or dropping a single column or constraint only\.  | 
| Assembly modules and SQL Common Language Runtime \(CLR\) routines  | Functionality related to assembly modules and CLR routines isn't supported\. | 
| BACKUP statement | Aurora PostgreSQL snapshots of a database are dissimilar to backup files created in SQL Server\. Also, the granularity of when a backup and restore occurs might be different between SQL Server and Aurora PostgreSQL\. | 
| Blank column names with no column alias | The `sqlcmd` and `psql` utilities handle columns with blank names differently: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/babelfish-compatibility.html)  | 
| Collation, index on type dependent on the ICU library | An index on a user\-defined type that depends on the International Components for Unicode \(ICU\) collation library \(the library used by Babelfish\) isn't invalidated when the version of the library is changed\. For more information about collations, see [Babelfish collation support](babelfish-collations.md)\. | 
| COLLATIONPROPERTY function | Collation properties are implemented only for the supported Babelfish BBF collations\. For more information about collations, see [Babelfish collation support](babelfish-collations.md)\. | 
| Column default | When creating a column default, the constraint name is ignored\. To drop a column default, use the following syntax: `ALTER TABLE...ALTER COLUMN..DROP DEFAULT...` | 
| Column name case | Column names are stored as lowercase in the PostgreSQL catalogs and are returned to the client in lowercase if you run a `SELECT` statement\. In general, all schema identifiers are stored in lowercase in the PostgreSQL catalogs\. For more information, see [SQL\-SYNTAX\-IDENTIFIERS](https://www.postgresql.org/docs/current/sql-syntax-lexical.html#SQL-SYNTAX-IDENTIFIERS) in the PostgreSQL documentation\. | 
| Column attributes | ROWGUIDCOL, SPARSE, FILESTREAM, and MASKED aren't supported\. | 
| CONNECTIONPROPERTY function | The unsupported properties include `local_net_address`, `client_net_address`, and `physical_net_transport`\. | 
| Constraints | PostgreSQL doesn't support turning on and turning off individual constraints\. The statement is ignored and a warning is raised\. | 
| Constraints created with DESC \(descending\) columns | Constraints are created with ASC \(ascending\) columns\. | 
| Constraints with IGNORE\_DUP\_KEY | Constraints are created without this property\. | 
| Contained databases | Contained databases with logins authenticated at the database level rather than at the server level aren't supported\. | 
| CREATE, ALTER, DROP SERVER ROLE |  ALTER SERVER ROLE is supported only for `sysadmin`\. All other syntax is unsupported\. The T\-SQL user in Babelfish has an experience that is similar to SQL Server for the concepts of a login \(server principal\), a database, and a database user \(database principal\)\. Only the `dbo` user is available in Babelfish user databases\. To operate as the `dbo` user, a login must be a member of the server\-level `sysadmin` role \(`ALTER SERVER ROLE sysadmin ADD MEMBER login`\)\. Logins without `sysadmin` role can currently access only `master` and `tempdb` as the `guest` user\. Currently, because Babelfish supports only the `dbo` user in user databases, all application users must use a login that is a `sysadmin` member\. You can't create a user with lesser privileges, such as read\-only on certain tables\.  | 
| CREATE DATABASE case\-sensitive collation  | Case\-sensitive collations aren't supported with the CREATE DATABASE statement\. | 
| CREATE DATABASE keywords and clauses | Options except COLLATE and CONTAINMENT=NONE aren't supported\. The COLLATE clause is accepted and is always set to the value of `babelfishpg_tsql.server_collation_name`\. | 
| CREATE SCHEMA\.\.\. supporting clauses | You can use the CREATE SCHEMA command to create an empty schema\. Use additional commands to create schema objects\. | 
| CREATE USER | This syntax isn't supported\. The PostgreSQL statement CREATE USER doesn't create a user that is equivalent to the SQL Server CREATE USER syntax\. | 
| CREATE, ALTER LOGIN clauses are supported with limited syntax | The CREATE LOGIN\.\.\. PASSWORD clause, \.\.\.DEFAULT\_DATABASE clause, and \.\.\.DEFAULT\_LANGUAGE clause are supported\. The ALTER LOGIN\.\.\. PASSWORD clause is supported, but ALTER LOGIN\.\.\. OLD\_PASSWORD clause isn't supported\. Only a login that is a sysadmin member can modify a password\. | 
| LOGIN objects | All options for LOGIN objects except: PASSWORD, DEFAULT\_DATABASE, ENABLE, DISABLE | 
| CROSS APPLY | Lateral joins aren't supported\. | 
| Cross\-database object references | Objects with three\-part names aren't supported\. For more information, see: [Using Babelfish with a single database or multiple databases](babelfish.md#babelfish-single_vs_multi_db)\. | 
| Cursors \(updatable\) | Updatable cursors aren't supported\. | 
| Cursors \(global\) | GLOBAL cursors aren't supported\. | 
| Cursor \(fetch behaviors\) | The following cursor fetch behaviors aren't supported: FETCH PRIOR, FIRST, LAST, ABSOLUTE, abd RELATIVE | 
| Cursor\-typed \(variables and parameters\) | Cursor\-typed variables and parameters aren't supported\. | 
| Cursor options | SCROLL, KEYSET, DYNAMIC, FAST\_FORWARD, SCROLL\_LOCKS, OPTIMISTIC, TYPE\_WARNING, and FOR UPDATE | 
|  Database ID values are different on Babelfish  |  The master and tempdb databases will not be database IDs 1 and 2\.  | 
| Data encryption | Data encryption isn't supported\. | 
| DBCC commands  | Microsoft SQL Server Database Console Commands \(DBCC\) aren't supported\. | 
| DROP IF EXISTS | This syntax isn't supported for USER and SCHEMA objects\. It's supported for the objects TABLE, VIEW, PROCEDURE, FUNCTION, and DATABASE\. | 
| DROP INDEX | This syntax is supported only in the form `DROP index_name ON table_name`\. | 
| DROP statements that drop multiple objects | This functionality is supported only for tables, views, functions, and procedures\. | 
| Encryption | Built\-in functions and statements don't support encryption\. | 
| ENCRYPT\_CLIENT\_CERT connections | Client certificate connections aren't supported\. | 
| EXECUTE AS statement | This statement isn't supported\. | 
| EXECUTE AS SELF clause | This clause isn't supported in functions, procedures, or triggers\. | 
| CREATE\.\.\. EXECUTE AS OWNER clause | EXECUTE AS OWNER or CALLER is supported for permission, but not for name resolution\. | 
| EXECUTE AS USER clause | This clause isn't supported in functions, procedures, or triggers\. | 
| EXECUTE with AS LOGIN or AT option | This syntax isn't supported\. | 
| Foreign key constraints referencing database name | Foreign key constraints that reference the database name aren't supported\. | 
| Full\-text search | Full\-text search built\-in Functions and statements aren't supported\. | 
| Function declarations with greater than 100 parameters | Function declarations that contain more than 100 parameters aren't supported\. | 
| Function calls that include DEFAULT as a parameter value | DEFAULT isn't a supported parameter value for a function call\. | 
| Function calls that include :: | Function calls that include :: aren't supported\. | 
| Functions, externally defined | External functions, including SQL CLR functions, aren't supported\. | 
| GEOMETRY | This data type and all associated functionality isn't supported\. | 
| GEOGRAPHY | This data type and all associated functionality isn't supported\. | 
| Global temporary tables \(tables with names that start with \#\#\) | Global temporary tables aren't supported\. | 
| Graph functionality | All SQL graph functionality isn't supported\. | 
| HASHBYTES function | The only supported algorithms are: MD5, SHA1, and SHA256 | 
| HIERARCHYID | The data type and methods aren't supported\. | 
| Hints | Hints aren't supported for joins, queries, or tables\. | 
| Identifiers exceeding 63 characters | PostgreSQL supports a maximum of 63 characters for identifiers\. Babelfish converts identifiers longer than 63 characters to a name that includes a hash of the original name\. | 
| Identifiers with leading dot characters | Identifiers that start with a `.` aren't supported in version 1\.0\.0, but are supported in Babelfish 1\.1\.0\. | 
| Identifiers \(variables or parameters\) with multiple leading @ characters | Identifiers that start with more than one leading `@` aren't supported\. | 
| Identifiers, table or column names that contain @ or \]\] characters | Table or column names that contain an `@` sign or square brackets aren't supported\. | 
| IDENTITY columns support | IDENTITY columns are supported for data types tinyint, smallint, int, bigint\. numeric, and decimal\. SQL Server supports precision to 38 places for data types `numeric` and `decimal` in IDENTITY columns\.PostgreSQL supports precision to 19 places for data types `numeric` and `decimal` in IDENTITY columns\. | 
| Indexes with IGNORE\_DUP\_KEY | Syntax that creates an index that includes IGNORE\_DUP\_KEY creates an index as if this property is omitted\. | 
| Indexes with more than 32 columns | An index can't include more than 32 columns\. Included index columns count toward the maximum in PostgreSQL but not in SQL Server\. | 
| INFORMATION\_SCHEMA catalog | Information schema views aren't supported\.   | 
| Inline indexes | Inline indexes aren't supported\. | 
| Indexes \(clustered\) | Clustered indexes are created as if NONCLUSTERED was specified\. | 
| Index clauses | The following clauses are ignored: FILLFACTOR, ALLOW\_PAGE\_LOCKS, ALLOW\_ROW\_LOCKS, PAD\_INDEX, STATISTICS\_NORECOMPUTE, OPTIMIZE\_FOR\_SEQUENTIAL\_KEY, SORT\_IN\_TEMPDB, DROP\_EXISTING, ONLINE, COMPRESSION\_DELAY, MAXDOP, and DATA\_COMPRESSION | 
| Invoking a procedure whose name is in a variable | Using a variable as a procedure name isn't supported\. | 
| Materialized views | Materialized views aren't supported\. | 
| NEWSEQUENTIALID function | Implemented as NEWID; sequential behavior isn't guaranteed\. When calling `NEWSEQUENTIALID`, PostgreSQL generates a new GUID value\. | 
| NEXT VALUE FOR sequence clause | This syntax isn't supported\. | 
| NOT FOR REPLICATION clause | This syntax is accepted and ignored\.  | 
| ODBC escape functions | ODBC escape functions aren't supported\. | 
| OUTER APPLY | SQL Server lateral joins aren't supported\. PostgreSQL provides SQL syntax that allows a lateral join, but the behavior isn't identical\. For more information about PostgreSQL lateral joins, see [the PostgreSQL documentation](https://www.postgresql.org/docs/14/queries-table-expressions.html)\. | 
| OUTPUT clause is supported with the following limitations | OUTPUT and OUTPUT INTO aren't supported in the same DML query\. References to non\-target table of UPDATE or DELETE operations in an OUTPUT clause aren't supported\. OUTPUT\.\.\. DELETED \*, INSERTED \* aren't supported in the same query\. | 
| Partitioning | Table and index partitioning isn't supported\. | 
| Procedure calls that includes DEFAULT as a parameter value | DEFAULT isn't a supported parameter value\. | 
| Procedure declarations with more than 100 parameters | Declarations with more than 100 parameters aren't supported\. | 
| Procedures, externally defined | Externally defined procedures, including SQL CLR procedures, aren't supported\. | 
| Procedure versioning | Procedure versioning isn't supported\. | 
| Procedures WITH RECOMPILE | WITH RECOMPILE \(when used in conjunction with the DECLARE and EXECUTE statements\) isn't supported\. | 
| Procedure or function parameter limit | Babelfish supports a maximum of 100 parameters for a procedure or function\. | 
| Remote object references | Objects with four\-part names aren't supported\.\. For more information, see: [Configuring a database for Babelfish](babelfish-configuration.md)\. | 
| Server\-level roles other than `sysadmin` | Server\-level roles \(other than `sysadmin`\) aren't supported\. | 
| Database\-level roles other than `db_owner` | Database\-level roles other than db\_owner aren't supported\. | 
| RESTORE statement | Aurora PostgreSQL snapshots of a database are dissimilar to backup files created in SQL Server\. Also, the granularity of when the backup and restore occurs might be different between SQL Server and Aurora PostgreSQL\. | 
| ROLLBACK: table variables don't support transactional rollback | Processing might be interrupted if a rollback occurs in a session with table variables\. | 
| ROWGUIDCOL | This clause is currently ignored\. Queries referencing `$GUIDGOL` cause a syntax error\. | 
| Row\-level security | Row\-level security with CREATE SECURITY POLICY and inline table\-valued functions isn't supported\. | 
| ROWSET functions | The following ROWSET functions aren't supported: OPENXML, OPENJSON, OPENROWSET, OPENQUERY, OPENDATASOURCE | 
| SELECT\.\.\. FOR XML PATH, ELEMENTS | Syntax is supported without the ELEMENTS clause\. | 
| SELECT\.\.\. FOR XML RAW, ELEMENTS | Syntax is supported without the ELEMENTS clause\. | 
| SERVERPROPERTY | Unsupported properties: BuildClrVersion, ComparisonStyle, ComputerNamePhysicalNetBIOS, EditionID, HadrManagerStatus, InstanceDefaultDataPath, InstanceDefaultLogPath, InstanceName, IsAdvancedAnalyticsInstalled, IsBigDataCluster, IsClustered, IsFullTextInstalled, IsHadrEnabled, IsIntegratedSecurityOnly, IsLocalDB, IsPolyBaseInstalled, IsXTPSupported, LCID, LicenseType, MachineName, NumLicenses, ProcessID, ProductBuild, ProductBuildType, ProductLevel, ProductUpdateLevel, ProductUpdateReference, ResourceLastUpdateDateTime, ResourceVersion, ServerName, SqlCharSet, SqlCharSetName, SqlSortOrder, SqlSortOrderName, FilestreamShareName, FilestreamConfiguredLevel, and FilestreamEffectiveLevel | 
| Service broker functionality | Service broker functionality isn't supported\. | 
| SESSIONPROPERTY | Unsupported properties: ANSI\_NULLS, ANSI\_PADDING, ANSI\_WARNINGS, ARITHABORT, CONCAT\_NULL\_YIELDS\_NULL, and NUMERIC\_ROUNDABORT | 
| SET LANGUAGE | This syntax isn't supported with any value other than `english` or `us_english`\. | 
| SEQUENCE object support | SEQUENCE objects are supported for the data types tinyint, smallint, int, bigint, numeric, and decimal\. Aurora PostgreSQL supports precision to 19 places for data types numeric and decimal in a SEQUENCE\. | 
| SET NUMERIC\_ROUNDABORT ON | This setting isn't supported\. | 
| SP\_CONFIGURE | This system stored procedure isn't supported\. | 
| SQL keyword SPARSE | The keyword SPARSE is accepted and ignored\. | 
| SQL keyword clause `ON filegroup` | This clause is currently ignored\. | 
| SQL keywords `CLUSTERED` and `NONCLUSTERED` for indexes and constraints | Babelfish accepts and ignores the `CLUSTERED` and `NONCLUSTERED` keywords\. | 
| System\-defined @@ variables | Version 1\.0\.0 supports the following system\-defined @@variables only: @@DATEFIRST, @@ERROR, @@FETCH\_STATUS, @@IDENTITY, @@MAX\_PRECISION, @@ROWCOUNT, @@SERVERNAME, @@SPID, @@TRANCOUNT, and @@VERSION\. Version 1\.1\.0 adds support for @@CURSOR\_ROWS, @@LOCK\_TIMEOUT, @@MAX\_CONNECTIONS, @@MICROSOFTVERSION, @@NESTLEVEL, and @@PROCID\.  | 
| `sysdatabases.cmptlevel` | `sysdatabases.cmptlevel` are always NULL\. | 
| System\-provided stored procedures are partially supported | Babelfish version 1\.0\.0 supported these three stored procedures only: sp\_getapplock, sp\_helpdb, and sp\_releaseapplock\. Babelfish 1\.1\.0 adds support for the following: sp\_columns, sp\_columns\_100, sp\_columns\_managed, sp\_cursor, sp\_cursor\_list, sp\_cursorclose, sp\_cursorexecute, sp\_cursorfetch, sp\_cursoropen, sp\_cursoroption, sp\_cursorprepare, sp\_cursorprepexec, sp\_cursorunprepare, sp\_databases, sp\_datatype\_info, sp\_datatype\_info\_100, sp\_describe\_cursor, sp\_describe\_first\_result\_set, sp\_describe\_undeclared\_parameters, sp\_oledb\_ro\_usrname, sp\_pkeys, sp\_prepare, sp\_statistics, sp\_statistics\_100, sp\_tablecollations\_100, sp\_tables, sp\_unprepare, and sp\_updatestats\. | 
| Table value constructor syntax \(FROM clause\) | The unsupported syntax is for a derived table constructed with the FROM clause\. | 
| tempdb isn't reinitialized at restart | Permanent objects \(like tables and procedures\) created in tempdb aren't removed when the database is restarted\. | 
| Temporal tables | Temporal tables aren't supported\. | 
| Temporary procedures aren't dropped automatically | This functionality isn't supported\. | 
| TEXTIMAGE\_ON filegroup | Babelfish ignores the `TEXTIMAGE_ON` *`filegroup`* clause\. | 
| Time precision | Babelfish supports 6\-digit precision for fractional seconds\. No adverse effects are anticipated with this behavior\. | 
| Transaction isolation levels | READUNCOMMITTED is treated the same as READCOMMITTED\. REPEATABLEREAD, and SERIALIZABLE aren't supported\. | 
| TIMESTAMP data type | This data type isn't supported\. The SQL Server TIMESTAMP type is unrelated to PostgreSQL TIMESTAMP\. | 
| Triggers, externally defined | These triggers aren't supported, including SQL Common Language Runtime \(CLR\)\. | 
| Trigger for multiple DML actions cannot reference transition tables | Triggers that reference multiple DML actions can't reference transition tables in version 1\.0\.0 of Babelfish\. Version 1\.1\.0 provides support for this functionality\.  | 
| Unquoted string values in stored procedure calls and default values | String parameters to stored procedure calls, and defaults for string parameters in CREATE PROCEDURE, are not supported\. | 
| Virtual computed columns \(non\-persistent\) | Virtual computed columns are created as persistent\. | 
| WITH ENCRYPTION clause | This syntax isn't supported for functions, procedures, triggers, or views\. | 
| WITHOUT SCHEMABINDING clause | This clause isn't supported in functions, procedures, triggers, or views\. The object is created, but as if WITH SCHEMABINDING was specified\. | 
| XML data type with schema \(xmlschema\) | XML type without schema is supported\. | 
| XML indexes | XML indexes aren't supported\. | 
| XML methods | XML methods aren't supported, including \.VALUES, \.NODES, and other methods\. | 
| XPATH expressions | This syntax isn't supported\. | 
| WITH XMLNAMESPACES construct | This syntax isn't supported\. | 

## Unsupported functionality in Babelfish<a name="babelfish-compatibility.tsql.limitations-unsupported"></a>

In the following lists, you can find functionality that isn't supported in Babelfish version 1\.0\.0\. For information about updates to Babelfish, see [Babelfish versions](babelfish-releases-updates.md)\. 

### Settings that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list8"></a>

The following settings aren't supported:
+ SET ANSI\_NULL\_DFLT\_OFF ON
+  SET ANSI\_NULL\_DFLT\_ON OFF
+  SET ANSI\_PADDING OFF
+  SET ANSI\_WARNINGS OFF
+  SET ARITHABORT OFF
+  SET ARITHIGNORE ON
+  SET CURSOR\_CLOSE\_ON\_COMMIT ON
+ SET NUMERIC\_ROUNDABORT ON

### Commands for which certain functionality isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list1"></a>

Certain functionality for the following commands isn't supported:
+ ADD SIGNATURE
+  ALTER DATABASE, ALTER DATABASE SET
+  CREATE, ALTER, DROP AUTHORIZATION
+  CREATE, ALTER, DROP AVAILABILITY GROUP
+  CREATE, ALTER, DROP BROKER PRIORITY
+  CREATE, ALTER, DROP COLUMN ENCRYPTION KEY
+  CREATE, ALTER, DROP DATABASE ENCRYPTION KEY
+  CREATE, ALTER, DROP, BACKUP CERTIFICATE
+  CREATE AGGREGATE
+  CREATE CONTRACT
+  GRANT

### Column names that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list7"></a>

The following column names aren't supported:
+ $IDENTITY
+ $ROWGUID
+ IDENTITYCOL

### Data types that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list6"></a>

The following data types aren't supported:
+ ROWVERSION
+ ROWVERSION data type
+ TIMESTAMP data type\. The SQL Server TIMESTAMP date is unrelated to PostgreSQL TIMESTAMP\.

### Object types that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list3"></a>

The following object types aren't supported:
+ COLUMN MASTER KEY
+ CREATE, ALTER EXTERNAL DATA SOURCE
+ CREATE, ALTER, DROP DATABASE AUDIT SPECIFICATION
+ CREATE, ALTER, DROP EXTERNAL LIBRARY
+  CREATE, ALTER, DROP SERVER AUDIT
+  CREATE, ALTER, DROP SERVER AUDIT SPECIFICATION
+  CREATE, ALTER, DROP, OPEN/CLOSE SYMMETRIC KEY
+  CREATE, DROP DEFAULT
+  CREDENTIAL
+  CRYPTOGRAPHIC PROVIDER
+  DIAGNOSTIC SESSION
+  Indexed views
+  SERVICE MASTER KEY
+  SYNONYM
+  USER

### Functions that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list4"></a>

The following functions aren't supported:
+  CERTENCODED function
+  CERTID function
+  CERTPRIVATEKEY function
+  CERTPROPERTY function
+  COLUMNPROPERTY
+  EVENTDATA function
+  GET\_TRANSMISSION\_STATUS
+  LOGINPROPERTY function
+  OBJECTPROPERTY
+  OBJECTPROPERTYEX
+  OPENXML
+  TYPEPROPERTY function

### Syntax for which certain functionality isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list2"></a>

Certain functionality for the following syntax isn't supported:
+ ALTER SERVICE, BACKUP/RESTORE SERVICE MASTER KEY clause
+  BEGIN DISTRIBUTED TRANSACTION
+  CREATE EXTERNAL TABLE
+  CREATE TABLE\.\.\. GRANT clause
+  CREATE TABLE\.\.\. IDENTITY clause
+  CREATE, ALTER, DROP APPLICATION ROLE
+  CREATE, ALTER, DROP ASSEMBLY
+  CREATE, ALTER, DROP ASYMMETRIC KEY
+  CREATE, ALTER, DROP EVENT SESSION
+  CREATE, ALTER, DROP EXTERNAL RESOURCE POOL
+  CREATE, ALTER, DROP FULLTEXT CATALOG
+  CREATE, ALTER, DROP FULLTEXT INDEX
+  CREATE, ALTER, DROP FULLTEXT STOPLIST
+  CREATE, ALTER, DROP QUEUE
+  CREATE, ALTER, DROP RESOURCE GOVERNOR
+  CREATE, ALTER, DROP ROUTE
+  CREATE, ALTER, DROP SERVICE
+  CREATE, ALTER, DROP WORKLOAD GROUP
+ CREATE, ALTER, DROP, OPEN/CLOSE, BACKUP/RESTORE MASTER KEY
+ CREATE/DROP RULE

### Syntax that isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list5"></a>

The following syntax isn't supported:
+  ALTER DATABASE
+  ALTER DATABASE SCOPED CONFIGURATION
+  ALTER DATABASE SCOPED CREDENTIAL
+  ALTER DATABASE SET HADR
+  ALTER FUNCTION
+  ALTER INDEX
+  ALTER PROCEDURE statement
+  ALTER SCHEMA
+  ALTER SERVER CONFIGURATION
+  ALTER VIEW
+  BEGIN CONVERSATION TIMER
+  BEGIN DIALOG CONVERSATION
+  BULK INSERT
+  CREATE COLUMNSTORE INDEX
+  CREATE EXTERNAL FILE FORMAT
+  CREATE, ALTER, DROP CREDENTIAL
+  CREATE, ALTER, DROP CRYPTOGRAPHIC PROVIDER
+  CREATE, ALTER, DROP ENDPOINT
+  CREATE, ALTER, DROP EXTERNAL LANGUAGE
+  CREATE, ALTER, DROP MESSAGE TYPE
+  CREATE, ALTER, DROP PARTITION FUNCTION
+  CREATE, ALTER, DROP PARTITION SCHEME
+  CREATE, ALTER, DROP RESOURCE POOL
+  CREATE, ALTER, DROP ROLE
+  CREATE, ALTER, DROP SEARCH PROPERTY LIST
+  CREATE, ALTER, DROP SECURITY POLICY
+  CREATE, ALTER, DROP SELECTIVE XML INDEX clause
+  CREATE, ALTER, DROP SPATIAL INDEX
+  CREATE, ALTER, DROP TYPE
+  CREATE, ALTER, DROP XML INDEX
+  CREATE, ALTER, DROP XML SCHEMA COLLECTION
+  CREATE, DROP WORKLOAD CLASSIFIER
+  CREATE/ALTER/ENABLE/DISABLE TRIGGER
+  DENY
+  END, MOVE CONVERSATION
+  EXECUTE with AS LOGIN or AT option
+  GET CONVERSATION GROUP
+  GROUP BY ALL clause
+  GROUP BY CUBE clause
+  GROUP BY ROLLUP clause
+  INSERT\.\.\. DEFAULT VALUES
+  INSERT\.\.\. TOP
+  KILL
+  MERGE
+  NEXT VALUE FOR sequence clause
+  READTEXT
+  REVERT
+  REVOKE
+  SELECT PIVOT/UNPIVOT
+  SELECT TOP x PERCENT WHERE x <> 100
+  SELECT TOP\.\.\. WITH TIES
+  SELECT\.\.\. FOR XML AUTO
+  SELECT\.\.\. FOR XML EXPLICIT
+  SEND
+  SET CONTEXT\_INFO
+  SET DATEFORMAT
+  SET DEADLOCK\_PRIORITY
+  SET FMTONLY
+  SET FORCEPLAN
+  SET LOCK\_TIMEOUT  
+  SET NUMERIC\_ROUNDABORT ON
+  SET OFFSETS
+  SET REMOTE\_PROC\_TRANSACTIONS
+  SET ROWCOUNT @variable
+  SET ROWCOUNT n WHERE n \!= 0
+  SET SHOWPLAN\_ALL
+  SET SHOWPLAN\_TEXT
+  SET SHOWPLAN\_XML
+  SET STATISTICS
+  SET STATISTICS IO
+  SET STATISTICS PROFILE
+  SET STATISTICS TIME
+  SET STATISTICS XML
+  SET TRANSACTION ISOLATION LEVEL REPEATABLE READ
+  SET TRANSACTION ISOLATION LEVEL SERIALIZABLE
+  SHUTDOWN statement
+  UPDATE STATISTICS
+  UPDATETEXT
+  Using EXECUTE to call a SQL function
+  VIEW\.\.\. CHECK OPTION clause
+  VIEW\.\.\. VIEW\_METADATA clause
+  WAITFOR DELAY
+  WAITFOR TIME
+  WAITFOR, RECEIVE
+  WITH XMLNAMESPACES construct
+  WRITETEXT
+  XPATH expressions