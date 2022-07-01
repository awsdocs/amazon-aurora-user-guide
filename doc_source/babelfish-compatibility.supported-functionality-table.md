# Supported functionality in Babelfish by version<a name="babelfish-compatibility.supported-functionality-table"></a>

In the following table you can find T\-SQL functionality supported by different Babelfish versions\. For lists of unsupported functionality, see [Unsupported functionality in Babelfish](babelfish-compatibility.tsql.limitations-unsupported.md)\. For information about various Babelfish releases, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\.


| Functionality or syntax | 2\.1\.0 | 1\.2\.1 | 1\.1\.0 | 1\.0\.0 | 
| --- |--- |--- |--- |--- |
| ALTER ROLE | ✓ | – | – | – | 
| Connecting with the SSMS object explorer connection dialog | ✓ | – | – | – | 
| CREATE ROLE | ✓ | – | – | – | 
| Create unique indexes | ✓ | ✓ | ✓ | – | 
| Cross–database references SELECT,SELECT\.\.INTO, INSERT, UPDATE, DELETE | ✓ | – | – | – | 
| Cursor\-typed parameters for input parameters only \(not output\) | ✓ | ✓ | ✓ | ✓ | 
| Data migration using the bcp client utility | ✓ | – | – | – | 
| Data migration with the SSMS Import/Export Wizard | ✓ | – | – | – | 
| Datatypes TIMESTAMP, ROWVERSION \(for usage information, see Features with limited implementation | ✓ | ✓ | – | – | 
| DROP IF EXISTS \(for SCHEMA, DATABASE, and USER objects\) | ✓ | ✓ | – | – | 
| DROP ROLE | ✓ | – | – | – | 
| Identifiers with leading dot character | ✓ | ✓ | ✓ | – | 
| INSTEAD OF triggers on tables \(tables only, not views\) | ✓ | ✓ | – | – | 
| Partial support for the SSMS object explorer | ✓ | – | – | – | 
| SET BABELFISH\_SHOWPLAN\_ALL ON \(and OFF\) | ✓ | – | – | – | 
| SET BABELFISH\_STATISTICS PROFILE ON \(OFF\) | ✓ | – | – | – | 
| SET LOCK\_TIMEOUT | ✓ | ✓ | – | – | 
| Triggers with multiple DML actions can reference transition tables | ✓ | ✓ | ✓ | – | 
| Built\-in functions: | 
| --- |
| CHARINDEX | ✓ | – | – | – | 
| CHOOSE | ✓ | ✓ | ✓ | – | 
| COLUMNS\_UPDATED | ✓ | ✓ | – | – | 
| COLUMNPROPERTY \(CharMaxLen, AllowsNull only\) | ✓ | ✓ | ✓ | – | 
| CONCAT\_WS | ✓ | ✓ | ✓ | – | 
| CURSOR\_STATUS | ✓ | ✓ | ✓ | – | 
| DATEFROMPARTS | ✓ | ✓ | ✓ | – | 
| DATETIMEFROMPARTS | ✓ | ✓ | ✓ | – | 
| FULLTEXTSERVICEPROPERTY | ✓ | ✓ | – | – | 
| HAS\_DBACCESS | ✓ | ✓ | – | – | 
| HAS\_PERMS\_BY\_NAME | ✓ | – | – | – | 
| IS\_MEMBER | ✓ | – | – | – | 
| IS\_ROLEMEMBER | ✓ | – | – | – | 
| IS\_SRVROLEMEMBER | ✓ | ✓ | – | – | 
| ISJSON | ✓ | ✓ | – | – | 
| JSON\_QUERY | ✓ | ✓ | – | – | 
| JSON\_VALUE | ✓ | ✓ | – | – | 
| ORIGINAL\_LOGIN | ✓ | ✓ | ✓ | – | 
| PATINDEX | ✓ | – | – | – | 
| SCHEMA\_NAME | ✓ | ✓ | ✓ | – | 
| SESSION\_USER | ✓ | ✓ | ✓ | – | 
| SQUARE | ✓ | ✓ | ✓ | – | 
| STRING\_SPLIT | ✓ | – | – | – | 
| SUSER\_SID | ✓ | ✓ | – | – | 
| SUSER\_SNAME | ✓ | ✓ | – | – | 
| TRIGGER\_NESTLEVEL \(without arguments only\) | ✓ | ✓ | ✓ | – | 
| UPDATE | ✓ | ✓ | – | – | 
| INFORMATION\_SCHEMA catalogs | 
| --- |
| COLUMNS | ✓ | ✓ | – | – | 
| DOMAINS | ✓ | ✓ | – | – | 
| TABLES | ✓ | ✓ | – | – | 
| TABLE\_CONSTRAINTS | ✓ | ✓ | – | – | 
| System\-defined @@ variables: | 
| --- |
| @@CURSOR\_ROWS | ✓ | ✓ | ✓ | – | 
| @@DATEFIRST | ✓ | ✓ | ✓ | ✓ | 
| @@DBTS | ✓ | ✓ | – | – | 
| @@ERROR | ✓ | ✓ | ✓ | ✓ | 
| @@ERROR=213 | ✓ | – | – | – | 
| @@FETCH\_STATUS | ✓ | ✓ | ✓ | ✓ | 
| @@IDENTITY | ✓ | ✓ | ✓ | ✓ | 
| @@LOCK\_TIMEOUT | ✓ | ✓ | ✓ | – | 
| @@LOCK\_TIMEOUT | ✓ | ✓ | – | – | 
| @@MAX\_CONNECTIONS | ✓ | ✓ | ✓ | – | 
| @@MAX\_PRECISION | ✓ | ✓ | ✓ | ✓ | 
| @@MICROSOFTVERSION | ✓ | ✓ | ✓ | – | 
| @@NESTLEVEL | ✓ | ✓ | ✓ | – | 
| @@PROCID | ✓ | ✓ | ✓ | – | 
| @@ROWCOUNT | ✓ | ✓ | ✓ | ✓ | 
| @@SERVERNAME | ✓ | ✓ | ✓ | ✓ | 
| @@SERVICENAME | ✓ | ✓ | – | – | 
| @@SPID | ✓ | ✓ | ✓ | ✓ | 
| @@TRANCOUNT | ✓ | ✓ | ✓ | ✓ | 
| @@VERSION \(note format difference as described in [T\-SQL differences in Babelfish](babelfish-compatibility.tsql.limitations.md)\. | ✓ | ✓ | ✓ | ✓ | 
| System stored procedures: | 
| --- |
| sp\_column\_privileges | ✓ | ✓ | – | – | 
| sp\_columns | ✓ | ✓ | ✓ | – | 
| sp\_columns\_100 | ✓ | ✓ | ✓ | – | 
| sp\_columns\_managed | ✓ | ✓ | ✓ | – | 
| sp\_cursor | ✓ | ✓ | ✓ | – | 
| sp\_cursor\_list | ✓ | ✓ | ✓ | – | 
| sp\_cursorclose | ✓ | ✓ | ✓ | – | 
| sp\_cursorexecute | ✓ | ✓ | ✓ | – | 
| sp\_cursorfetch | ✓ | ✓ | ✓ | – | 
| sp\_cursoropen | ✓ | ✓ | ✓ | – | 
| sp\_cursoroption | ✓ | ✓ | ✓ | – | 
| sp\_cursorprepare | ✓ | ✓ | ✓ | – | 
| sp\_cursorprepexec | ✓ | ✓ | ✓ | – | 
| sp\_cursorunprepare | ✓ | ✓ | ✓ | – | 
| sp\_databases | ✓ | ✓ | ✓ | – | 
| sp\_datatype\_info | ✓ | ✓ | ✓ | – | 
| sp\_datatype\_info\_100 | ✓ | ✓ | ✓ | – | 
| sp\_describe\_cursor | ✓ | ✓ | ✓ | – | 
| sp\_describe\_first\_result\_set | ✓ | ✓ | ✓ | – | 
| sp\_describe\_undeclared\_parameters | ✓ | ✓ | – | – | 
| sp\_fkeys | ✓ | ✓ | – | – | 
| sp\_getapplock | ✓ | ✓ | ✓ | ✓ | 
| sp\_helpdb | ✓ | ✓ | ✓ | ✓ | 
| sp\_helprole | ✓ | – | – | – | 
| sp\_helprolemember | ✓ | – | – | – | 
| sp\_helpuser | ✓ | ✓ | – | – | 
| sp\_oledb\_ro\_usrname | ✓ | ✓ | ✓ | – | 
| sp\_pkeys | ✓ | ✓ | ✓ | – | 
| sp\_pkeys | ✓ | ✓ | – | – | 
| sp\_prepare | ✓ | ✓ | ✓ | – | 
| sp\_releaseapplock | ✓ | ✓ | ✓ | ✓ | 
| sp\_special\_columns | ✓ | ✓ | – | – | 
| sp\_sproc\_columns | ✓ | – | – | – | 
| sp\_sproc\_columns\_100 | ✓ | – | – | – | 
| sp\_statistics | ✓ | ✓ | ✓ | – | 
| sp\_statistics\_100 | ✓ | ✓ | ✓ | – | 
| sp\_stored\_procedures | ✓ | ✓ | – | – | 
| sp\_table\_privileges | ✓ | ✓ | – | – | 
| sp\_tablecollations\_100 | ✓ | ✓ | ✓ | – | 
| sp\_tables | ✓ | ✓ | ✓ | – | 
| sp\_unprepare | ✓ | ✓ | ✓ | – | 
| xp\_qv | ✓ | ✓ | – | – | 
| Properties supported on the CONNECTIONPROPERTY system function | 
| --- |
| auth\_scheme | ✓ | ✓ | ✓ | ✓ | 
| client\_net\_address | ✓ | ✓ | – | – | 
| local\_net\_address | ✓ | ✓ | ✓ | – | 
| local\_tcp\_port | ✓ | ✓ | ✓ | ✓ | 
| net\_transport | ✓ | ✓ | ✓ | ✓ | 
| protocol\_type | ✓ | ✓ | ✓ | ✓ | 
| physical\_net\_transport | ✓ | ✓ | – | – | 
| Properties supported on the SERVERPROPERTY function | 
| --- |
| Babelfish | ✓ | ✓ | ✓ | ✓ | 
| Collation | ✓ | ✓ | ✓ | ✓ | 
| CollationID | ✓ | ✓ | ✓ | ✓ | 
| Edition | ✓ | ✓ | ✓ | ✓ | 
| EditionID | ✓ | ✓ | – | – | 
| EngineEdition | ✓ | ✓ | – | – | 
| IsAdvancedAnalyticsInstalled | ✓ | ✓ | – | – | 
| IsBigDataCluster | ✓ | ✓ | – | – | 
| IsFullTextInstalled | ✓ | ✓ | – | – | 
| IsIntegratedSecurityOnly | ✓ | ✓ | – | – | 
| IsLocalDB | ✓ | ✓ | – | – | 
| IsPolyBaseInstalled | ✓ | ✓ | – | – | 
| IsSingleUser | ✓ | ✓ | ✓ | ✓ | 
| IsXTPSupported | ✓ | ✓ | – | – | 
| Japanese\_CI\_AI | ✓ | – | – | – | 
| Japanese\_CI\_AS | ✓ | – | – | – | 
| Japanese\_CS\_AS | ✓ | – | – | – | 
| LicenseType | ✓ | ✓ | – | – | 
| ProductMajorVersion | ✓ | ✓ | – | – | 
| ProductMinorVersion | ✓ | ✓ | – | – | 
| ProductVersion | ✓ | ✓ | – | – | 
| ServerName | ✓ | ✓ | ✓ | ✓ | 
| SQL Server views supported by Babelfish | 
| --- |
| sys\.all\_columns | ✓ | ✓ | ✓ | ✓ | 
| sys\.all\_objects | ✓ | ✓ | ✓ | ✓ | 
| sys\.all\_sql\_modules | ✓ | – | – | – | 
| sys\.all\_views | ✓ | ✓ | ✓ | ✓ | 
| sys\.columns | ✓ | ✓ | ✓ | ✓ | 
| sys\.configurations | ✓ | ✓ | – | – | 
| sys\.data\_spaces | ✓ | – | – | – | 
| sys\.database\_files | ✓ | – | – | – | 
| sys\.database\_mirroring | ✓ | – | – | – | 
| sys\.database\_principals | ✓ | ✓ | – | – | 
| sys\.database\_role\_members | ✓ | – | – | – | 
| sys\.databases | ✓ | ✓ | ✓ | ✓ | 
| sys\.dm\_exec\_connections | ✓ | ✓ | – | – | 
| sys\.dm\_exec\_sessions | ✓ | ✓ | – | – | 
| sys\.dm\_hadr\_database\_replica\_states | ✓ | – | – | – | 
| sys\.dm\_os\_host\_info | ✓ | ✓ | – | – | 
| sys\.endpoints | ✓ | ✓ | – | – | 
| sys\.indexes | ✓ | – | – | – | 
| sys\.schemas | ✓ | ✓ | ✓ | ✓ | 
| sys\.server\_principals | ✓ | ✓ | ✓ | ✓ | 
| sys\.sql\_modules | ✓ | – | – | – | 
| sys\.sysconfigures | ✓ | ✓ | – | – | 
| sys\.syscurconfigs | ✓ | ✓ | – | – | 
| sys\.sysprocesses  | ✓ | ✓ | – | – | 
| sys\.table\_types | ✓ | ✓ | – | – | 
| sys\.tables | ✓ | ✓ | ✓ | ✓ | 
| sys\.xml\_schema\_collections | ✓ | – | – | – | 
| syslanguages | ✓ | – | – | – | 