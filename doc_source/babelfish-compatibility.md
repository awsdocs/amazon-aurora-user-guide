# Differences between Aurora PostgreSQL with Babelfish and SQL Server<a name="babelfish-compatibility"></a>

Babelfish provides support for T\-SQL and SQL Server behavior by supporting SQL Server data types, syntax, and functions for Aurora PostgreSQL\. This approach allows Aurora to support both Aurora PostgreSQL and SQL Server SQL dialects\. Also, Babelfish supports the SQL Server wire\-level protocol \(TDS\), allowing a SQL Server application to communicate natively with Aurora PostgreSQL\. Doing this helps migrate database objects, stored procedures, and application code with fewer changes\.

Although Babelfish doesn't offer complete support for T\-SQL, you can use Aurora PostgreSQL SQL commands to perform many of the tasks normally handled by these commands\. For example, if you regularly use a specific T\-SQL command that isn't supported by Babelfish, connect to the Aurora PostgreSQL port and use a PostgreSQL SQL command instead\. For more information, see [SQL Commands](https://www.postgresql.org/docs/14/sql-commands.html) in the PostgreSQL documentation\.

Aurora PostgreSQL offers functionality to replace many commonly used SQL Server features\. Some examples of SQL Server functionality that can be replaced by Aurora functionality follow:
+ If you use SQL Server bulk copy, you can use the Aurora PostgreSQL [ COPY statement\. COPY is optimized for fast data loading\.](https://www.postgresql.org/docs/14/populate.html)
+ If you use unsupported SQL Server GROUP BY clauses, you can use Aurora PostgreSQL [ GROUPING SETS](https://www.postgresql.org/docs/14/queries-table-expressions.html#QUERIES-GROUP)\.
+ If you use SQL Server JSON support, you can use Aurora PostgreSQL [ JSON functions and operators\.](https://www.postgresql.org/docs/14/functions-json.html)
+ If you use SQL Server XML support, you can use Aurora PostgreSQL [ XML functions\.](https://www.postgresql.org/docs/14/functions-xml.html)
+ If you use SQL Server full\-text search, you can use Aurora PostgreSQL [full\-text search\.](https://www.postgresql.org/docs/14/textsearch.html)
+ If you use the SQL Server GEOGRAPHY data type, you can use [ PostGIS](https://postgis.net/) to provide support for geographical data and geographical data manipulation\.

To help with cluster management in Aurora PostgreSQL, you can use its scalability, high\-availability with failover support, and built\-in replication\. For more information about these capabilities, see [Managing performance and scaling for Aurora DB clusters](Aurora.Managing.Performance.md), [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md), and [Replication with Amazon Aurora](Aurora.Replication.md)\. You also have access to other AWS tools and utilities:
+ [ Amazon CloudWatch](http://aws.amazon.com/cloudwatch/) is a monitoring and observability service that provides you with data and actionable insights\.
+ [ Amazon RDS Performance Insights](http://aws.amazon.com/rds/performance-insights/) is a database performance tuning and monitoring feature that helps you quickly assess the load on your database\.
+ [ Amazon RDS Multi\-AZ deployments](http://aws.amazon.com/rds/features/multi-az/) provide enhanced availability and durability for your database cluster\.
+ [ Amazon RDS global databases](http://aws.amazon.com/rds/aurora/global-database/) allow a single Amazon Aurora database to span multiple AWS Regions, offering scalable, cross\-Region replication\.
+ Automatic software patching keeps your database up\-to\-date with the latest security and feature patches when they become available\.
+ [Overview of Amazon RDS event notification](USER_Events.overview.md) Amazon RDS events notify you by email or SMS message of important database events, such as an automated failover\.

## T\-SQL limitations and unsupported functionality<a name="tsql.limitations"></a>

The following table lists unsupported or partially supported T\-SQL syntax and unsupported functionality for Babelfish\.  


| Functionality or syntax | Notes | 
| --- | --- | 
| ADD SIGNATURE | Functionality related to this command isn't supported\. | 
| CREATE AGGREGATE | Functionality related to this command isn't supported\. | 
| Aggregate functions \(partially supported\) | APPROX\_COUNT\_DISTINCT, CHECKSUM\_AGG, GROUPING\_ID, ROWCOUNT\_BIG, STDEV, STDEVP, VAR, and VARP aren't supported\. | 
| CREATE, ALTER, DROP APPLICATION ROLE | Functionality related to this syntax isn't supported\. | 
| CREATE, ALTER, DROP ASSEMBLY | Functionality related to this syntax isn't supported\. | 
| CREATE, ALTER, DROP ASYMMETRIC KEY | Functionality related to this syntax isn't supported\. | 
| Assembly modules and CLR routines | Functionality related to assembly modules and CLR routines isn't supported\. | 
| CREATE, ALTER, DROP AUTHORIZATION | Functionality related to these commands isn't supported\.   | 
| CREATE, ALTER, DROP AVAILABILITY GROUP | Functionality related to these commands isn't supported\. | 
| BACKUP statement | Aurora PostgreSQL snapshots of a database are dissimilar to backup files created in SQL Server\. Also, the granularity of when a backup and restore occurs might be different between SQL Server and Aurora PostgreSQL\. | 
| BEGIN DISTRIBUTED TRANSACTION | Functionality related to this syntax isn't supported\. | 
| CREATE, ALTER, DROP BROKER PRIORITY | Functionality related to these commands isn't supported\. | 
| Bulk copy in and out | Functionality related to bulk copy isn't supported\. | 
| BULK INSERT | This syntax isn't supported\. | 
| CERTENCODED function | This function isn't supported\. | 
| CERTID function | This function isn't supported\. | 
| CERTPRIVATEKEY function | This function isn't supported\. | 
| CERTPROPERTY function | This function isn't supported\. | 
| SQL keywords `CLUSTERED` and `NONCLUSTERED` for indexes and constraints | Babelfish accepts and ignores the `CLUSTERED` and `NONCLUSTERED` keywords\. | 
| Collation, index on type dependant on the ICU library | An index on a user\-defined type that depends on the ICU collation library \(the library used by Babelfish\) isn't invalidated when the version of the library is changed\. For more information about collations, see [Babelfish collation support](babelfish-collations.md)\. | 
| COLLATIONPROPERTY function | Collation properties are only implemented for the supported Babelfish BBF collations\. For more information about collations, see [Babelfish collation support](babelfish-collations.md)\. | 
| Column default | When creating a column default, the constraint name is ignored\. To drop a column default, use the following syntax: `ALTER TABLE...ALTER COLUMN..DROP DEFAULT...` | 
| Column name: IDENTITYCOL | This column name isn't supported\. | 
| Column name: $IDENTITY | This column name isn't supported\. | 
| Column name: $ROWGUID | This column name isn't supported\. | 
| COLUMNPROPERTY | This function isn't supported\. | 
| Blank column names with no column alias | The `sqlcmd` and `psql` utilities handle columns with blank names differently: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/babelfish-compatibility.html)  | 
| Column name case | Column names are stored as lowercase in the PostgreSQL catalogs and are returned to the client in lowercase if you run a `SELECT` statement\. In general, all schema identifiers are stored in lowercase in the PostgreSQL catalogs\. For more information, see [SQL\-SYNTAX\-IDENTIFIERS](https://www.postgresql.org/docs/current/sql-syntax-lexical.html#SQL-SYNTAX-IDENTIFIERS) in the PostgreSQL documentation\. | 
| Virtual computed columns \(non\-persistent\) | Virtual computed columns are created as persistent\. | 
| Column attributes | ROWGUIDCOL, SPARSE, FILESTREAM, MASKED aren't supported\. | 
| CREATE, ALTER, DROP COLUMN ENCRYPTION KEY |   Functionality related to these commands isn't supported\. | 
| COLUMN MASTER KEY | Functionality related to this object type isn't supported\. | 
| `sysdatabases.cmptlevel` | `sysdatabases.cmptlevel` are always NULL\. | 
| CREATE CONTRACT | All functionality related to this command isn't supported\. | 
| CREATE, ALTER, DROP, BACKUP CERTIFICATE | Functionality related to these commands isn't supported\. | 
| CONNECTIONPROPERTY function | The unsupported properties include `local_net_address`, `client_net_address`, and `physical_net_transport`\. | 
| Constraints | PostgreSQL doesn't support turning on and turning off individual constraints\. The statement is ignored and a warning is raised\. | 
| Constraints created with DESC \(descending\) columns | Constraints are created with ASC \(ascending\) columns\. | 
| Constraints with IGNORE\_DUP\_KEY | Constraints are created without this property\. | 
| BEGIN CONVERSATION TIMER | This syntax isn't supported\. | 
| END, MOVE CONVERSATION | This syntax isn't supported\. | 
| GET CONVERSATION GROUP | This syntax isn't supported\. | 
| CREATE, ALTER, DROP CREDENTIAL | This syntax isn't supported\. | 
| ALTER DATABASE | This syntax isn't supported\. | 
| ALTER DATABASE SCOPED CREDENTIAL | This syntax isn't supported\. | 
| CREATE DATABASE keywords and clauses | Options except COLLATE and CONTAINMENT=NONE aren't supported\. The COLLATE clause is accepted and is always set to the value of `babelfishpg_tsql.server_collation_name`\. | 
| CREDENTIAL | Functionality related to this object type isn't supported\. | 
| Cross\-database object references | Objects with three\-part names aren't supported\. For more information, see: [Using Babelfish with a single database or multiple databases](babelfish.md#babelfish-single_vs_multi_db)\. | 
| Remote object references | Objects with four\-part names aren't supported\.\. For more information, see: [Configuring a database for Babelfish](babelfish-configuration.md)\. | 
| CRYPTOGRAPHIC PROVIDER | Functionality related to this object type isn't supported\. | 
| Cursors \(updatable\) | Updatable cursors aren't supported\. | 
| Cursors \(global\) | GLOBAL cursors aren't supported\. | 
| Cursor \(fetch behaviors\) | The following cursor fetch behaviors aren't supported: FETCH PRIOR, FIRST, LAST, ABSOLUTE, RELATIVE | 
| Cursor\-typed \(variables and parameters\) | Cursor\-typed variables and parameters aren't supported\. | 
| CROSS APPLY | Lateral joins aren't supported\. | 
| CREATE, ALTER, DROP CRYPTOGRAPHIC PROVIDER | This syntax isn't supported\. | 
| Cursor Options | SCROLL, KEYSET, DYNAMIC, FAST\_FORWARD, SCROLL\_LOCKS, OPTIMISTIC, TYPE\_WARNING, FOR UPDATE | 
| DBCC commands  | DBCC commands aren't supported\. | 
|  Database ID values are different on Babelfish  |  The `master` and `tempdb` databases won't be database IDs 1 and 2\.  | 
| CREATE DATABASE case\-sensitive collation  | Case\-sensitive collations aren't supported with the CREATE DATABASE statement\. | 
| ALTER DATABASE, ALTER DATABASE SET | Functionality related to these commands isn't supported\. | 
| CREATE, ALTER, DROP DATABASE ENCRYPTION KEY | Functionality related to these commands isn't supported\. | 
| Contained databases | Contained databases with logins authenticated at the database level rather than at the server level aren't supported\. | 
| CREATE, ALTER, DROP DATABASE AUDIT SPECIFICATION | Functionality related to this object type isn't supported\. | 
| CREATE, ALTER EXTERNAL DATA SOURCE | Functionality related to this object type isn't supported\. | 
| ROWVERSION data type | This data type isn't supported\. | 
| TIMESTAMP data type | This data type isn't supported\. The SQL Server TIMESTAMP type is unrelated to PostgreSQL TIMESTAMP\. | 
| CREATE, DROP DEFAULT | Functionality related to this object type isn't supported\. | 
| DENY | This syntax isn't supported\. | 
| DIAGNOSTIC SESSION | Functionality related to this object type isn't supported\. | 
| BEGIN DIALOG CONVERSATION | This syntax isn't supported\. | 
| DROP statements that drop multiple objects | This functionality is supported only for tables, views, functions, and procedures\. | 
| DROP IF EXISTS | This syntax isn't supported for USER and SCHEMA objects\. It's supported for the objects TABLE, VIEW, PROCEDURE, FUNCTION, and DATABASE\. | 
| Data encryption | Data encryption isn't supported\. | 
| Encryption | Built\-in functions and statements don't support encryption\. | 
| ENCRYPT\_CLIENT\_CERT connections | Client certificate connections aren't supported\. | 
| CREATE, ALTER, DROP ENDPOINT | This syntax isn't supported\. | 
| EXECUTE AS statement | This statement isn't supported\. | 
| EXECUTE AS SELF clause | This clause isn't supported in functions, procedures, or triggers\. | 
| CREATE\.\.\. EXECUTE AS OWNER clause | EXECUTE AS OWNER or CALLER is supported for permission, but not for name resolution\. | 
| EXECUTE AS USER clause | This clause isn't supported in functions, procedures, or triggers\. | 
| EXECUTE with AS LOGIN or AT option | This syntax isn't supported\. | 
| Using EXECUTE to call a SQL function | This syntax isn't supported\. | 
| EVENTDATA function | This function isn't supported\. | 
| CREATE, DROP EVENT NOTIFICATION | This functionality isn't supported\. | 
| CREATE, ALTER, DROP EVENT SESSION | Functionality related to this syntax isn't supported\. | 
| CREATE EXTERNAL FILE FORMAT | This syntax isn't supported\. | 
| SQL keyword clause `ON filegroup` | This clause is currently ignored\. | 
| Foreign Key constraints referencing database name | Foreign Key constraints that reference the database name aren't supported\. | 
| CREATE, ALTER, DROP FULLTEXT CATALOG | Functionality related to this syntax isn't supported\. | 
| CREATE, ALTER, DROP FULLTEXT INDEX | Functionality related to this syntax isn't supported\. | 
| CREATE, ALTER, DROP FULLTEXT STOPLIST | Functionality related to this syntax isn't supported\. | 
| Full\-text Search | Full\-text search Built\-in Functions and statements aren't supported\. | 
| ALTER FUNCTION | This syntax isn't supported\. | 
| Function declarations with > 100 parameters | Function declarations that contain more than 100 parameters aren't supported\. | 
| Function calls that includes DEFAULT as a parameter value | DEFAULT isn't a supported parameter value for a function call\. | 
| Function calls that include :: | Function calls that include :: aren't supported\. | 
| Functions, externally defined | External functions, including SQL Common Language Runtime \(CLR\) aren't supported\. | 
| GEOMETRY | Data type and all associated functionality isn't supported\. | 
| GEOGRAPHY | Data type and all associated functionality isn't supported\. | 
| GET\_TRANSMISSION\_STATUS | This function isn't supported\. | 
| Graph functionality | All SQL graph functionality isn't supported\. | 
| GRANT | This command isn't supported\. | 
| GROUP BY ALL clause | This syntax isn't supported\. | 
| GROUP BY ROLLUP clause | This syntax isn't supported\. | 
| GROUP BY CUBE clause | This syntax isn't supported\. | 
| ALTER DATABASE SET HADR | This syntax isn't supported\. | 
| HASHBYTES function | The only supported algorithms are: MD5, SHA1, and SHA256 | 
| HIERARCHYID | The data type and methods aren't supported\. | 
| Hints | Hints aren't supported for joins, queries, or tables\. | 
| INFORMATION\_SCHEMA catalog | Information schema views aren't supported\. | 
| Identifiers exceeding 63 characters | PostgreSQL supports a maximum of 63 characters for identifiers\. Babelfish converts identifiers longer than 63 characters to a name that includes a hash of the original name\. | 
| Identifiers with leading dot characters | Identifiers that start with a `.` aren't supported\. | 
| Identifiers \(variables or parameters\) with multiple leading @ characters | Identifiers that start with more than one leading `@` aren't supported\. | 
| Identifiers, table or column names that contain @ or \]\] characters | Table or column names that contain an `@` sign or square brackets aren't supported\. | 
| System\-defined @@ variables | Babelfish doesn't support system\-defined @@variables other than these: @@VERSION, @@SPID, @@ROWCOUNT, @@TRANCOUNT, @@IDENTITY, @@ERROR, @@FETCH\_STATUS, @@MAX\_PRECISION, @@SERVERNAME, @@DATEFIRST | 
| IDENTITY columns support | IDENTITY columns are supported for data types tinyint, smallint, int, bigint\. numeric, and decimal\. SQL Server supports precision to 38 places for data types `numeric` and `decimal` in IDENTITY columns\.PostgreSQL supports precision to 19 places for data types `numeric` and `decimal` in IDENTITY columns\. | 
| Indexes with IGNORE\_DUP\_KEY | Syntax that creates an index that includes IGNORE\_DUP\_KEY creates an index as if this property is omitted\. | 
| Indexes with more than 32 columns | An index can't include more than 32 columns\. Included index columns count toward the maximum in PostgreSQL but not in SQL Server\. | 
| Inline indexes | Inline indexes aren't supported\. | 
| Indexes \(clustered\) | Clustered indexes are created as if NONCLUSTERED was specified\. | 
| Indexed views | Functionality related to this object type isn't supported\. | 
| CREATE COLUMNSTORE INDEX | This syntax isn't supported\. | 
| ALTER INDEX | This syntax isn't supported\. | 
| DROP INDEX | This syntax is supported only in the form `DROP index_name ON table_name`\. | 
| CREATE, ALTER, DROP SPATIAL INDEX | This syntax isn't supported\. | 
| CREATE, ALTER, DROP XML INDEX | This syntax isn't supported\. | 
| Index clauses | The following clauses are ignored: FILLFACTOR, ALLOW\_PAGE\_LOCKS, ALLOW\_ROW\_LOCKS, PAD\_INDEX, STATISTICS\_NORECOMPUTE, OPTIMIZE\_FOR\_SEQUENTIAL\_KEY, SORT\_IN\_TEMPDB, DROP\_EXISTING, ONLINE, COMPRESSION\_DELAY, MAXDOP, DATA\_COMPRESSION | 
| INSERT\.\.\. TOP | This syntax isn't supported\. | 
| INSERT\.\.\. DEFAULT VALUES | This syntax isn't supported\. | 
| JSON | Data types, built\-in functions, and statements aren't supported\. | 
| KILL | This syntax isn't supported\. | 
| CREATE, ALTER, DROP EXTERNAL LANGUAGE | This syntax isn't supported\. | 
| CREATE, ALTER, DROP EXTERNAL LIBRARY | Functionality related to this object type | 
| CREATE, ALTER LOGIN clauses are supported with limited syntax | The CREATE LOGIN\.\.\. PASSWORD clause, \.\.\.DEFAULT\_DATABASE clause, and \.\.\.DEFAULT\_LANGUAGE clause are supported\. The ALTER LOGIN\.\.\. PASSWORD clause is supported, but ALTER LOGIN\.\.\. OLD\_PASSWORD clause isn't supported\. Only a login who is a sysadmin member can modify a password\. | 
| LOGIN objects | All options for LOGIN objects except: PASSWORD, DEFAULT\_DATABASE, ENABLE, DISABLE | 
| LOGINPROPERTY function | This function isn't supported\. | 
| CREATE, ALTER, DROP, OPEN/CLOSE, BACKUP/RESTORE MASTER KEY | Functionality related to this syntax isn't supported\. | 
| Materialized views | Materialized views aren't supported\. | 
| CREATE, ALTER, DROP MESSAGE TYPE | This syntax isn't supported\. | 
| NEWSEQUENTIALID function | Implemented as NEWID; sequential behavior isn't guaranteed\. | 
| MERGE | This syntax isn't supported\. | 
| NEWSEQUENTIALID function | When calling `NEWSEQUENTIALID`, PostgreSQL generates a new GUID value\. | 
| NEXT VALUE FOR sequence clause | This syntax isn't supported\. | 
| NOT FOR REPLICATION clause | This syntax is accepted and ignored\.  | 
| SET NUMERIC\_ROUNDABORT ON | This setting isn't supported\. | 
| OBJECTPROPERTY | This function isn't supported\. | 
| OBJECTPROPERTYEX | This function isn't supported\. | 
| ODBC escape functions | ODBC escape functions aren't supported\. | 
| OUTER APPLY | SQL Server lateral joins aren't supported\. PostgreSQL provides SQL syntax that allows a lateral join, but the behavior isn't identical\. For more information about PostgreSQL lateral joins, see [the PostgreSQL documentation](https://www.postgresql.org/docs/14/queries-table-expressions.html)\. | 
| OUTPUT clause is supported with the following limitations | OUTPUT and OUTPUT INTO aren't supported in the same DML query\. References to non\-target table of UPDATE or DELETE operations in an OUTPUT clause aren't supported\. OUTPUT\.\.\. DELETED \*, INSERTED \* aren't supported in the same query\. | 
| Procedure or function parameter limit | Babelfish supports a maximum of 100 parameters for a procedure or function\. | 
| Partitioning | Table and index partitioning isn't supported\. | 
| CREATE, ALTER, DROP PARTITION FUNCTION | This syntax isn't supported\. | 
| CREATE, ALTER, DROP PARTITION SCHEME | This syntax isn't supported\. | 
| ALTER PROCEDURE statement | This syntax isn't supported\. | 
| Procedures WITH RECOMPILE | WITH RECOMPILE \(when used in conjunction with the DECLARE and EXECUTE statements\) isn't supported\. | 
| Procedure declarations with more than 100 parameters | Declarations with more than 100 parameters aren't supported\. | 
| Procedure calls that includes DEFAULT as a parameter value | DEFAULT isn't a supported parameter value\. | 
| Procedures, externally defined | Externally defined procedures, including SQL Common Language Runtime \(CLR\), aren't supported\. | 
| Invoking a procedure whose name is in a variable | Using a variable as a procedure name isn't supported\. | 
| Procedure versioning | Procedure versioning isn't supported\. | 
| CREATE, ALTER, DROP QUEUE | Functionality related to this syntax isn't supported\. | 
| READTEXT | This syntax isn't supported\. | 
| CREATE, ALTER, DROP REMOTE SERVICE BINDING | Functionality related to this object type\. | 
| CREATE, ALTER, DROP RESOURCE POOL | This syntax isn't supported\. | 
| CREATE, ALTER, DROP EXTERNAL RESOURCE POOL | Functionality related to this syntax isn't supported\. | 
| CREATE, ALTER, DROP RESOURCE GOVERNOR | Functionality related to this syntax isn't supported\. | 
| RESTORE statement | Aurora PostgreSQL snapshots of a database are dissimilar to backup files created in SQL Server\. Also, the granularity of when the backup and restore occurs might be different between SQL Server and Aurora PostgreSQL\. | 
| REVERT | This syntax isn't supported\. | 
| REVOKE | This syntax isn't supported\. | 
| CREATE, ALTER, DROP ROLE | This syntax isn't supported\. | 
| Server\-level roles other than `sysadmin` | Server\-level roles \(other than `sysadmin`\) aren't supported\. | 
| Database\-level roles other than `db_owner` | Database\-level roles other than db\_owner aren't supported\. | 
| ROLLBACK: table variables don't support transactional rollback | Processing might be interrupted if a rollback occurs in a session with table variables\. | 
| CREATE, ALTER, DROP ROUTE | Functionality related to this syntax isn't supported\. | 
| ROWGUIDCOL | Currently ignored\. Queries referencing `$GUIDGOL` cause a syntax error\. | 
| Row\-level security | Row\-level security with CREATE SECURITY POLICY and inline table\-valued functions isn't supported\. | 
| ROWSET functions | The following ROWSET functions aren't supported: OPENXML, OPENJSON, OPENROWSET, OPENQUERY, OPENDATASOURCE | 
| ROWVERSION | This data type isn't supported\. | 
| CREATE/DROP RULE | Functionality related to this syntax isn't supported\. | 
| ALTER DATABASE SCOPED CONFIGURATION | This syntax isn't supported\. | 
| ALTER SCHEMA | This syntax isn't supported\. | 
| CREATE SCHEMA\.\.\. supporting clauses | You can use the CREATE SCHEMA command to create an empty schema\. Use additional commands to create schema objects\. | 
| CREATE, ALTER, DROP SECURITY POLICY | This syntax isn't supported\. | 
| CREATE, ALTER, DROP SEARCH PROPERTY LIST | This syntax isn't supported\. | 
| SEQUENCE object support | SEQUENCE objects are supported for data types tinyint, smallint, int, bigint, numeric, and decimal\. Aurora PostgreSQL supports precision to 19 places for data types numeric and decimal in a SEQUENCE\. | 
| CREATE, ALTER, DROP SERVER AUDIT | Functionality related to this object type isn't supported\. | 
| CREATE, ALTER, DROP SERVER AUDIT SPECIFICATION | Functionality related to this object type isn't supported\. | 
| SELECT PIVOT/UNPIVOT | This syntax isn't supported\. | 
| SELECT TOP x PERCENT WHERE x <> 100 | This syntax isn't supported\. | 
| SELECT TOP\.\.\. WITH TIES | This syntax isn't supported\. | 
| SELECT\.\.\. FOR XML PATH, ELEMENTS | Syntax is supported without the ELEMENTS clause\. | 
| SELECT\.\.\. FOR XML RAW, ELEMENTS | Syntax is supported without the ELEMENTS clause\. | 
| SEND | This syntax isn't supported\. | 
| ALTER SERVER CONFIGURATION | This syntax isn't supported\. | 
| SERVERPROPERTY | Unsupported properties: BuildClrVersion, ComparisonStyle, ComputerNamePhysicalNetBIOS, EditionID, HadrManagerStatus, InstanceDefaultDataPath, InstanceDefaultLogPath, InstanceName, IsAdvancedAnalyticsInstalled, IsBigDataCluster, IsClustered, IsFullTextInstalled, IsHadrEnabled, IsIntegratedSecurityOnly, IsLocalDB, IsPolyBaseInstalled, IsXTPSupported, LCID, LicenseType, MachineName, NumLicenses, ProcessID, ProductBuild, ProductBuildType, ProductLevel, ProductUpdateLevel, ProductUpdateReference, ResourceLastUpdateDateTime, ResourceVersion, ServerName, SqlCharSet, SqlCharSetName, SqlSortOrder, SqlSortOrderName, FilestreamShareName, FilestreamConfiguredLevel, FilestreamEffectiveLevel | 
| CREATE, ALTER, DROP SERVER ROLE |  ALTER SERVER ROLE is supported only for `sysadmin`\. All other syntax is unsupported\. Babelfish provides the T\-SQL user with an experience that is similar to SQL Server for the concepts of a login \(server principal\), a database, and a database user \(database principal\)\. In Babelfish, currently only the `dbo` user is available in user databases\. Currently, to operate as the `dbo` user, a login must be a member of the server\-level `sysadmin` role \(`ALTER SERVER ROLE sysadmin ADD MEMBER login`\)\. Logins without `sysadmin` role can currently access only `master` and `tempdb` as the `guest` user\. Currently, because Babelfish only supports the `dbo` user in user databases, all application users must use a login that is a `sysadmin` member\. You can't create a user with lesser privileges, such as read\-only on certain tables\.  | 
| CREATE, ALTER, DROP SERVICE | Functionality related to this syntax isn't supported\. | 
| ALTER SERVICE, BACKUP/RESTORE SERVICE MASTER KEY clause | Functionality related to this syntax isn't supported\. | 
| SESSIONPROPERTY | Unsupported properties: ANSI\_NULLS, ANSI\_PADDING, ANSI\_WARNINGS, ARITHABORT, CONCAT\_NULL\_YIELDS\_NULL, NUMERIC\_ROUNDABORT | 
| Service broker functionality | Service broker functionality isn't supported\. | 
| SERVICE MASTER KEY | Functionality related to this object type isn't supported\. | 
| SET ANSI\_NULL\_DFLT\_OFF ON | This setting isn't supported\. | 
| SET ANSI\_NULL\_DFLT\_ON OFF | This setting isn't supported\. | 
| SET ANSI\_PADDING OFF | This setting isn't supported\. | 
| SET ANSI\_WARNINGS OFF | This setting isn't supported\. | 
| SET ARITHABORT OFF | This setting isn't supported\. | 
| SET ARITHIGNORE ON | This setting isn't supported\. | 
| SET CONTEXT\_INFO | This syntax isn't supported\. | 
| SET CURSOR\_CLOSE\_ON\_COMMIT ON | This setting isn't supported\. | 
| SET DATEFORMAT | This syntax isn't supported\. | 
| SET DEADLOCK\_PRIORITY | This syntax isn't supported\. | 
| SET FORCEPLAN | This syntax isn't supported\. | 
| SET FMTONLY | This syntax isn't supported\. | 
| SET LANGUAGE | This syntax isn't supported with any value other than `english` or `us_english`\. | 
| SET LOCK\_TIMEOUT | This syntax isn't supported\. | 
| SET NUMERIC\_ROUNDABORT ON | This syntax isn't supported\. | 
| SET REMOTE\_PROC\_TRANSACTIONS | This syntax isn't supported\. | 
| SET ROWCOUNT n WHERE n \!= 0 | This syntax isn't supported\. | 
| SET ROWCOUNT @variable | This syntax isn't supported\. | 
| SET SHOWPLAN\_ALL | This syntax isn't supported\. | 
| SET SHOWPLAN\_TEXT | This syntax isn't supported\. | 
| SET SHOWPLAN\_XML | This syntax isn't supported\. | 
| SET STATISTICS | This syntax isn't supported\. | 
| SET STATISTICS IO | This syntax isn't supported\. | 
| SET STATISTICS PROFILE | This syntax isn't supported\. | 
| SET STATISTICS TIME | This syntax isn't supported\. | 
| SET STATISTICS XML | This syntax isn't supported\. | 
| SET TRANSACTION ISOLATION LEVEL REPEATABLE READ | This syntax isn't supported\. | 
| SET TRANSACTION ISOLATION LEVEL SERIALIZABLE | This syntax isn't supported\. | 
| SET OFFSETS | This syntax isn't supported\. | 
| SETUSER statements | SETUSER statements aren't supported\. | 
| SHUTDOWN statement | This syntax isn't supported\. | 
| SP\_CONFIGURE | This system stored procedure isn't supported\. | 
| SQL keyword SPARSE | The keyword SPARSE is accepted and ignored\. | 
| System\-provided stored procedures are partially supported | SP\_HELPDB, SP\_GETAPPLOCK, and SP\_RELEASEAPPLOCK are supported\. All other stored procedures aren't supported\. | 
| Unquoted string values in stored procedure calls and default values | String parameters to stored procedure calls, and defaults for string parameters in CREATE PROCEDURE, are not supported\. | 
| SYNONYM | Functionality related to this object type isn't supported\. | 
| CREATE, ALTER, DROP, OPEN/CLOSE SYMMETRIC KEY | Functionality related to this object type isn't supported\. | 
| CREATE TABLE\.\.\. GRANT clause | Functionality related to this syntax isn't supported\. | 
| CREATE TABLE\.\.\. IDENTITY clause | Functionality related to this syntax isn't supported\. | 
| CREATE EXTERNAL TABLE | Functionality related to this syntax isn't supported\. | 
| Table value constructor syntax \(FROM clause\) | The unsupported syntax is for a derived table constructed with the FROM clause\. | 
| tempdb isn't reinitialized at restart | Permanent objects \(like tables and procedures\) created in tempdb aren't removed when the database is restarted\. | 
| Time precision | Babelfish supports 6\-digit precision for fractional seconds\. No adverse effects are anticipated with this behavior\. | 
| TYPEPROPERTY function | This function isn't supported\. | 
| Global temporary tables \(tables with names that start with \#\#\) | Global temporary tables aren't supported\. | 
| Temporary procedures aren't dropped automatically | This functionality isn't supported\. | 
| Temporal tables | Temporal tables aren't supported\. | 
| TEXTIMAGE\_ON filegroup | Babelfish ignores the `TEXTIMAGE_ON` *filegroup* clause\. | 
| Transaction isolation levels | READUNCOMMITTED is treated the same as READCOMMITTED\. REPEATABLEREAD, and SERIALIZABLE aren't supported\. | 
| CREATE/ALTER/ENABLE/DISABLE TRIGGER | This syntax isn't supported\. | 
| DDL trigger | DDL triggers aren't supported\. | 
| LOGON trigger | LOGON triggers aren't supported\. | 
| Triggers, externally defined | These triggers aren't supported, including SQL Common Language Runtime \(CLR\)\. | 
| INSTEAD OF triggers | INSTEAD OF triggers aren't supported\. | 
| Trigger for multiple DML actions cannot reference transition tables | Triggers that reference multiple DML actions can't reference transition tables\. | 
| CREATE, ALTER, DROP TYPE | This syntax isn't supported\. | 
| SELECT\.\.\. FOR XML AUTO | This syntax isn't supported\. | 
| SELECT\.\.\. FOR XML EXPLICIT | This syntax isn't supported\. | 
| UPDATE STATISTICS | This syntax isn't supported\. | 
| CREATE USER | This syntax isn't supported\. The PostgreSQL statement CREATE USER doesn't create a user that is equivalent to the SQL Server CREATE USER syntax\. | 
| UPDATETEXT | This syntax isn't supported\. | 
| USER | Functionality related to this object type isn't supported\. | 
| ALTER VIEW | This syntax isn't supported\. | 
| VIEW\.\.\. VIEW\_METADATA clause | This syntax isn't supported\. | 
| VIEW\.\.\. CHECK OPTION clause | This syntax isn't supported\. | 
| @@version | The format of the value returned by `@@version` is slightly different from the value returned by SQL Server\. Your code might not work correctly if it depends on the formatting of `@@version`\. | 
| WAITFOR DELAY | This syntax isn't supported\. | 
| WAITFOR, RECEIVE | This syntax isn't supported\. | 
| WAITFOR TIME | This syntax isn't supported\. | 
| WITH ENCRYPTION clause | This syntax isn't supported for functions, procedures, triggers, or views\. | 
| CREATE, ALTER, DROP WORKLOAD GROUP | Functionality related to this syntax isn't supported\. | 
| CREATE, DROP WORKLOAD CLASSIFIER | This syntax isn't supported\. | 
| WRITETEXT | This syntax isn't supported\. | 
| OPENXML | This function isn't supported\. | 
| XML data type with schema \(xmlschema\) | XML type without schema is supported\. | 
| XML methods | XML methods aren't supported, including \.VALUES, \.NODES, and other methods\. | 
| XML indexes | XML indexes aren't supported\. | 
| XPATH expressions | This syntax isn't supported\. | 
| WITH XMLNAMESPACES construct | This syntax isn't supported\. | 
| WITHOUT SCHEMABINDING clause | This clause isn't supported in functions, procedures, triggers, or views\. The object is created, but as if WITH SCHEMABINDING was specified\. | 
| CREATE, ALTER, DROP SELECTIVE XML INDEX clause | This syntax isn't supported\. | 
| CREATE, ALTER, DROP XML SCHEMA COLLECTION | This syntax isn't supported\. | 