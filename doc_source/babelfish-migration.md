# Using Babelfish to migrate to PostgreSQL<a name="babelfish-migration"></a>

You can use Babelfish for Aurora PostgreSQL to ease migration from a SQL Server database to an Amazon Aurora PostgreSQL DB cluster\. Before migrating, review [Using Babelfish with a single database or multiple databases](babelfish.md#babelfish-single_vs_multi_db)\. 

The following high\-level overview lists the steps required to make your SQL Server application work with Babelfish: 

1. Create a new Aurora PostgreSQL DB cluster with Babelfish turned on, providing support for SQL Server T\-SQL syntax and features\. For details, see [Creating an Aurora PostgreSQL cluster with Babelfish](babelfish-create.md)\.

1. To connect to the new database, use a native SQL Server tool such as [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-ver15)\. For details, see [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)\.

1. Move your existing SQL Server schema to the new cluster\. Export the data definition language \(DDL\) from your SQL Server database\. The DDL is SQL code that describes database objects that contain user data \(like tables, indexes, and views\) and user\-written database code \(stored procedures, user\-defined functions, and triggers\)\.

   You can use SQL Studio Management Studio \(SSMS\) to export the DDL\. After connecting to your existing SQL Server instance, open the context menu for \(right\-click\) a database name\. Then choose **Tasks**, **Generate Scripts** from the context menu\. Don't forget to turn on triggers, logins, owners, and permissions\. These are turned off by default in SSMS\.

1. Run an assessment tool \(for example, the Babelfish Compass tool\) on the DDL to determine to what extent the T\-SQL code is supported by Babelfish\. Identify T\-SQL code that might require changing before running on Babelfish\.

1. Run the DDL on your new Babelfish server to recreate your schemas on Babelfish\. Make code adjustments as needed\.

1. Use the [AWS Database Migration Service \(AWS DMS\)](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.BabelFish.html) to import the data\.

1. Reconfigure your client application to connect to the Babelfish endpoint instead of SQL Server\. For details, see [Connecting to a DB cluster with Babelfish turned on](babelfish-connect.md)\.

1. Modify your application where necessary and retest\. For more information, see [Differences between Aurora PostgreSQL with Babelfish and SQL Server](babelfish-compatibility.md)\.

1. When you're satisfied with your application test results, start using your Babelfish database for production\. During this time, AWS DMS continues to incrementally replicate changes to the Babelfish database or databases\. When you're ready, stop the original database and redirect live client applications to use the Babelfish TDS port\.  