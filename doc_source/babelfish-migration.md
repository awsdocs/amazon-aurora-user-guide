# Migrating a SQL Server database to Babelfish for Aurora PostgreSQL<a name="babelfish-migration"></a>

You can use Babelfish for Aurora PostgreSQL to migrate an SQL Server database to an Amazon Aurora PostgreSQL DB cluster\. Before migrating, review [Using Babelfish with a single database or multiple databases](babelfish-architecture.md#babelfish-single_vs_multi_db)\. 

**Topics**
+ [Overview of the migration process](#babelfish-migration.process-summary)
+ [Evaluating and handling differences between SQL Server and Babelfish](#babelfish-migration.assessing-the-source)
+ [Import/export tools for migrating from SQL Server to Babelfish](#babelfish-migration.import-export-tools)

## Overview of the migration process<a name="babelfish-migration.process-summary"></a>

The following summary overview lists the steps required to successfully migrate your SQL Server application and make it work with Babelfish\. For information about the tools you can use for the export and import processes and for more detail, see [Import/export tools for migrating from SQL Server to Babelfish](#babelfish-migration.import-export-tools)\.

1. Create a new Aurora PostgreSQL DB cluster with Babelfish turned on\. To learn how, see [Creating a Babelfish for Aurora PostgreSQL DB cluster](babelfish-create.md)\.

   To import the various SQL artifacts exported from your SQL Server database in the steps that follow, you connect to the Babelfish cluster using a native SQL Server tool such as [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-ver15)\. For more information, see [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)\.

1. On the SQL Server database that you want to migrate, export the data definition language \(DDL\)\. The DDL is SQL code that describes database objects that contain user data \(such as tables, indexes, and views\) and user\-written database code \(such as stored procedures, user\-defined functions, and triggers\)\.

   To do so, you can use a SQL Server client tool such as Microsoft SQL Server Management Studio \(SMSS\)\. Regardless of the tool, we recommend that you export your SQL Server DDL in two distinct phases:
   + Generate the DDL for the table objects alone, without foreign keys, indexes, or other constraints\. 
   + Generate the DDL for other objects, such as views and stored procedures\.

   For more information, see [Using SQL Server Management Studio \(SSMS\) to migrate to Babelfish](#babelfish-migration.import-export-tools.ssms)\.

1. Export the data manipulation language \(DML\) for your SQL Server databases that you want to migrate\. The DML is SQL code that inserts rows into the tables in your database\.

1. Run an assessment tool to evaluate the scope of any changes that you might need to make so that Babelfish can effectively support the application running on SQL Server\. For more information, see [Evaluating and handling differences between SQL Server and Babelfish](#babelfish-migration.assessing-the-source)\. 

1. On your new Babelfish DB cluster, run the DDL to create only the schemas and tables with their primary key constraints\. 

1. To load the data, you can use the SSMS Import/Export Wizard or another tool\. For more information, see [Import/export tools for migrating from SQL Server to Babelfish](#babelfish-migration.import-export-tools)\. Make code adjustments as needed\. This process might require multiple iterations\.

1. Run the DML on your new Babelfish server to insert rows into the tables in your database\.

1. Reconfigure your client application to connect to the Babelfish endpoint rather than your SQL Server database\. For more information, see [Connecting to a Babelfish DB cluster](babelfish-connect.md)\.

1. Modify your application as needed and retest\. For more information, see [Differences between Babelfish for Aurora PostgreSQL and SQL Server](babelfish-compatibility.md)\.

You still need to assess your client\-side SQL queries\. The schemas generated from your SQL Server instance convert only the server\-side SQL code\. We recommend that you take the following steps:
+ Capture client\-side queries by using the SQL Server Profiler with the TSQL\_Replay predefined template\. This template captures T\-SQL statement information that can then be replayed for iterative tuning and testing\. You can start the profiler within SQL Server Management Studio, from the **Tools** menu\. Choose **SQL Server Profiler** to open the profiler and choose the TSQL\_Replay template\. 

  To use for your Babelfish migration, start a trace and then run your application using your functional tests\. The profiler captures the T\-SQL statements\. When you finish testing, stop the trace\. Save the result to an XML file with your client\-side queries \(File > Save as > Trace XML File for Replay\)\. 

  For more information, see [ SQL Server Profiler](https://docs.microsoft.com/en-us/sql/tools/sql-server-profiler/sql-server-profiler?view=sql-server-ver16) in the Microsoft documentation\. For more information about the TSQL\_Replay template, see [SQL Server Profiler Templates](https://docs.microsoft.com/en-us/sql/tools/sql-server-profiler/sql-server-profiler-templates?view=sql-server-ver16)\. 
+ For applications with complex client\-side SQL queries, we recommend that you use Babelfish Compass to analyze these for Babelfish compatibility\. If the analysis indicates that the client\-side SQL statements contain unsupported SQL features, review the SQL aspects in the client application and modify as needed\. 

When you're satisfied with all testing, analysis, and any modifications need for your migrated application, you can start using your Babelfish database for production\. To do so, stop the original database and redirect live client applications to use the Babelfish TDS port\.

## Evaluating and handling differences between SQL Server and Babelfish<a name="babelfish-migration.assessing-the-source"></a>

For best results, we recommend that you evaluate the generated DDL/DML and the client query code before actually migrating your SQL Server database application to Babelfish\. Depending on the version of Babelfish and the specific features of SQL Server that your application implements, you might need to refactor your application or use alternatives for functionality that isn't yet fully supported in Babelfish\. 

To assess your SQL Server application code, you can use tools such as the following: 
+ Use Babelfish Compass on the generated DDL to determine to what extent the T\-SQL code is supported by Babelfish\. Identify T\-SQL code that might need modifications before running on Babelfish\. For more information about this tool, see [ Babelfish Compass tool](https://github.com/babelfish-for-postgresql/babelfish_compass/releases/latest) on GitHub\.
+ Use the AWS Schema Conversion Tool to help with your migration\. The AWS Schema Conversion Tool supports Babelfish as a virtual target, so you don't need to have a Babelfish DB cluster running to find out how well \(or how poorly\) your migration would work\. To learn more, see [Using virtual targets](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Mapping.VirtualTargets.html) in the *AWS Schema Conversion Tool User Guide*\. 

**Note**  
Babelfish Compass is an open\-source tool\. Report any issues with Babelfish Compass through GitHub rather than to AWS Support\. 

## Import/export tools for migrating from SQL Server to Babelfish<a name="babelfish-migration.import-export-tools"></a>

As of Babelfish 2\.1\.0 \(Aurora PostgreSQL 14\.3\), you can choose one of the following tools to export your SQL code \(DDL, DML\) from SQL Server and then import into Babelfish\. 
+ Use SQL Server Management Studio \(SSMS\) to export your DDL and DML from SQL Server\. For more information, see [Using SQL Server Management Studio \(SSMS\) to migrate to Babelfish](#babelfish-migration.import-export-tools.ssms)\. 
+ Use the SSMS Import/Export Wizard\. This tool is available through the SSMS, but it's also available as a standalone tool\. For more information, see [ Welcome to SQL Server Import and Export Wizard](https://docs.microsoft.com/en-us/sql/integration-services/import-export-data/welcome-to-sql-server-import-and-export-wizard?view=sql-server-ver16) in the Microsoft documentation\. 
+ Use the Microsoft bulk data copy program \(bcp\)\. Support for bcp in Babelfish 2\.1\.0 is experimental\. Certain bcp options aren't supported, including `-E`, `-T`, and `-h`\. This utility lets you copy data from a Microsoft SQL Server instance to a data file in the format you specify\. For more information, see [bcp Utility](https://docs.microsoft.com/en-us/sql/tools/bcp-utility?view=sql-server-ver16) in the Microsoft documentation\. 

**Important**  
The converse approach isn't currently supported\. In other words, you can't use SSMS or another tool to export T\-SQL DDL from Babelfish–created objects based on the Babelfish catalogs\. The DDL generated in this way isn't reliable for production use\. 

### Using SQL Server Management Studio \(SSMS\) to migrate to Babelfish<a name="babelfish-migration.import-export-tools.ssms"></a>

You can use SQL Server Management Studio \(SSMS\) to export DDL and DML from your SQL Server instance by following these steps\. 

1. Connect to your existing SQL Server instance\.

1. Open the context menu \(right\-click\) for a database name\.

1. Choose **Tasks**, **Generate Scripts\.\.\.** from the context menu\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/babelfish-export-from-ssms.png)

1. On the **Choose Objects** pane, choose **Select specific database objects**\. Choose **Tables**, select all tables\. Choose **Next** to generate the one of two scripts to create the DDL to use on your Babelfish DB cluster\.

1. On the **Set Scripting Options** page, choose **Advanced** to open the **Options** settings\. Turn on triggers, logins, owners, and permissions\. These are turned off by default in SSMS\.

1. Save the script\.

1. Repeat the process to generate scripts for the remaining DDL\.

To export your DML, follow these steps: 

1. Open the context \(right\-click\) menu for a database name\.

1. Choose **Tasks**, **Generate Scripts** from the context menu\.

1. On the **Choose Objects** page, select the entire database or specific objects\.

1. On the **Set Scripting Options** page, choose **Advanced** and for **Types of data to script**, choose **Data only**\.

1. Save the script\.