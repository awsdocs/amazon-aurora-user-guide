# Using Babelfish for Aurora PostgreSQL<a name="babelfish"></a>

Babelfish for Aurora PostgreSQL extends your Aurora PostgreSQL DB cluster with the ability to accept database connections from SQL Server clients\. With Babelfish, applications that were originally built for SQL Server can work directly with Aurora PostgreSQL with few code changes compared to a traditional migration and without changing database drivers\. For more information about migrating, see [Migrating a SQL Server database to Babelfish for Aurora PostgreSQL](babelfish-migration.md)\.

Babelfish provides an additional endpoint for an Aurora PostgreSQL database cluster that allows it to understand the SQL Server wire\-level protocol and commonly used SQL Server statements\. Client applications that use the Tabular Data Stream \(TDS\) wire protocol can connect natively to the TDS listener port on Aurora PostgreSQL\. To learn more about TDS, see [\[MS\-TDS\]: Tabular Data Stream Protocol](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-tds/b46a581a-39de-4745-b076-ec4dbb7d13ec) on the Microsoft website\.

**Note**  
Babelfish for Aurora PostgreSQL supports TDS versions 7\.1 through 7\.4\.

Babelfish also provides access to data using the native PostgreSQL connection\. By default, both SQL dialects supported by Babelfish are available through their native wire protocols at the following ports: 
+ SQL Server dialect \(T\-SQL\), clients connect to port 1433\. 
+ PostgreSQL dialect \(PL/pgSQL\), clients connect to port 5432\.

Babelfish runs the Transact\-SQL \(T\-SQL\) language with some differences\. For more information, see [Differences between Babelfish for Aurora PostgreSQL and SQL Server](babelfish-compatibility.md)\. 

In the following sections, you can find information about setting up and using a Babelfish for Aurora PostgreSQL DB cluster\.

**Topics**
+ [Babelfish limitations](babelfish-limitations.md)
+ [Understanding Babelfish architecture and configuration](babelfish-understanding-overview-howitworks.md)
+ [Creating a Babelfish for Aurora PostgreSQL DB cluster](babelfish-create.md)
+ [Migrating a SQL Server database to Babelfish for Aurora PostgreSQL](babelfish-migration.md)
+ [Connecting to a Babelfish DB cluster](babelfish-connect.md)
+ [Working with Babelfish](working-with-babelfish-usage-notes-features.md)
+ [Troubleshooting Babelfish](babelfish-troubleshooting.md)
+ [Turning off Babelfish](babelfish-remove.md)
+ [Babelfish version updates](babelfish-information.md)
+ [Babelfish for Aurora PostgreSQL reference](USER_AuroraPostgreSQL_Babelfish_Reference.md)