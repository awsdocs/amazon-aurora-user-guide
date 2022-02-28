# Using Babelfish to migrate to PostgreSQL<a name="babelfish-migration"></a>

You can use Babelfish for Aurora PostgreSQL to ease migration from a SQL Server database to an Amazon Aurora PostgreSQL DB cluster\. Before migrating, review [Using Babelfish with a single database or multiple databases](babelfish.md#babelfish-single_vs_multi_db)\. 

The following high\-level overview lists the steps required to make your SQL Server application work with Babelfish: 

1. Create a new Aurora PostgreSQL DB cluster with Babelfish turned on, providing support for SQL Server T\-SQL syntax and features\. For details, see [Creating an Aurora PostgreSQL cluster with Babelfish](babelfish-create.md)\.

1. To connect to the new database, use a native SQL Server tool such as [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-ver15)\. For details, see [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)\.

1. Export the data definition language \(DDL\) for your SQL Server databases that you want to migrate\. The DDL is SQL code that describes database objects that contain user data \(such as tables, indexes, and views\) and user\-written database code \(such as stored procedures, user\-defined functions, and triggers\)\.

   You can use SQL Server Management Studio \(SSMS\) to export the DDL\. After connecting to your existing SQL Server instance, complete the following steps:

   1. Open the context menu \(right\-click\) for a database name\.

   1. Choose **Tasks**, **Generate Scripts** from the context menu\.

   1. On the **Choose Objects** page, select the entire database or specific objects\.

   1. On the **Set Scripting Options** page, choose **Advanced** and make sure that you turn on triggers, logins, owners, and permissions\. These are turned off by default in SSMS\.

   1. Save the script\.

1. Export the data manipulation language \(DML\) for your SQL Server databases that you want to migrate\. The DML is SQL code that inserts rows into the tables in your database\.

   You can use SQL Server Management Studio \(SSMS\) to export the DML\. After connecting to your existing SQL Server instance, complete the following steps:

   1. Open the context \(right\-click\) menu for a database name\.

   1. Choose **Tasks**, **Generate Scripts** from the context menu\.

   1. On the **Choose Objects** page, select the entire database or specific objects\.

   1. On the **Set Scripting Options** page, choose **Advanced** and for **Types of data to script**, choose **Data only**\.

   1. Save the script\.

1. Run an assessment tool\. For example, you can run the [ Babelfish Compass tool](https://github.com/babelfish-for-postgresql/babelfish_compass/releases/latest)\. You run this tool on the DDL to determine to what extent the T\-SQL code is supported by Babelfish\. Identify T\-SQL code that might require modifications before running on Babelfish\.
**Note**  
Because Babelfish Compass is an open\-source tool, report any issues through GitHub\. Don't report issues with Babelfish Compass to AWS Support\.

   You can also use the AWS Schema Conversion Tool to help with your migration\. The AWS Schema Conversion Tool supports Babelfish as a virtual target\. To learn more, see [Using virtual targets](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Mapping.VirtualTargets.html) in the *AWS Schema Conversion Tool User Guide*\.

1. Run the DDL on your new Babelfish server to recreate your schemas on Babelfish using SSMS or `sqlcmd`\. Make code adjustments as needed\. This process might require multiple iterations\.

1. Run the DML on your new Babelfish server to insert rows into the tables in your database\.

1. Reconfigure your client application to connect to the Babelfish endpoint instead of SQL Server\. For details, see [Connecting to a DB cluster with Babelfish turned on](babelfish-connect.md)\.

1. Modify your application where necessary and retest\. For more information, see [Differences between Aurora PostgreSQL with Babelfish and SQL Server](babelfish-compatibility.md)\.

1. When you're satisfied with your application test results, start using your Babelfish database for production\. 

   When you're ready, stop the original database and redirect live client applications to use the Babelfish TDS port\.

1. \(Optional\) Capture client\-side SQL queries, and run these queries through an assessment tool \(such as Babelfish Compass\)\. A reverse\-engineered schema only converts server\-side SQL code\. For applications with complex client\-side SQL queries, we recommend that you also analyze these for Babelfish compatibility\. If the analysis indicates that the client\-side SQL statements contain unsupported SQL features, review the SQL aspects in the client application and make modifications if necessary\.