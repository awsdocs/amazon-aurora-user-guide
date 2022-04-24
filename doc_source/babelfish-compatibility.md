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
+ [T\-SQL differences in Babelfish](#babelfish-compatibility.tsql.limitations)
+ [Features with limited implementation](#babelfish-compatibility.tsql.limited-implementation)
+ [Unsupported functionality in Babelfish](#babelfish-compatibility.tsql.limitations-unsupported)

## T\-SQL differences in Babelfish<a name="babelfish-compatibility.tsql.limitations"></a>

Following, you can find a table of T\-SQL functionality as supported in the current release of Babelfish with some notes about differences in the behavior from that of SQL Server\.

For more information about support in various versions, see [Supported functionality in Babelfish by version](babelfish-compatibility.supported-functionality-table.md)\. For information about features that currently aren't supported, see [Unsupported functionality in Babelfish](#babelfish-compatibility.tsql.limitations-unsupported)\. 

For more information about Babelfish releases, see [Babelfish versions](babelfish-releases-updates.md)\.


| Functionality or syntax | Description of behavior or difference | 
| --- | --- | 
| \\ \(line continuation character\) | The line continuation character \(a backslash prior to a newline\) for character and hexadecimal strings isn't currently supported\. For character strings, the backslash\-newline is interpreted as characters in the string\. For hexadecimal strings, backslash\-newline results in a syntax error\.  | 
| @@version | The format of the value returned by `@@version` is slightly different from the value returned by SQL Server\. Your code might not work correctly if it depends on the formatting of `@@version`\. | 
| Aggregate functions | Aggregate functions are partially supported \(AVG, COUNT, COUNT\_BIG, GROUPING, MAX, MIN, STRING\_AGG, and SUM are supported\)\. For a list of unsupported aggregate functions, see [Functions that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list4)\. | 
|  ALTER TABLE  | Supports adding or dropping a single column or constraint only\.  | 
| BACKUP statement | Aurora PostgreSQL snapshots of a database are dissimilar to backup files created in SQL Server\. Also, the granularity of when a backup and restore occurs might be different between SQL Server and Aurora PostgreSQL\. | 
| Blank column names with no column alias | The `sqlcmd` and `psql` utilities handle columns with blank names differently: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/babelfish-compatibility.html)  | 
| Collation, index on type dependent on the ICU library | An index on a user\-defined type that depends on the International Components for Unicode \(ICU\) collation library \(the library used by Babelfish\) isn't invalidated when the version of the library is changed\. For more information about collations, see [Babelfish collation support](babelfish-collations.md)\. | 
| COLLATIONPROPERTY function | Collation properties are implemented only for the supported Babelfish BBF collations\. For more information about collations, see [Babelfish collation support](babelfish-collations.md)\. | 
| Column default | When creating a column default, the constraint name is ignored\. To drop a column default, use the following syntax: `ALTER TABLE...ALTER COLUMN..DROP DEFAULT...` | 
| Constraints | PostgreSQL doesn't support turning on and turning off individual constraints\. The statement is ignored and a warning is raised\. | 
| Constraints created with DESC \(descending\) columns | Constraints are created with ASC \(ascending\) columns\. | 
| Constraints with IGNORE\_DUP\_KEY | Constraints are created without this property\. | 
| CREATE, ALTER, DROP SERVER ROLE |  ALTER SERVER ROLE is supported only for `sysadmin`\. All other syntax is unsupported\. The T\-SQL user in Babelfish has an experience that is similar to SQL Server for the concepts of a login \(server principal\), a database, and a database user \(database principal\)\. Only the `dbo` user is available in Babelfish user databases\. To operate as the `dbo` user, a login must be a member of the server\-level `sysadmin` role \(`ALTER SERVER ROLE sysadmin ADD MEMBER login`\)\. Logins without `sysadmin` role can currently access only `master` and `tempdb` as the `guest` user\. Currently, because Babelfish supports only the `dbo` user in user databases, all application users must use a login that is a `sysadmin` member\. You can't create a user with lesser privileges, such as read\-only on certain tables\.  | 
| CREATE, ALTER LOGIN clauses are supported with limited syntax | The CREATE LOGIN\.\.\. PASSWORD clause, \.\.\.DEFAULT\_DATABASE clause, and \.\.\.DEFAULT\_LANGUAGE clause are supported\. The ALTER LOGIN\.\.\. PASSWORD clause is supported, but ALTER LOGIN\.\.\. OLD\_PASSWORD clause isn't supported\. Only a login that is a sysadmin member can modify a password\. | 
| CREATE DATABASE case\-sensitive collation  | Case\-sensitive collations aren't supported with the CREATE DATABASE statement\. | 
| CREATE DATABASE keywords and clauses | Options except COLLATE and CONTAINMENT=NONE aren't supported\. The COLLATE clause is accepted and is always set to the value of `babelfishpg_tsql.server_collation_name`\. | 
| CREATE SCHEMA\.\.\. supporting clauses | You can use the CREATE SCHEMA command to create an empty schema\. Use additional commands to create schema objects\. | 
| CREATE, ALTER LOGIN clauses are supported with limited syntax | The CREATE LOGIN\.\.\. PASSWORD clause, \.\.\.DEFAULT\_DATABASE clause, and \.\.\.DEFAULT\_LANGUAGE clause are supported\. The ALTER LOGIN\.\.\. PASSWORD clause is supported, but ALTER LOGIN\.\.\. OLD\_PASSWORD clause isn't supported\. Only a login that is a sysadmin member can modify a password\. | 
| LOGIN objects | All options for LOGIN objects are supported except for PASSWORD, DEFAULT\_DATABASE, ENABLE, DISABLE\. | 
| Database ID values are different on Babelfish  |  The master and tempdb databases won't be database IDs 1 and 2\.  | 
| Identifiers exceeding 63 characters | PostgreSQL supports a maximum of 63 characters for identifiers\. Babelfish converts identifiers longer than 63 characters to a name that includes a hash of the original name\. Fpr example, a table created as "AB\(ABC1234567890123456789012345678901234567890123456789012345678901234567890" might be converted to "ABC123456789012345678901234567890123456789012345678901234567890"\.  | 
| IDENTITY columns support | IDENTITY columns are supported for data types tinyint, smallint, int, bigint\. numeric, and decimal\. SQL Server supports precision to 38 places for data types `numeric` and `decimal` in IDENTITY columns\.PostgreSQL supports precision to 19 places for data types `numeric` and `decimal` in IDENTITY columns\. | 
| Indexes with IGNORE\_DUP\_KEY | Syntax that creates an index that includes IGNORE\_DUP\_KEY creates an index as if this property is omitted\. | 
| Indexes with more than 32 columns | An index can't include more than 32 columns\. Included index columns count toward the maximum in PostgreSQL but not in SQL Server\. | 
| Indexes \(clustered\) | Clustered indexes are created as if NONCLUSTERED was specified\. | 
| Index clauses | The following clauses are ignored: FILLFACTOR, ALLOW\_PAGE\_LOCKS, ALLOW\_ROW\_LOCKS, PAD\_INDEX, STATISTICS\_NORECOMPUTE, OPTIMIZE\_FOR\_SEQUENTIAL\_KEY, SORT\_IN\_TEMPDB, DROP\_EXISTING, ONLINE, COMPRESSION\_DELAY, MAXDOP, and DATA\_COMPRESSION | 
| NEWSEQUENTIALID function | Implemented as NEWID; sequential behavior isn't guaranteed\. When calling `NEWSEQUENTIALID`, PostgreSQL generates a new GUID value\. | 
| OUTER APPLY | SQL Server lateral joins aren't supported\. PostgreSQL provides SQL syntax that allows a lateral join, but the behavior isn't identical\. For more information about PostgreSQL lateral joins, see [the PostgreSQL documentation](https://www.postgresql.org/docs/14/queries-table-expressions.html)\. | 
| OUTPUT clause is supported with the following limitations | OUTPUT and OUTPUT INTO aren't supported in the same DML query\. References to non\-target table of UPDATE or DELETE operations in an OUTPUT clause aren't supported\. OUTPUT\.\.\. DELETED \*, INSERTED \* aren't supported in the same query\. | 
| Procedure or function parameter limit | Babelfish supports a maximum of 100 parameters for a procedure or function\. | 
| RESTORE statement | Aurora PostgreSQL snapshots of a database are dissimilar to backup files created in SQL Server\. Also, the granularity of when the backup and restore occurs might be different between SQL Server and Aurora PostgreSQL\. | 
| ROLLBACK: table variables don't support transactional rollback | Processing might be interrupted if a rollback occurs in a session with table variables\. | 
| ROWGUIDCOL | This clause is currently ignored\. Queries referencing `$GUIDGOL` cause a syntax error\. | 
| SEQUENCE object support | SEQUENCE objects are supported for the data types tinyint, smallint, int, bigint, numeric, and decimal\. Aurora PostgreSQL supports precision to 19 places for data types numeric and decimal in a SEQUENCE\. | 
| Server\-level roles | The `sysadmin` server\-level role is supported\. Other server\-level roles \(other than `sysadmin`\) aren't supported\. | 
| Database\-level roles other than `db_owner` | The `db_owner` database\-level roles is supported\. Other database\-level roles \(other than db\_owner\) aren't supported\. | 
| SQL keyword SPARSE | The keyword SPARSE is accepted and ignored\. | 
| SQL keyword clause `ON filegroup` | This clause is currently ignored\. | 
| SQL keywords `CLUSTERED` and `NONCLUSTERED` for indexes and constraints | Babelfish accepts and ignores the `CLUSTERED` and `NONCLUSTERED` keywords\. | 
| `sysdatabases.cmptlevel` | `sysdatabases.cmptlevel` are always NULL\. | 
| tempdb isn't reinitialized at restart | Permanent objects \(like tables and procedures\) created in tempdb aren't removed when the database is restarted\. | 
| TEXTIMAGE\_ON filegroup | Babelfish ignores the `TEXTIMAGE_ON` *`filegroup`* clause\. | 
| Time precision | Babelfish supports 6\-digit precision for fractional seconds\. No adverse effects are anticipated with this behavior\. | 
| Transaction isolation levels | READUNCOMMITTED is treated the same as READCOMMITTED\. REPEATABLEREAD, and SERIALIZABLE aren't supported\. | 
| Virtual computed columns \(non\-persistent\) | Virtual computed columns are created as persistent\. | 
| WITHOUT SCHEMABINDING clause | This clause isn't supported in functions, procedures, triggers, or views\. The object is created, but as if WITH SCHEMABINDING was specified\. | 

## Features with limited implementation<a name="babelfish-compatibility.tsql.limited-implementation"></a>

Each new version of Babelfish for Aurora PostgreSQL adds support for more features that better align with T\-SQL functionality and behavior\. Still, there are some unsupported features and differences in the current implementation\. In the following, you can find information about functional differences between Babelfish and T\-SQL, with some workarounds or usage notes\.

As of version 1\.2\.0 of Babelfish, the following features currently have limited implementations:
+ **SQL Server catalogs \(system views\)** – The catalogs `sys.sysconfigures`, `sys.syscurconfigs`, and `sys.configurations` support a single read\-only configuration only\. The `sp_configure` isn't currently supported\. For more information about some of the other SQL Server views implemented by Babelfish, see [Querying a database for object information](babelfish-query-database.md)\. 
+ **GRANT permissions** – GRANT…TO PUBLIC is supported, but GRANT\.\.TO PUBLIC WITH GRANT OPTION is not currently supported\. 
+ **SQL Server *ownership chain* and permission mechanism limitation** – In Babelfish, the SQL Server ownership chain works for views but not for stored procedures\. This means that procedures must be granted explicit access to other objects owned by the same owner as the calling procedures\. In SQL Server, granting the caller EXECUTE permissions on the procedure is sufficient to call other objects owned by same owner\. In Babelfish, caller must also be granted permissions on the objects accessed by the procedure\. 
+ **Resolution of unqualified \(without schema name\) object references** – When a SQL object \(procedure, view, function or trigger\) references an object without qualifying it with a schema name, SQL Server resolves the object's schema name by using the schema name of the SQL object in which the reference occurs\. Currently, Babelfish resolves this differently, by using the default schema of the database user executing the procedure\. 
+ **Default schema changes, sessions, and connections** – If users change their default schema with `ALTER USER...WITH DEFAULT SCHEMA`, the change takes effect immediately in that session\. However, for other currently connected sessions belonging to the same user, the timing differs, as follows:
  + For SQL Server: – The change takes effect across all other connections for this user immediately\.
  + For Babelfish: – The change takes effect for this user for new connections only\.
+ **Non\-deterministic collations and CHARINDEX** – CHARINDEX can't currently be used when the applicable collation is non\-deterministic\. Because Babelfish by default uses a case\-insensitive collation, which is non\-deterministic, you may get a run\-time error saying "nondeterministic collations are not supported for substring searches"\. Until this is resolved, this issue can be worked around in one of the following ways:
  + Explicitly convert the expression to a case\-sensitive collation and case\-fold both arguments by applying LOWER or UPPER\. For example, `SELECT charindex('x', a) FROM t1` would become the following:

    ```
    SELECT charindex(LOWER('x'), LOWER(a COLLATE sql_latin1_general_cp1_cs_as)) FROM t1
    ```
  + Create a SQL function f\_charindex, and replace CHARINDEX calls with calls to the following function:

    ```
    CREATE function f_charindex(@s1 varchar(max), @s2 varchar(max)) returns int
    AS
    BEGIN
    declare @i int = 1
    WHILE len(@s2) >= len(@s1)
    BEGIN
      if LOWER(@s1) = LOWER(substring(@s2,1,len(@s1))) return @i
      set @i += 1
      set @s2 = substring(@s2,2,999999999)
    END
    return 0
    END
    go
    ```
+ **ROWVERSION and TIMESTAMP datatypes implementation and escape hatch setting** – The ROWVERSION and TIMESTAMP datatypes are now supported in Babelfish\. To use ROWVERSION or TIMESTAMP in Babelfish, you must change the setting for the escape hatch `babelfishpg_tsql.escape_hatch_rowversion` from its default \(strict\) to `ignore`\. The Babelfish implementation of the ROWVERSION and TIMESTAMP datatypes is mostly semantically identical to SQL Server, with the following exceptions: 
  + In SQL Server, every inserted or updated row gets a unique ROWVERSION/TIMESTAMP value\. In Babelfish, every inserted row updated by the same statement is assigned the same ROWVERSION/TIMESTAMP value\.

    For example, when an UPDATE statement or INSERT\-SELECT statement affects multiple rows, in SQL Server, the affected rows all have different values in their ROWVERSION/TIMESTAMP column\. In Babelfish \(PostgreSQL\), the rows have the same value\.
  + In SQL Server, when you create a new table with SELECT\-INTO, you can cast an explicit value \(such as NULL\) to a to\-be\-created ROWVERSION/TIMESTAMP column\. When you do the same thing in Babelfish, an actual ROWVERSION/TIMESTAMP value is assigned to each row in the new table for you, by Babelfish\.

**Note**  
These minor differences in ROWVERSION/TIMESTAMP datatypes shouldn't have an adverse impact on applications running on Babelfish\.

**Schema creation, ownership, and permissions** – Permissions to create objects in a schema that was created by the database owner \(using `CREATE SCHEMA…AUTHORIZATION DBO`\) differ for SQL Server and Babelfish non\-DBO users, as shown in the following table:


| Database user \(non\-DBO\) can do the following: | SQL Server | Babelfish | 
| --- | --- | --- | 
|  Create objects in the schema without additional grants by the DBO?  |  No  |  Yes  | 
|  Reference objects created in the schema by DBO without additional grants?  |  Yes  |  No  | 

## Unsupported functionality in Babelfish<a name="babelfish-compatibility.tsql.limitations-unsupported"></a>

In the following table and lists, you can find functionality that isn't currently supported in Babelfish\. For information about updates to Babelfish, see [Babelfish versions](babelfish-releases-updates.md)\. 

**Topics**
+ [Functionality that isn't currently supported](#babelfish-compatibility.tsql.limitations-unsupported-table)
+ [Settings that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list8)
+ [Commands for which certain functionality isn't supported](#babelfish-compatibility.tsql.limitations-unsupported-list1)
+ [Column names or attributes that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list7)
+ [Object types that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list3)
+ [Functions that aren't supported](#babelfish-compatibility.tsql.limitations-unsupported-list4)
+ [Syntax for which certain functionality isn't supported](#babelfish-compatibility.tsql.limitations-unsupported-list2)
+ [Syntax that isn't supported](#babelfish-compatibility.tsql.limitations-unsupported-list5)

### Functionality that isn't currently supported<a name="babelfish-compatibility.tsql.limitations-unsupported-table"></a>

In the table you can find information about certain functionality that isn't currently supported\. 


| Functionality or syntax | Description | 
| --- | --- | 
| Assembly modules and SQL Common Language Runtime \(CLR\) routines  | Functionality related to assembly modules and CLR routines isn't supported\. | 
| Column attributes | ROWGUIDCOL, SPARSE, FILESTREAM, and MASKED aren't supported\. | 
| Contained databases | Contained databases with logins authenticated at the database level rather than at the server level aren't supported\. | 
| CROSS APPLY | Lateral joins aren't supported\. | 
| Cross\-database object references | Objects with three\-part names aren't supported\. For more information, see: [Using Babelfish with a single database or multiple databases](babelfish.md#babelfish-single_vs_multi_db)\. | 
| Cursors \(updatable\) | Updatable cursors aren't supported\. | 
| Cursors \(global\) | GLOBAL cursors aren't supported\. | 
| Cursor \(fetch behaviors\) | The following cursor fetch behaviors aren't supported: FETCH PRIOR, FIRST, LAST, ABSOLUTE, abd RELATIVE | 
| Cursor\-typed \(variables and parameters\) | Cursor\-typed variables and parameters aren't supported\. | 
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
| Materialized views | Materialized views aren't supported\. | 
| NOT FOR REPLICATION clause | This syntax is accepted and ignored\.  | 
| ODBC escape functions | ODBC escape functions aren't supported\. | 
| Partitioning | Table and index partitioning isn't supported\. | 
| Procedure calls that includes DEFAULT as a parameter value | DEFAULT isn't a supported parameter value\. | 
| Procedure declarations with more than 100 parameters | Declarations with more than 100 parameters aren't supported\. | 
| Procedures, externally defined | Externally defined procedures, including SQL CLR procedures, aren't supported\. | 
| Procedure versioning | Procedure versioning isn't supported\. | 
| Procedures WITH RECOMPILE | WITH RECOMPILE \(when used in conjunction with the DECLARE and EXECUTE statements\) isn't supported\. | 
| Remote object references | Objects with four\-part names aren't supported\.\. For more information, see: [Configuring a database for Babelfish](babelfish-configuration.md)\. | 
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
| TIMESTAMP data type | This data type isn't supported\. The SQL Server TIMESTAMP type is unrelated to PostgreSQL TIMESTAMP\. | 
| Triggers, externally defined | These triggers aren't supported, including SQL Common Language Runtime \(CLR\)\. | 
| Triggers, INSTEAD\-OF on views | INSTEAD\-OF triggers on views aren't supported\. INSTEAD\-OF triggers are supported \(Babelfish 1\.2\.0 and higher releases\)\. | 
| Unquoted string values in stored procedure calls and default values | String parameters to stored procedure calls, and defaults for string parameters in CREATE PROCEDURE, aren't supported\. | 
| WITHOUT SCHEMABINDING clause | This clause isn't supported in functions, procedures, triggers, or views\. The object is created, but as if WITH SCHEMABINDING was specified\. | 

### Settings that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list8"></a>

The following settings aren't supported:
+ SET ANSI\_NULL\_DFLT\_OFF ON
+ SET ANSI\_NULL\_DFLT\_ON OFF
+ SET ANSI\_PADDING OFF
+ SET ANSI\_WARNINGS OFF
+ SET ARITHABORT OFF
+ SET ARITHIGNORE ON
+ SET CURSOR\_CLOSE\_ON\_COMMIT ON
+ SET NUMERIC\_ROUNDABORT ON

### Commands for which certain functionality isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list1"></a>

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
+ GRANT

### Column names or attributes that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list7"></a>

The following column names aren't supported:
+ $IDENTITY
+ $ROWGUID
+ IDENTITYCOL

### Object types that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list3"></a>

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
+ USER

### Functions that aren't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list4"></a>

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

### Syntax for which certain functionality isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list2"></a>

Certain functionality for the following syntax isn't supported:
+ ALTER SERVICE, BACKUP/RESTORE SERVICE MASTER KEY clause
+ BEGIN DISTRIBUTED TRANSACTION
+ CREATE EXTERNAL TABLE
+ CREATE TABLE\.\.\. GRANT clause
+ CREATE TABLE\.\.\. IDENTITY clause
+ CREATE, ALTER, DROP APPLICATION ROLE
+ CREATE, ALTER, DROP ASSEMBLY
+ CREATE, ALTER, DROP ASYMMETRIC KEY
+ CREATE, ALTER, DROP EVENT SESSION
+ CREATE, ALTER, DROP EXTERNAL RESOURCE POOL
+ CREATE, ALTER, DROP FULLTEXT CATALOG
+ CREATE, ALTER, DROP FULLTEXT INDEX
+ CREATE, ALTER, DROP FULLTEXT STOPLIST
+ CREATE, ALTER, DROP QUEUE
+ CREATE, ALTER, DROP RESOURCE GOVERNOR
+ CREATE, ALTER, DROP ROUTE
+ CREATE, ALTER, DROP SERVICE
+ CREATE, ALTER, DROP WORKLOAD GROUP
+ CREATE, ALTER, DROP, OPEN/CLOSE, BACKUP/RESTORE MASTER KEY
+ CREATE/DROP RULE
+ CREATE USER – This syntax isn't supported\. The PostgreSQL statement CREATE USER doesn't create a user that is equivalent to the SQL Server CREATE USER syntax\. For more information, see [T\-SQL differences in Babelfish](#babelfish-compatibility.tsql.limitations)\.
+ SET LANGUAGE – This syntax isn't supported with any value other than `english` or `us_english`\.

### Syntax that isn't supported<a name="babelfish-compatibility.tsql.limitations-unsupported-list5"></a>

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
+ ALTER VIEW
+ BEGIN CONVERSATION TIMER
+ BEGIN DIALOG CONVERSATION
+ BULK INSERT
+ CREATE COLUMNSTORE INDEX
+ CREATE EXTERNAL FILE FORMAT
+ CREATE, ALTER, DROP CREDENTIAL
+ CREATE, ALTER, DROP CRYPTOGRAPHIC PROVIDER
+ CREATE, ALTER, DROP ENDPOINT
+ CREATE, ALTER, DROP EXTERNAL LANGUAGE
+ CREATE, ALTER, DROP MESSAGE TYPE
+ CREATE, ALTER, DROP PARTITION FUNCTION
+ CREATE, ALTER, DROP PARTITION SCHEME
+ CREATE, ALTER, DROP RESOURCE POOL
+ CREATE, ALTER, DROP ROLE
+ CREATE, ALTER, DROP SEARCH PROPERTY LIST
+ CREATE, ALTER, DROP SECURITY POLICY
+ CREATE, ALTER, DROP SELECTIVE XML INDEX clause
+ CREATE, ALTER, DROP SPATIAL INDEX
+ CREATE, ALTER, DROP TYPE
+ CREATE, ALTER, DROP XML INDEX
+ CREATE, ALTER, DROP XML SCHEMA COLLECTION
+ CREATE, DROP WORKLOAD CLASSIFIER
+ CREATE/ALTER/ENABLE/DISABLE TRIGGER
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
+ REVOKE
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