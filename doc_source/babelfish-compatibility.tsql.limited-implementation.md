# Using Babelfish features with limited implementation<a name="babelfish-compatibility.tsql.limited-implementation"></a>

Each new version of Babelfish adds support for features that better align with T\-SQL functionality and behavior\. Still, there are some unsupported features and differences in the current implementation\. In the following, you can find information about functional differences between Babelfish and T\-SQL, with some workarounds or usage notes\.

As of version 1\.2\.0 of Babelfish, the following features currently have limited implementations:
+ **SQL Server catalogs \(system views\)** – The catalogs `sys.sysconfigures`, `sys.syscurconfigs`, and `sys.configurations` support a single read\-only configuration only\. The `sp_configure` isn't currently supported\. For more information about the other SQL Server views implemented by Babelfish, see [Getting information from the Babelfish system catalog](babelfish-query-database.md)\. 
+ **GRANT permissions** – GRANT…TO PUBLIC is supported, but GRANT\.\.TO PUBLIC WITH GRANT OPTION is not currently supported\. 
+ **SQL Server *ownership chain* and permission mechanism limitation** – In Babelfish, the SQL Server ownership chain works for views but not for stored procedures\. This means that procedures must be granted explicit access to other objects owned by the same owner as the calling procedures\. In SQL Server, granting the caller EXECUTE permissions on the procedure is sufficient to call other objects owned by same owner\. In Babelfish, caller must also be granted permissions on the objects accessed by the procedure\. 
+ **Resolution of unqualified \(without schema name\) object references** – When a SQL object \(procedure, view, function or trigger\) references an object without qualifying it with a schema name, SQL Server resolves the object's schema name by using the schema name of the SQL object in which the reference occurs\. Currently, Babelfish resolves this differently, by using the default schema of the database user executing the procedure\. 
+ **Default schema changes, sessions, and connections** – If users change their default schema with `ALTER USER...WITH DEFAULT SCHEMA`, the change takes effect immediately in that session\. However, for other currently connected sessions belonging to the same user, the timing differs, as follows:
  + For SQL Server: – The change takes effect across all other connections for this user immediately\.
  + For Babelfish: – The change takes effect for this user for new connections only\.
+ **ROWVERSION and TIMESTAMP datatypes implementation and escape hatch setting** – The ROWVERSION and TIMESTAMP datatypes are now supported in Babelfish\. To use ROWVERSION or TIMESTAMP in Babelfish, you must change the setting for the escape hatch `babelfishpg_tsql.escape_hatch_rowversion` from its default \(strict\) to `ignore`\. The Babelfish implementation of the ROWVERSION and TIMESTAMP datatypes is mostly semantically identical to SQL Server, with the following exceptions: 
  + The built\-in @@DBTS function behaves similarly to SQL Server, but with small differences\. Rather than returning the last\-used value for `SELECT @@DBTS`, Babelfish generates a new timestamp, due to the underlying PostgreSQL database engine and its multi\-version concurrency control \(MVCC\) implementation\.
  + In SQL Server, every inserted or updated row gets a unique ROWVERSION/TIMESTAMP value\. In Babelfish, every inserted row updated by the same statement is assigned the same ROWVERSION/TIMESTAMP value\.

    For example, when an UPDATE statement or INSERT\-SELECT statement affects multiple rows, in SQL Server, the affected rows all have different values in their ROWVERSION/TIMESTAMP column\. In Babelfish \(PostgreSQL\), the rows have the same value\.
  + In SQL Server, when you create a new table with SELECT\-INTO, you can cast an explicit value \(such as NULL\) to a to\-be\-created ROWVERSION/TIMESTAMP column\. When you do the same thing in Babelfish, an actual ROWVERSION/TIMESTAMP value is assigned to each row in the new table for you, by Babelfish\.

  These minor differences in ROWVERSION/TIMESTAMP datatypes shouldn't have an adverse impact on applications running on Babelfish\. 

**Schema creation, ownership, and permissions** – Permissions to create and access objects in a schema owned by a non\-DBO user \(using `CREATE SCHEMA schema name AUTHORIZATION user name`\) differ for SQL Server and Babelfish non\-DBO users, as shown in the following table:


| Database user \(non\-DBO\) who owns the schema can do the following: | SQL Server | Babelfish | 
| --- | --- | --- | 
|  Create objects in the schema without additional grants by the DBO?  |  No  |  Yes  | 
|  Access objects created by DBO in the schema without additional grants?  |  Yes  |  No  | 