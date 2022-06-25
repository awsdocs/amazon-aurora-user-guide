# Differences between Babelfish for Aurora PostgreSQL and SQL Server<a name="babelfish-compatibility"></a>

Babelfish provides support for T\-SQL and Microsoft SQL Server behavior by supporting SQL Server data types, syntax, and functions for Aurora PostgreSQL\. This approach allows Aurora to support both Aurora PostgreSQL and SQL Server SQL dialects\. Babelfish supports the SQL Server wire\-level protocol \(TDS\), allowing a SQL Server application to communicate natively with Aurora PostgreSQL\. Doing this helps migrate database objects, stored procedures, and application code with fewer changes\.

Babelfish provides T\-SQL semantics on top of PostgreSQL through the T\-SQL dialect using the TDS port\. Babelfish also supports PL/pgSQL semantics through the standard PostgreSQL port\. 