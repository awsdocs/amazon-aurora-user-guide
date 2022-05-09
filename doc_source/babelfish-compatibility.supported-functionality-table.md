# Supported functionality in Babelfish by version<a name="babelfish-compatibility.supported-functionality-table"></a>

In the following table you can find T\-SQL functionality supported by different Babelfish versions\. For lists of unsupported functionality, see [Unsupported functionality in Babelfish](babelfish-compatibility.md#babelfish-compatibility.tsql.limitations-unsupported)\. For information about various Babelfish releases, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\.


| Functionality or syntax | 1\.2\.0 | 1\.1\.0 | 1\.0\.0 | 
| --- |--- |--- |--- |
| Create unique indexes | ✓ | ✓ | – | 
| Triggers with multiple DML actions can reference transition tables | ✓ | ✓ | – | 
| INSTEAD OF triggers on tables \(tables only, not views\) | ✓ | – | – | 
| Identifiers with leading dot character | ✓ | ✓ | – | 
| SET LOCK\_TIMEOUT | ✓ | – | – | 
| Datatypes TIMESTAMP, ROWVERSION \(for usage information, see [Features with limited implementation](babelfish-compatibility.md#babelfish-compatibility.tsql.limited-implementation)\. | ✓ | – | – | 
| DROP IF EXISTS \(for SCHEMA, DATABASE, and USER objects\)  | ✓ | – | – | 
| Built\-in functions: | 
| --- |
| CHOOSE | ✓ | ✓ | – | 
| COLUMNS\_UPDATED | ✓ | – | – | 
| COLUMNPROPERTY \(CharMaxLen, AllowsNull only\) | ✓ | ✓ | – | 
| CONCAT\_WS | ✓ | ✓ | – | 
| CURSOR\_STATUS | ✓ | ✓ | – | 
| DATEFROMPARTS | ✓ | ✓ | – | 
| DATETIMEFROMPARTS | ✓ | ✓ | – | 
| FULLTEXTSERVICEPROPERTY | ✓ | – | – | 
| HAS\_DBACCESS | ✓ | – | – | 
| IS\_SRVROLEMEMBER | ✓ | – | – | 
| ISJSON | ✓ | – | – | 
| JSON\_QUERY | ✓ | – | – | 
| JSON\_VALUE | ✓ | – | – | 
| ORIGINAL\_LOGIN | ✓ | ✓ | – | 
| SCHEMA\_NAME | ✓ | ✓ | – | 
| SESSION\_USER | ✓ | ✓ | – | 
| SQUARE | ✓ | ✓ | – | 
| SUSER\_SID | ✓ | – | – | 
| SUSER\_SNAME | ✓ | – | – | 
| TRIGGER\_NESTLEVEL \(without arguments only\) | ✓ | ✓ | – | 
| UPDATE | ✓ | – | – | 
| INFORMATION\_SCHEMA catalogs | 
| --- |
| COLUMNS | ✓ | – | – | 
| DOMAINS | ✓ | – | – | 
| TABLES | ✓ | – | – | 
| TABLE\_CONSTRAINTS | ✓ | – | – | 
| System\-defined @@ variables: | 
| --- |
| @@CURSOR\_ROWS | ✓ | ✓ | – | 
| @@DATEFIRST | ✓ | ✓ | ✓ | 
| @@DBTS | ✓ | – | – | 
| @@ERROR | ✓ | ✓ | ✓ | 
| @@FETCH\_STATUS | ✓ | ✓ | ✓ | 
| @@IDENTITY | ✓ | ✓ | ✓ | 
| @@LOCK\_TIMEOUT | ✓ | ✓ | – | 
| @@LOCK\_TIMEOUT | ✓ | – | – | 
| @@MAX\_CONNECTIONS | ✓ | ✓ | – | 
| @@MAX\_PRECISION | ✓ | ✓ | ✓ | 
| @@MICROSOFTVERSION | ✓ | ✓ | – | 
| @@NESTLEVEL | ✓ | ✓ | – | 
| @@PROCID | ✓ | ✓ | – | 
| @@ROWCOUNT | ✓ | ✓ | ✓ | 
| @@SERVERNAME | ✓ | ✓ | ✓ | 
| @@SERVICENAME | ✓ | – | – | 
| @@SPID | ✓ | ✓ | ✓ | 
| @@TRANCOUNT | ✓ | ✓ | ✓ | 
| @@VERSION \(note format difference as described in [T\-SQL differences in Babelfish](babelfish-compatibility.md#babelfish-compatibility.tsql.limitations)\. | ✓ | ✓ | ✓ | 
| System stored procedures: | 
| --- |
| sp\_column\_privileges | ✓ | – | – | 
| sp\_columns | ✓ | ✓ | – | 
| sp\_columns\_100 | ✓ | ✓ | – | 
| sp\_columns\_managed | ✓ | ✓ | – | 
| sp\_cursor | ✓ | ✓ | – | 
| sp\_cursor\_list | ✓ | ✓ | – | 
| sp\_cursorclose | ✓ | ✓ | – | 
| sp\_cursorexecute | ✓ | ✓ | – | 
| sp\_cursorfetch | ✓ | ✓ | – | 
| sp\_cursoropen | ✓ | ✓ | – | 
| sp\_cursoroption | ✓ | ✓ | – | 
| sp\_cursorprepare | ✓ | ✓ | – | 
| sp\_cursorprepexec | ✓ | ✓ | – | 
| sp\_cursorunprepare | ✓ | ✓ | – | 
| sp\_databases | ✓ | ✓ | – | 
| sp\_datatype\_info | ✓ | ✓ | – | 
| sp\_datatype\_info\_100 | ✓ | ✓ | – | 
| sp\_describe\_cursor | ✓ | ✓ | – | 
| sp\_describe\_first\_result\_set | ✓ | ✓ | – | 
| sp\_describe\_undeclared\_parameters | ✓ | – | – | 
| sp\_fkeys | ✓ | – | – | 
| sp\_getapplock | ✓ | ✓ | ✓ | 
| sp\_helpdb | ✓ | ✓ | ✓ | 
| sp\_helpuser | ✓ | – | – | 
| sp\_oledb\_ro\_usrname | ✓ | ✓ | – | 
| sp\_pkeys | ✓ | ✓ | – | 
| sp\_pkeys | ✓ | – | – | 
| sp\_prepare | ✓ | ✓ | – | 
| sp\_releaseapplock | ✓ | ✓ | ✓ | 
| sp\_special\_columns | ✓ | – | – | 
| sp\_statistics | ✓ | ✓ | – | 
| sp\_statistics\_100 | ✓ | ✓ | – | 
| sp\_stored\_procedures | ✓ | – | – | 
| sp\_table\_privileges | ✓ | – | – | 
| sp\_tablecollations\_100 | ✓ | ✓ | – | 
| sp\_tables | ✓ | ✓ | – | 
| sp\_unprepare | ✓ | ✓ | – | 
| xp\_qv | ✓ | – | – | 
| Properties supported on the CONNECTIONPROPERTY system function | 
| --- |
| auth\_scheme | ✓ | ✓ | ✓ | 
| client\_net\_address | ✓ | – | – | 
| local\_net\_address | ✓ | ✓ | – | 
| local\_tcp\_port | ✓ | ✓ | ✓ | 
| net\_transport | ✓ | ✓ | ✓ | 
| protocol\_type | ✓ | ✓ | ✓ | 
| physical\_net\_transport | ✓ | – | – | 
| Properties supported on the SERVERPROPERTY function | 
| --- |
| Babelfish | ✓ | ✓ | ✓ | 
| Collation | ✓ | ✓ | ✓ | 
| CollationID | ✓ | ✓ | ✓ | 
| Edition | ✓ | ✓ | ✓ | 
| EditionID | ✓ | – | – | 
| EngineEdition | ✓ | – | – | 
| IsAdvancedAnalyticsInstalled | ✓ | – | – | 
| IsBigDataCluster | ✓ | – | – | 
| IsFullTextInstalled | ✓ | – | – | 
| IsIntegratedSecurityOnly | ✓ | – | – | 
| IsLocalDB | ✓ | – | – | 
| IsPolyBaseInstalled | ✓ | – | – | 
| IsSingleUser | ✓ | ✓ | ✓ | 
| IsXTPSupported | ✓ | – | – | 
| LicenseType | ✓ | – | – | 
| ProductMajorVersion | ✓ | – | – | 
| ProductMinorVersion | ✓ | – | – | 
| ProductVersion | ✓ | – | – | 
| ServerName | ✓ | ✓ | ✓ | 
| SQL Server views supported by Babelfish | 
| --- |
| sys\.all\_columns | ✓ | ✓ | ✓ | 
| sys\.all\_objects | ✓ | ✓ | ✓ | 
| sys\.all\_views | ✓ | ✓ | ✓ | 
| sys\.columns | ✓ | ✓ | ✓ | 
| sys\.configurations | ✓ | – | – | 
| sys\.database\_principals | ✓ | – | – | 
| sys\.databases | ✓ | ✓ | ✓ | 
| sys\.dm\_exec\_connections | ✓ | – | – | 
| sys\.dm\_exec\_sessions | ✓ | – | – | 
| sys\.dm\_os\_host\_info | ✓ | – | – | 
| sys\.endpoints | ✓ | – | – | 
| sys\.schemas | ✓ | ✓ | ✓ | 
| sys\.server\_principals | ✓ | ✓ | ✓ | 
| sys\.sysconfigures | ✓ | – | – | 
| sys\.syscurconfigs | ✓ | – | – | 
| sys\.sysprocesses  | ✓ | – | – | 
| sys\.table\_types | ✓ | – | – | 
| sys\.tables | ✓ | ✓ | ✓ | 