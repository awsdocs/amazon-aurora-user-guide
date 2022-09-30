# Unsupported functionality in Babelfish<a name="babelfish-compatibility.tsql.limitations-unsupported"></a>

In the following table and lists, you can find functionality that isn't currently supported in Babelfish\. Updates to Babelfish are included in Aurora PostgreSQL versions\. For more information, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\. 

**Topics**
+ [Functionality that isn't currently supported](#babelfish-compatibility.tsql.limitations-unsupported-table)
+ [Settings that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list8)
+ [Commands that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list1)
+ [Column names or attributes that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list7)
+ [Data types that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list9)
+ [Object types that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list3)
+ [Functions that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list4)
+ [Syntax that isn't supported](#babelfish-compatibility.tsql.limitations-unsupported-list5)

## Functionality that isn't currently supported<a name="babelfish-compatibility.tsql.limitations-unsupported-table"></a>

In the table you can find information about certain functionality that isn't currently supported\. 


| Functionality or syntax | Description | 
| --- | --- | 
| Assembly modules and SQL Common Language Runtime \(CLR\) routines  | Functionality related to assembly modules and CLR routines isn't supported\. | 
| Column attributes | ROWGUIDCOL, SPARSE, FILESTREAM, and MASKED aren't supported\. | 
| Contained databases | Contained databases with logins authenticated at the database level rather than at the server level aren't supported\. | 
| CROSS APPLY | Lateral joins aren't supported\. | 
| Cursors \(updatable\) | Updatable cursors aren't supported\. | 
| Cursors \(global\) | GLOBAL cursors aren't supported\. | 
| Cursor \(fetch behaviors\) | The following cursor fetch behaviors aren't supported: FETCH PRIOR, FIRST, LAST, ABSOLUTE, abd RELATIVE | 
| Cursor\-typed \(variables and parameters\) | Cursor\-typed variables and parameters aren't supported for output parameters \(an error is raised\)\.  | 
| Cursor options | SCROLL, KEYSET, DYNAMIC, FAST\_FORWARD, SCROLL\_LOCKS, OPTIMISTIC, TYPE\_WARNING, and FOR UPDATE | 
| Data encryption | Data encryption isn't supported\. | 
| DBCC commands  | Microsoft SQL Server Database Console Commands \(DBCC\) aren't supported\. | 
| DROP IF EXISTS | This syntax isn't supported for USER and SCHEMA objects\. It's supported for the objects TABLE, VIEW, PROCEDURE, FUNCTION, and DATABASE\. | 
| Encryption | Built\-in functions and statements don't support encryption\. | 
| ENCRYPT\_CLIENT\_CERT connections | Client certificate connections aren't supported\. | 
| EXECUTE AS statement | This statement isn't supported\. | 
| EXECUTE AS SELF clause | This clause isn't supported in functions, procedures, or triggers\. | 
| EXECUTE AS USER clause | This clause isn't supported in functions, procedures, or triggers\. | 
| Foreign key constraints referencing database name | Foreign key constraints that reference the database name aren't supported\. | 
| Full\-text search | Full\-text search built\-in Functions and statements aren't supported\. | 
| Function declarations with greater than 100 parameters | Function declarations that contain more than 100 parameters aren't supported\. | 
| Function calls that include DEFAULT as a parameter value | DEFAULT isn't a supported parameter value for a function call\. | 
| Function calls that include :: | Function calls that include :: aren't supported\. | 
| Functions, externally defined | External functions, including SQL CLR functions, aren't supported\. | 
| Global temporary tables \(tables with names that start with \#\#\) | Global temporary tables aren't supported\. | 
| Graph functionality | All SQL graph functionality isn't supported\. | 
| Hints | Hints aren't supported for joins, queries, or tables\. | 
| Identifiers \(variables or parameters\) with multiple leading @ characters | Identifiers that start with more than one leading `@` aren't supported\. | 
| Identifiers, table or column names that contain @ or \]\] characters | Table or column names that contain an `@` sign or square brackets aren't supported\. | 
| Inline indexes | Inline indexes aren't supported\. | 
| Invoking a procedure whose name is in a variable | Using a variable as a procedure name isn't supported\. | 
| JSON\_MODIFY | Updating JSON string using JSON\_MODIFY isn't supported\. | 
| FOR JSON clause | Formatting query results as JSON isn't supported\. | 
| Materialized views | Materialized views aren't supported\. | 
| NOT FOR REPLICATION clause | This syntax is accepted and ignored\.  | 
| ODBC escape functions | ODBC escape functions aren't supported\. | 
| Partitioning | Table and index partitioning isn't supported\. | 
| Procedure calls that includes DEFAULT as a parameter value | DEFAULT isn't a supported parameter value\. | 
| Procedure declarations with more than 100 parameters | Declarations with more than 100 parameters aren't supported\. | 
| Procedures, externally defined | Externally defined procedures, including SQL CLR procedures, aren't supported\. | 
| Procedure versioning | Procedure versioning isn't supported\. | 
| Procedures WITH RECOMPILE | WITH RECOMPILE \(when used in conjunction with the DECLARE and EXECUTE statements\) isn't supported\. | 
| Remote object references | Objects with four\-part names aren't supported\.\. For more information, see: [DB cluster parameter group settings for Babelfish](babelfish-configuration.md)\. | 
| Row\-level security | Row\-level security with CREATE SECURITY POLICY and inline table\-valued functions isn't supported\. | 
| Service broker functionality | Service broker functionality isn't supported\. | 
| SESSIONPROPERTY | Unsupported properties: ANSI\_NULLS, ANSI\_PADDING, ANSI\_WARNINGS, ARITHABORT, CONCAT\_NULL\_YIELDS\_NULL, and NUMERIC\_ROUNDABORT | 
| SET LANGUAGE | This syntax isn't supported with any value other than `english` or `us_english`\. | 
| SP\_CONFIGURE | This system stored procedure isn't supported\. | 
| SQL keyword SPARSE | The keyword SPARSE is accepted and ignored\. | 
| Table value constructor syntax \(FROM clause\) | The unsupported syntax is for a derived table constructed with the FROM clause\. | 
| Temporal tables | Temporal tables aren't supported\. | 
| Temporary procedures aren't dropped automatically | This functionality isn't supported\. | 
| Transaction isolation levels | READUNCOMMITTED is treated the same as READCOMMITTED\. REPEATABLEREAD, and SERIALIZABLE aren't supported\. | 
| Triggers, externally defined | These triggers aren't supported, including SQL Common Language Runtime \(CLR\)\. | 
| Triggers, INSTEAD\-OF on views | INSTEAD\-OF triggers on views aren't supported\. INSTEAD\-OF triggers are supported \(Babelfish 1\.2\.0 and higher releases\)\. | 
| Unquoted string values in stored procedure calls and default values | String parameters to stored procedure calls, and defaults for string parameters in CREATE PROCEDURE, aren't supported\. | 
| WITHOUT SCHEMABINDING clause | This clause isn't supported in functions, procedures, triggers, or views\. The object is created, but as if WITH SCHEMABINDING was specified\. | 

## Settings that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list8"></a>

The following settings aren't supported:
+ SET ANSI\_NULL\_DFLT\_OFF ON
+ SET ANSI\_NULL\_DFLT\_ON OFF
+ SET ANSI\_PADDING OFF
+ SET ANSI\_WARNINGS OFF
+ SET ARITHABORT OFF
+ SET ARITHIGNORE ON
+ SET CURSOR\_CLOSE\_ON\_COMMIT ON
+ SET NUMERIC\_ROUNDABORT ON
+ SET PARSEONLY ON \(command doesn't work as expected\)
+ SET FMTONLY ON \(command doesn't work as expected\. It suppresses only the execution of SELECT statements but not others\.\)

## Commands that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list1"></a>

Certain functionality for the following commands isn't supported:
+ ADD SIGNATURE
+ ALTER DATABASE, ALTER DATABASE SET
+ CREATE, ALTER, DROP AUTHORIZATION
+ CREATE, ALTER, DROP AVAILABILITY GROUP
+ CREATE, ALTER, DROP BROKER PRIORITY
+ CREATE, ALTER, DROP COLUMN ENCRYPTION KEY
+ CREATE, ALTER, DROP DATABASE ENCRYPTION KEY
+ CREATE, ALTER, DROP, BACKUP CERTIFICATE
+ CREATE AGGREGATE
+ CREATE CONTRACT
+ CHECKPOINT

## Column names or attributes that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list7"></a>

The following column names aren't supported:
+ $IDENTITY
+ $ROWGUID
+ IDENTITYCOL

## Data types that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list9"></a>

The following data types aren't supported:
+ Geospatial \(GEOGRAPHY and GEOMETRY\)
+ HIERARCHYID

## Object types that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list3"></a>

The following object types aren't supported:
+ COLUMN MASTER KEY
+ CREATE, ALTER EXTERNAL DATA SOURCE
+ CREATE, ALTER, DROP DATABASE AUDIT SPECIFICATION
+ CREATE, ALTER, DROP EXTERNAL LIBRARY
+ CREATE, ALTER, DROP SERVER AUDIT
+ CREATE, ALTER, DROP SERVER AUDIT SPECIFICATION
+ CREATE, ALTER, DROP, OPEN/CLOSE SYMMETRIC KEY
+ CREATE, DROP DEFAULT
+ CREDENTIAL
+ CRYPTOGRAPHIC PROVIDER
+ DIAGNOSTIC SESSION
+ Indexed views
+ SERVICE MASTER KEY
+ SYNONYM

## Functions that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list4"></a>

The following built\-in functions aren't supported:

**Aggregate functions**
+ APPROX\_COUNT\_DISTINCT
+ CHECKSUM\_AGG
+ GROUPING\_ID
+ ROWCOUNT\_BIG
+ STDEV
+ STDEVP
+ VAR
+ VARP

**Cryptographic functions**
+ CERTENCODED function
+ CERTID function
+ CERTPROPERTY function

**Metadata functions**
+ COLUMNPROPERTY
+ OBJECTPROPERTY
+ OBJECTPROPERTYEX
+ TYPEPROPERTY
+ SERVERPROPERTY function – The following properties aren't supported:
  + BuildClrVersion
  + ComparisonStyle
  + ComputerNamePhysicalNetBIOS
  + HadrManagerStatus
  + InstanceDefaultDataPath
  + InstanceDefaultLogPath
  + InstanceName
  + IsClustered
  + IsHadrEnabled
  + LCID
  + MachineName
  + NumLicenses
  + ProcessID
  + ProductBuild
  + ProductBuildType
  + ProductLevel
  + ProductUpdateLevel
  + ProductUpdateReference
  + ResourceLastUpdateDateTime
  + ResourceVersion
  + ServerName
  + SqlCharSet
  + SqlCharSetName
  + SqlSortOrder
  + SqlSortOrderName
  + FilestreamShareName
  + FilestreamConfiguredLevel
  + FilestreamEffectiveLevel

**Security functions**
+ CERTPRIVATEKEY
+ LOGINPROPERTY

**Statements, operators, other functions**
+ EVENTDATA function
+ GET\_TRANSMISSION\_STATUS
+ OPENXML

## Syntax that isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list5"></a>

The following syntax isn't supported:
+ ALTER DATABASE
+ ALTER DATABASE SCOPED CONFIGURATION
+ ALTER DATABASE SCOPED CREDENTIAL
+ ALTER DATABASE SET HADR
+ ALTER FUNCTION
+ ALTER INDEX
+ ALTER PROCEDURE statement
+ ALTER SCHEMA
+ ALTER SERVER CONFIGURATION
+ ALTER SERVICE, BACKUP/RESTORE SERVICE MASTER KEY clause
+ ALTER VIEW
+ BEGIN CONVERSATION TIMER
+ BEGIN DISTRIBUTED TRANSACTION
+ BEGIN DIALOG CONVERSATION
+ BULK INSERT
+ CREATE COLUMNSTORE INDEX
+ CREATE EXTERNAL FILE FORMAT
+ CREATE EXTERNAL TABLE
+ CREATE, ALTER, DROP APPLICATION ROLE
+ CREATE, ALTER, DROP ASSEMBLY
+ CREATE, ALTER, DROP ASYMMETRIC KEY
+ CREATE, ALTER, DROP CREDENTIAL
+ CREATE, ALTER, DROP CRYPTOGRAPHIC PROVIDER
+ CREATE, ALTER, DROP ENDPOINT
+ CREATE, ALTER, DROP EVENT SESSION
+ CREATE, ALTER, DROP EXTERNAL LANGUAGE
+ CREATE, ALTER, DROP EXTERNAL RESOURCE POOL
+ CREATE, ALTER, DROP FULLTEXT CATALOG
+ CREATE, ALTER, DROP FULLTEXT INDEX
+ CREATE, ALTER, DROP FULLTEXT STOPLIST
+ CREATE, ALTER, DROP MESSAGE TYPE
+ CREATE, ALTER, DROP, OPEN/CLOSE, BACKUP/RESTORE MASTER KEY
+ CREATE, ALTER, DROP PARTITION FUNCTION
+ CREATE, ALTER, DROP PARTITION SCHEME
+ CREATE, ALTER, DROP QUEUE
+ CREATE, ALTER, DROP RESOURCE GOVERNOR
+ CREATE, ALTER, DROP RESOURCE POOL
+ CREATE, ALTER, DROP ROUTE
+ CREATE, ALTER, DROP SEARCH PROPERTY LIST
+ CREATE, ALTER, DROP SECURITY POLICY
+ CREATE, ALTER, DROP SELECTIVE XML INDEX clause
+ CREATE, ALTER, DROP SERVICE
+ CREATE, ALTER, DROP SPATIAL INDEX
+ CREATE, ALTER, DROP TYPE
+ CREATE, ALTER, DROP XML INDEX
+ CREATE, ALTER, DROP XML SCHEMA COLLECTION
+ CREATE/DROP RULE
+ CREATE, DROP WORKLOAD CLASSIFIER
+ CREATE, ALTER, DROP WORKLOAD GROUP
+ CREATE/ALTER/ENABLE/DISABLE TRIGGER
+ CREATE TABLE\.\.\. GRANT clause
+ CREATE TABLE\.\.\. IDENTITY clause
+ CREATE USER – This syntax isn't supported\. The PostgreSQL statement CREATE USER doesn't create a user that is equivalent to the SQL Server CREATE USER syntax\. For more information, see [T\-SQL differences in Babelfish](babelfish-compatibility.tsql.limitations.md)\.
+ DENY
+ END, MOVE CONVERSATION
+ EXECUTE with AS LOGIN or AT option
+ GET CONVERSATION GROUP
+ GROUP BY ALL clause
+ GROUP BY CUBE clause
+ GROUP BY ROLLUP clause
+ INSERT\.\.\. DEFAULT VALUES
+ INSERT\.\.\. TOP
+ KILL
+ MERGE
+ NEXT VALUE FOR sequence clause
+ READTEXT
+ REVERT
+ SELECT PIVOT/UNPIVOT
+ SELECT TOP x PERCENT WHERE x <> 100
+ SELECT TOP\.\.\. WITH TIES
+ SELECT\.\.\. FOR BROWSE
+ SELECT\.\.\. FOR XML AUTO
+ SELECT\.\.\. FOR XML EXPLICIT
+ SEND
+ SET CONTEXT\_INFO
+ SET DATEFORMAT
+ SET DEADLOCK\_PRIORITY
+ SET FMTONLY
+ SET FORCEPLAN
+ SET NO\_BROWSETABLE
+ SET NUMERIC\_ROUNDABORT ON
+ SET OFFSETS
+ SET REMOTE\_PROC\_TRANSACTIONS
+ SET ROWCOUNT @variable
+ SET ROWCOUNT n WHERE n \!= 0
+ SET SHOWPLAN\_ALL
+ SET SHOWPLAN\_TEXT
+ SET SHOWPLAN\_XML
+ SET STATISTICS
+ SET STATISTICS IO
+ SET STATISTICS PROFILE
+ SET STATISTICS TIME
+ SET STATISTICS XML
+ SET TRANSACTION ISOLATION LEVEL REPEATABLE READ
+ SET TRANSACTION ISOLATION LEVEL SERIALIZABLE
+ SHUTDOWN statement
+ UPDATE STATISTICS
+ UPDATETEXT
+ Using EXECUTE to call a SQL function
+ VIEW\.\.\. CHECK OPTION clause
+ VIEW\.\.\. VIEW\_METADATA clause
+ WAITFOR DELAY
+ WAITFOR TIME
+ WAITFOR, RECEIVE
+ WITH XMLNAMESPACES construct
+ WRITETEXT
+ XPATH expressions