# Migrating a SQL Server database to Babelfish for Aurora PostgreSQL<a name="babelfish-migration"></a>

You can use Babelfish for Aurora PostgreSQL to migrate an SQL Server database to an Amazon Aurora PostgreSQL DB cluster\. Before migrating, review [Using Babelfish with a single database or multiple databases](babelfish-architecture.md#babelfish-single_vs_multi_db)\. 

**Topics**
+ [Overview of the migration process](#babelfish-migration.process-summary)
+ [Evaluating and handling differences between SQL Server and Babelfish](#babelfish-migration.assessing-the-source)
+ [Import/export tools for migrating from SQL Server to Babelfish](#babelfish-migration.import-export-tools)

## Overview of the migration process<a name="babelfish-migration.process-summary"></a>

The following summary lists the steps required to successfully migrate your SQL Server application and make it work with Babelfish\. For information about the tools you can use for the export and import processes and for more detail, see [Import/export tools for migrating from SQL Server to Babelfish](#babelfish-migration.import-export-tools)\.

1. Create a new Aurora PostgreSQL DB cluster with Babelfish turned on\. To learn how, see [Creating a Babelfish for Aurora PostgreSQL DB cluster](babelfish-create.md)\.

   To import the various SQL artifacts exported from your SQL Server database, connect to the Babelfish cluster using a SQL Server tool such as [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-ver15)\. For more information, see [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)\.

1. On the SQL Server database that you want to migrate, export the data definition language \(DDL\)\. The DDL is SQL code that describes database objects that contain user data \(such as tables, indexes, and views\) and user\-written database code \(such as stored procedures, user\-defined functions, and triggers\)\.

   For more information, see [Using SQL Server Management Studio \(SSMS\) to migrate to Babelfish](#babelfish-migration.import-export-tools.ssms)\.

1. Run an assessment tool to evaluate the scope of any changes that you might need to make so that Babelfish can effectively support the application running on SQL Server\. For more information, see [Evaluating and handling differences between SQL Server and Babelfish](#babelfish-migration.assessing-the-source)\.

1.  To load the data, we recommend using the AWS DMS with either Babelfish or Aurora PostgreSQL as a target endpoint based on your migration requirements\. Make sure to modify the columns with the recommended Babelfish data types\. To do so, see the [ Prerequisites to using Babelfish as a target for AWS DMS](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Babelfish.html#CHAP_Target.Babelfish.Prerequisites)\.  

1. On your new Babelfish DB cluster, run the DDL within your specified T\-SQL database to create only the schemas, user\-defined data types, and tables with their primary key constraints\.

1. Use AWS DMS to migrate your data from SQL Server to Babelfish tables\. For continuous replication using SQL Server Change Data Capture or SQL Replication, use Aurora PostgreSQL instead of Babelfish as the endpoint\. To do so, see the [ Using Babelfish for Aurora PostgreSQL as a target for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.PostgreSQL.html#CHAP_Target.PostgreSQL.Babelfish)\.

1. When the data load completes, create all the remaining T\-SQL objects that support the application on your Babelfish cluster\.

1.  Reconfigure your client application to connect to the Babelfish endpoint instead of your SQL Server database\. For more information, see [Connecting to a Babelfish DB cluster](babelfish-connect.md)\.

1. Modify your application as needed and retest\. For more information, see [Differences between Babelfish for Aurora PostgreSQL and SQL Server](babelfish-compatibility.md)\. 

You still need to assess your client\-side SQL queries\. The schemas generated from your SQL Server instance convert only the server\-side SQL code\. We recommend that you take the following steps:
+ Capture client\-side queries by using the SQL Server Profiler with the TSQL\_Replay predefined template\. This template captures T\-SQL statement information that you can then replay for iterative tuning and testing\. You can start the profiler within SQL Server Management Studio from the **Tools** menu\. Choose **SQL Server Profiler** to open the profiler and choose the TSQL\_Replay template\.

  To use for your Babelfish migration, start a trace and then run your application using your functional tests\. The profiler captures the T\-SQL statements\. When you finish testing, stop the trace\. Save the result to an XML file with your client\-side queries \(File > Save as > Trace XML File for Replay\)\.

  For more information, see [ SQL Server Profiler](https://docs.microsoft.com/en-us/sql/tools/sql-server-profiler/sql-server-profiler?view=sql-server-ver16) in the Microsoft documentation\. For more information about the TSQL\_Replay template, see [SQL Server Profiler Templates](https://docs.microsoft.com/en-us/sql/tools/sql-server-profiler/sql-server-profiler-templates?view=sql-server-ver16)\.
+ For applications with complex client\-side SQL queries, we recommend that you use Babelfish Compass to analyze these queries for Babelfish compatibility\. If the analysis indicates that the client\-side SQL statements contain unsupported SQL features, review the SQL aspects in the client application and modify as needed\.
+ You can also capture the SQL queries as extended events \(\.xel format\)\. To do so, use the SSMS XEvent Profiler\. After generating the \.xel file, extract the SQL statements into \.xml files that Compass can then process\. For more information, see [ Use the SSMS XEvent Profler](https://learn.microsoft.com/en-us/sql/relational-databases/extended-events/use-the-ssms-xe-profiler?view=sql-server-ver16) in the Microsoft documentation\.

When you're satisfied with all testing, analysis, and any modifications needed for your migrated application, you can start using your Babelfish database for production\. To do so, stop the original database and redirect live client applications to use the Babelfish TDS port\.

## Evaluating and handling differences between SQL Server and Babelfish<a name="babelfish-migration.assessing-the-source"></a>

 For best results, we recommend that you evaluate the generated DDL/DML and the client query code before actually migrating your SQL Server database application to Babelfish\. Depending on the version of Babelfish and the specific features of SQL Server that your application implements, you might need to refactor your application or use alternatives for functionality that aren't fully supported yet in Babelfish\.
+ To assess your SQL Server application code, use Babelfish Compass on the generated DDL to determine how much T\-SQL code is supported by Babelfish\. Identify T\-SQL code that might need modifications before running on Babelfish\. For more information about this tool, see [ Babelfish Compass tool](https://github.com/babelfish-for-postgresql/babelfish_compass/releases/latest) on GitHub\.

You can use Generate Script Wizard with SQL Server Management Studio \(SSMS\) to generate the SQL file that is assessed by Babelfish Compass or AWS Schema Conversion Tool CLI\. We recommend the following steps to streamline the assessment\.

1. On the **Choose Objects** page, choose **Script entire database and all database objects**\.   
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-wizard-choose-objects.png)

1. For the **Set Scripting Options**, choose **Save as script file** as a **Single script file**\.   
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-wizard-set-scripting-options.png)

1. Choose **Advanced** to change the default scripting options to identify features that normally are set to false for a full assessment:
   + Script Change Tracking to True
   + Script Full\-Text Indexes to True
   + Script Triggers to True
   + Script Logins to True
   + Script Owner to True
   + Script Object\-Level Permissions to True
   + Script Collations to True  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-advanced-scripting-options.png)

1. Perform the remaining steps in the wizard to generate the file\.

**Note**  
Babelfish Compass is an open\-source tool\. Report any issues with Babelfish Compass through GitHub instead of to AWS Support\.

## Import/export tools for migrating from SQL Server to Babelfish<a name="babelfish-migration.import-export-tools"></a>

  We recommend that you use AWS DMS as the primary tool for migrating from SQL Server to Babelfish\. However, Babelfish supports several other ways to migrate data using SQL Server tools that includes the following\.
+ SQL Server Integration Services \(SSIS\) for all versions of Babelfish\. For more information, see [ Migrate from SQL Server to Aurora PostgreSQL using SSIS and Babelfish](http://aws.amazon.com/blogs/database/migrate-from-sql-server-to-aurora-postgresql-using-ssis-and-babelfish/)\. 
+ Use the SSMS Import/Export Wizard for Babelfish versions 2\.1\.0 and later\. This tool is available through the SSMS, but it's also available as a standalone tool\. For more information, see [ Welcome to SQL Server Import and Export Wizard](https://docs.microsoft.com/en-us/sql/integration-services/import-export-data/welcome-to-sql-server-import-and-export-wizard?view=sql-server-ver16) in the Microsoft documentation\.
+ The Microsoft bulk data copy program \(bcp\) utility lets you copy data from a Microsoft SQL Server instance to a data file in the format you specify\. For more information, see [bcp Utility](https://docs.microsoft.com/en-us/sql/tools/bcp-utility?view=sql-server-ver16) in the Microsoft documentation\. Babelfish now supports the data migration using the BCP client and the bcp utility now supports `-E` flag \(for identity columns\) and \-b flag \(for batching inserts\)\. Certain bcp options aren't supported, including `-C`, `-T`, `-G`, `-K`, `-R`, `-V`, and `-h`\.

### Using SQL Server Management Studio \(SSMS\) to migrate to Babelfish<a name="babelfish-migration.import-export-tools.ssms"></a>

We recommend generating separate files for each of the specific object types\. You can use the Generate Scripts wizard in SSMS for each set of DDL statements first, and then modify the objects as a group to fix any issues found during the assessment\.

Perform these steps to migrate the data using AWS DMS or other data migration methods\. Run these create script types first for a better and faster approach to load the data on the Babelfish tables in Aurora PostgreSQL\. 

1. Run `CREATE SCHEMA` statements\. 

1. Run `CREATE TYPE` statements to create user\-defined data types\. 

1. Run basic `CREATE TABLE` statements with the primary keys or unique constraints\.

Perform the data load using the recommended import/export tool\. Run the modified scripts for the following steps to add the remaining database objects\. You need the create table statements to run these scripts for the constraints, triggers, and indexes\. After the scripts generate, delete the create table statements\.

1. Run `ALTER TABLE` statements for the check constraints, foreign key constraints, default constraints\.

1. Run `CREATE TRIGGER` statements\.

1. Run `CREATE INDEX` statements\.

1. Run `CREATE VIEW` statements\.

1. Run `CREATE STORED PROCEDURE` statements\.

**To generate scripts for each object type**

Use the following steps to create the basic create table statements using the Generate Scripts wizard in SSMS\. Follow the same steps to generate scripts for the different object types\.

1. Connect to your existing SQL Server instance\.

1. Open the context \(right\-click\) menu for a database name\.

1. Choose **Tasks**, and then choose **Generate Scripts\.\.\.**\.

1. On the **Choose Objects** pane, choose **Select specific database objects**\. Choose **Tables**, select all tables\. Choose **Next** to continue\.    
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-choose-objects-tables.png)

1. On the **Set Scripting Options** page, choose **Advanced** to open the **Options** settings\. To generate the basic create table statements, change the following default values:  
   + Script Defaults to False\.
   + Script Extended Properties to False\. Babelfish does not support extended properties\.
   + Script Check Constraints to False\. Script Foreign Keys to False\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-advance-scripting-def-values.png)

1. Choose **OK**\.

1. On the **Set Scripting Options** page, choose **Save as script file** and then choose the **Single script file** option\. Enter your **File name**\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-set-scripting-def-values.png)

1. Choose **Next** to view the **Summary wizard** page\.

1. Choose **Next** to start the script generation\.

   You can continue to generate scripts for the othe object types in the wizard\. Instead of choosing **Finish** after the file is saved, choose the **Previous** button three times to go back to the **Choose Objects** page\. Then repeate the steps in the wizard to generate scripts for the other object types\.