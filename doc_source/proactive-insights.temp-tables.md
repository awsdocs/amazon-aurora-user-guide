# Database is creating temporary tables on disk<a name="proactive-insights.temp-tables"></a>

Your recent on\-disk temporary table usage increased significantly, up to *percentage*\. The database is creating around *number* temporary tables per second\. This might impact performance and increase disk operations on *db\-instance*\.

**Topics**
+ [Supported engine versions](#proactive-insights.temp-tables.context.supported)
+ [Context](#proactive-insights.temp-tables.context)
+ [Likely causes for this issue](#proactive-insights.temp-tables.causes)
+ [Actions](#proactive-insights.temp-tables.actions)
+ [Relevant metrics](#proactive-insights.temp-tables.metrics)

## Supported engine versions<a name="proactive-insights.temp-tables.context.supported"></a>

This insight information is supported for all versions of Aurora MySQL\.

## Context<a name="proactive-insights.temp-tables.context"></a>

Sometimes it's necessary for the MySQL server to create an internal temporary table while processing a query\. Aurora MySQL can hold an internal temporary table in memory, where it can be processed by the TempTable or MEMORY storage engine, or stored on disk by InnoDB\. For more information, see [Internal Temporary Table Use in MySQL](https://dev.mysql.com/doc/refman/5.6/en/internal-temporary-tables.html) in the *MySQL Reference Manual*\.

## Likely causes for this issue<a name="proactive-insights.temp-tables.causes"></a>

An increase in on\-disk temporary tables indicates the use of complex queries\. If the configured memory is insufficient to store temporary tables in memory, Aurora MySQL creates the tables on disk\. This can impact performance and increase disk operations\.

## Actions<a name="proactive-insights.temp-tables.actions"></a>

We recommend different actions depending on the causes of your insight\.
+ For Aurora MySQL version 3, we recommend that you use the TempTable storage engine\.
+ Optimize your queries to return less data by selecting only necessary columns\.

  If you turn on the Performance Schema with all `statement` instruments enabled and timed, you can query `SYS.statements_with_temp_tables` to retrieve the list of queries that use temporary tables\. For more information, see [Prerequisites for Using the sys Schema](https://dev.mysql.com/doc/refman/8.0/en/sys-schema-prerequisites.html) in the MySQL documentation\.
+ Consider indexing columns that are involved in sorting and grouping operations\.
+ Rewrite your queries to avoid `BLOB` and `TEXT` columns\. These columns always use disk\.
+ Tune the following database parameters: `tmp_table_size` and `max_heap_table_size`\.

  The default values for these parameters is 16 MiB\. When using the MEMORY storage engine for in\-memory temporary tables, their maximum size is defined by the `tmp_table_size` or `max_heap_table_size` value, whichever is smaller\. When this maximum size is reached, MySQL automatically converts the in\-memory internal temporary table to an InnoDB on\-disk internal temporary table\. For more information, see [Use the TempTable storage engine on Amazon RDS for MySQL and Amazon Aurora MySQL](https://aws.amazon.com/blogs/database/use-the-temptable-storage-engine-on-amazon-rds-for-mysql-and-amazon-aurora-mysql/)\.
**Note**  
When explicitly creating MEMORY tables with CREATE TABLE, only the `max_heap_table_size` variable determines how large a table can grow\. There is also no conversion to an on\-disk format\.

## Relevant metrics<a name="proactive-insights.temp-tables.metrics"></a>

The following Performance Insights metrics are related to this insight:
+ Created\_tmp\_disk\_tables
+ Created\_tmp\_tables

For more information, see [Created\_tmp\_disk\_tables](https://dev.mysql.com/doc/refman/8.0/en/server-status-variables.html#statvar_Created_tmp_disk_tables) in the MySQL documentation\.