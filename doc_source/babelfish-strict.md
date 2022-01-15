# Managing Babelfish error handling<a name="babelfish-strict"></a>

Babelfish mimics SQL behavior in terms of control flow and transaction state whenever possible\. If Babelfish encounters an error, it returns an error code similar to the SQL Server error code if possible\. If Babelfish can't map the error, it returns a fixed error code \(`33557097`\)\. If an unmapped error is one of the following, the indicated result happens:
+ If it's a compile time error, Babelfish rolls back the transaction\.
+ If it's a runtime error, Babelfish ends the batch and rolls back the transaction\.
+ If it's a protocol error between the client and server, the transaction isn't rolled back\.

If an error code can't be mapped to an equivalent code and the code for a similar error is available, the error code is mapped to the alternative code\. For example, the behaviors that cause SQL Server codes `8143` and `8144` are both mapped to `8143`\.

Errors that can't be mapped don't respect a `TRY... CATCH` construct\.

You can use `@@ERROR` to return a SQL Server error code, or the `@@PGERROR` function to return a PostgreSQL error code\. You can also use the `fn_mapped_system_error_list` function to return a list of mapped error codes\. For information about PostgreSQL error codes, see [the PostgreSQL website](https://www.postgresql.org/docs/current/errcodes-appendix.html)\.

## Babelfish escape hatches<a name="babelfish-escape_hatches"></a>

To better deal with statements that might fail, Babelfish defines certain options called escape hatches\. An *escape hatch* is an option that specifies Babelfish behavior when it encounters an unsupported feature or syntax\.

You can use the `sp_babelfish_configure` stored procedure to control the settings of an escape hatch\. Use the script to set the escape hatch to `ignore` or `strict`\. If it's set to `strict`, Babelfish returns an error that you need to correct before continuing\.

Include the `server` keyword to apply the changes to the current session and on a cluster level\.

The usage is as follows:
+ To list all escape hatches and their status, plus usage information, run `sp_babelfish_configure`\.
+ To list the named escape hatches and their values, for the current session or cluster\-wide, run the command `sp_babelfish_configure 'hatch_name'` where `hatch_name` is the identifier of one or more escape hatches\. *hatch\_name* can use SQL wildcards, such as '%'\.
+ To set one or more escape hatches to the value specified, run `sp_babelfish_configure ['hatch_name' [, 'strict'|'ignore' [, 'server']]`\. To make the settings permanent on a cluster\-wide level, include the `server` keyword\. To set them for the current session only, don't use `server`\.

The string identifying the hatch \(or hatches\) might contain SQL wildcards\. For example, the following sets all syntax escape hatches to `ignore` for the Aurora PostgreSQL cluster\.

```
EXECUTE sp_babelfish_configure '%', 'ignore', 'server'
```

The Babelfish predefined escape hatches are as follows\.


| Escape hatch | Description | Default | 
| --- | --- | --- | 
| escape\_hatch\_storage\_options |  Escape hatch on any storage option used in CREATE, ALTER DATABASE, TABLE, INDEX\. This includes clauses \(LOG\) ON, TEXTIMAGE\_ON, FILESTREAM\_ON that define storage locations \(partitions, file groups\) for tables, indexes, and constraints, and also for a database\. This escape hatch setting applies to all of these clauses \(including ON \[PRIMARY\] and ON "DEFAULT"\)\. The exception is when a partition is specified for a table or index with ON partition\_scheme \(column\)\.  |  ignore  | 
| escape\_hatch\_storage\_on\_partition |  Controls Babelfish behavior related to the `ON partition_scheme column `clause when defining partitioning\. Babelfish currently doesn't implement partitioning\.  |  strict  | 
| escape\_hatch\_database\_misc\_options |  Controls Babelfish behavior related to the following options on CREATE or ALTER DATABASE: CONTAINMENT, DB\_CHAINING, TRUSTWORTHY, PERSISTENT\_LOG\_BUFFER\.  |  ignore  | 
| escape\_hatch\_language\_non\_english |  Controls Babelfish behavior related to languages other than English for onscreen messages\. Babelfish currently supports only `us_english` for onscreen messages\. SET LANGUAGE might use a variable containing the language name, so the actual language being set can only be detected at run time\.  |  strict  | 
| escape\_hatch\_login\_hashed\_password |  When ignored, suppresses the error for the `HASHED` keyword for `CREATE LOGIN` and `ALTER LOGIN`\.  |  strict  | 
| escape\_hatch\_login\_misc\_options |  When ignored, suppresses the error for other keywords besides `HASHED`, `MUST_CHANGE`, `OLD_PASSWORD`, and `UNLOCK` for `CREATE LOGIN` and `ALTER LOGIN`\.  |  strict  | 
| escape\_hatch\_login\_old\_password |  When ignored, suppresses the error for the `OLD_PASSWORD` keyword for `CREATE LOGIN` and `ALTER LOGIN`\.  |  strict  | 
| escape\_hatch\_login\_password\_must\_change |  When ignored, suppresses the error for the `MUST_CHANGE` keyword for `CREATE LOGIN` and `ALTER LOGIN`\.  |  strict  | 
| escape\_hatch\_login\_password\_unlock |  When ignored, suppresses the error for the `UNLOCK` keyword for `CREATE LOGIN` and `ALTER LOGIN`\.  |  strict  | 
| escape\_hatch\_fulltext |  Controls Babelfish behavior related to FULLTEXT features, such a DEFAULT\_FULLTEXT\_LANGUAGE in CREATE/ALTER DATABASE, CREATE FULLTEXT INDEX, or sp\_fulltext\_database\.  |  strict  | 
| escape\_hatch\_schemabinding\_function |  Controls Babelfish behavior related to the WITH SCHEMABINDING clause\. By default, the WITH SCHEMABINDING clause is ignored when specified with the CREATE or ALTER FUNCTION command\.   |  ignore  | 
| escape\_hatch\_schemabinding\_procedure |  Controls Babelfish behavior related to the WITH SCHEMABINDING clause\. By default, the WITH SCHEMABINDING clause is ignored when specified with the CREATE or ALTER PROCEDURE command\.   |  ignore  | 
| escape\_hatch\_schemabinding\_view |  Controls Babelfish behavior related to the WITH SCHEMABINDING clause\. By default, the WITH SCHEMABINDING clause is ignored when specified with the CREATE or ALTER VIEW command\.  |  ignore  | 
| escape\_hatch\_schemabinding\_trigger |  Controls Babelfish behavior related to the WITH SCHEMABINDING clause\. By default, the WITH SCHEMABINDING clause is ignored when specified with the CREATE or ALTER TRIGGER command\.  |  ignore  | 
| escape\_hatch\_index\_clustering |  Controls Babelfish behavior related to the CLUSTERED or NONCLUSTERED keywords for indexes and PRIMARY KEY or UNIQUE constraints\. When CLUSTERED is ignored, the index or constraint is still created as if NONCLUSTERED was specified\.  |  ignore  | 
| escape\_hatch\_index\_columnstore |  Controls Babelfish behavior related to the COLUMNSTORE clause\. If you specify `ignore`, Babelfish creates a regular B\-tree index\.  |  strict  | 
| escape\_hatch\_for\_replication |  Controls Babelfish behavior related to the \[NOT\] FOR REPLICATION clause when creating or altering a table\.  |  strict  | 
| escape\_hatch\_for\_replication |  Controls Babelfish behavior related to the \[NOT\] FOR REPLICATION clause when creating or altering a procedure\.  |  strict  | 
| escape\_hatch\_rowguidcol\_column |  Controls Babelfish behavior related to the ROWGUIDCOL clause when creating or altering a table\.  |  strict  | 
| escape\_hatch\_nocheck\_add\_constraint |  Controls Babelfish behavior related to the WITH CHECK or NOCHECK clause for constraints\.  |  strict  | 
| escape\_hatch\_nocheck\_existing\_constraint |  Controls Babelfish behavior related to FOREIGN KEY or CHECK constraints\.   |  strict  | 
| escape\_hatch\_constraint\_name\_for\_default |  Controls Babelfish behavior related to default constraint names\.  |  ignore  | 
| escape\_hatch\_table\_hints |  Controls the behavior of table hints specified using the WITH \(\.\.\.\) clause\.   |  ignore  | 
| escape\_hatch\_query\_hints |  Controls Babelfish behavior related to query hints\. When this option is set to ignore, the server ignores hints that use the OPTION \(\.\.\.\) clause to specify query processing aspects\. Examples include SELECT FROM \.\.\. OPTION\(MERGE JOIN HASH, MAXRECURSION 10\)\)\.  |  ignore  | 
| escape\_hatch\_join\_hints |  Controls the behavior of keywords in a JOIN operator: LOOP, HASH, MERGE, REMOTE, REDUCE, REDISTRIBUTE, REPLICATE\.  |  ignore  | 
| escape\_hatch\_session\_settings |  Controls Babelfish behavior toward unsupported session\-level SET statements\.  |  ignore  | 
| escape\_hatch\_unique\_constraint |  Controls Babelfish behavior when creating a unique index or constraint on a nullable column\.  |  strict  | 