# Getting information from the Babelfish system catalog<a name="babelfish-query-database"></a>

You can obtain information about the database objects that are stored in your Babelfish cluster by querying many of the same system views as used in SQL Server\. Each new release of Babelfish adds support for more system views\. For a list of available views currently available, see the [SQL Server system catalog views](#system-catalog-table) table\. 

These system views provide information from the system catalog \(`sys.schemas`\)\. In the case of Babelfish, these views contain both SQL Server and PostgreSQL system schemas\. To query Babelfish for system catalog information, you can use the TDS port or the PostgreSQL port, as shown in the following examples\.
+ **Query the T\-SQL port using `sqlcmd` or another SQL Server client**\.

  ```
  1> SELECT * FROM sys.schemas
  2> GO
  ```

  This query returns SQL Server and Aurora PostgreSQL system schemas, as shown in the following\.

  ```
  name      
  ---------------------------------------------------------
  demographic_dbo          
  public                                                    
  sys                           
  master_dbo
  tempdb_dbo
  ...
  ```
+ **Query the PostgreSQL port using `psql` or `pgAdmin`**\. This example uses the `psql` list schemas metacommand \(`\dn`\):

  ```
  babelfish_db=> \dn
  ```

  The query returns the same result set as that returned by `sqlcmd` on the T\-SQL port\.

  ```
          List of schemas
               Name              
  ------------------------------
  
   demographic_dbo           
  
   public                       
   sys                          
   master_dbo                   
   tempdb_dbo                   
  ...
  ```

## SQL Server system catalogs available in Babelfish<a name="babelfish-query-database.system-catalogs"></a>

In the following table you can find the SQL Server views currently implemented in Babelfish\. For more information about the system catalogs in SQL Server, see [System Catalog Views \(Transact\-SQL\)](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/catalog-views-transact-sql?view=sql-server-ver16) in Microsoft documentation\.<a name="system-catalog-table"></a>


| View name | Description or Babelfish limitation \(if any\) | 
| --- | --- | 
| `sys.all_columns` | All columns in all tables and views | 
| `sys.all_objects` | All objects in all schemas | 
| `sys.all_sql_modules` | The union of `sys.sql_modules` and `sys.system_sql_modules` | 
| `sys.all_views` | All views in all schemas | 
| `sys.columns` | All columns in user\-defined tables and views | 
| `sys.configurations` | Babelfish support limited to a single read\-only configuration\. | 
| `sys.data_spaces` | Contains a row for each data space\. This can be a filegroup, partition scheme, or FILESTREAM data filegroup\. | 
| `sys.database_files` | A per\-database view that contains one row for each file of a database as stored in the database itself\. | 
| `sys.database_mirroring` | For information, see [sys\.database\_mirroring](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-database-mirroring-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\.  | 
| `sys.database_principals` | For information, see [sys\.database\_principals](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.database_role_members` | For information, see [sys\.database\_role\_members](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-database-role-members-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.databases` | All databases in all schemas | 
| `sys.dm_exec_connections` | For information, see [sys\.dm\_exec\_connections](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.dm_exec_sessions` | For information, see [sys\.dm\_exec\_sessions](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\.  | 
| `sys.dm_hadr_database_replica_states` | For information, see [sys\.dm\_hadr\_database\_replica\_states](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.dm_os_host_info` | For information, see [sys\.dm\_os\_host\_info](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-host-info-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.endpoints` | For information, see [sys\.endpoints](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-endpoints-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.indexes` | For information, see [sys\.indexes](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.languages` | For information, see [sys\.languages](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-fulltext-languages-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\.  | 
| `sys.schemas` | All schemas | 
| `sys.server_principals` | All logins and roles | 
| `sys.sql_modules` | For information, see [sys\.sql\_modules](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-sql-modules-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.sysconfigures` | Babelfish support limited to a single read\-only configuration\. | 
| `sys.syscurconfigs` | Babelfish support limited to a single read\-only configuration\. | 
| `sys.sysprocesses` | For information, see [sys\.sysprocesses](https://docs.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.system_sql_modules` | For information, see [sys\.system\_sql\_modules](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-system-sql-modules-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.table_types` | For information, see [sys\.table\_types](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 
| `sys.tables` | All tables in a schema | 
| `sys.xml_schema_collections` | For information, see [sys\.xml\_schema\_collections](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-xml-schema-collections-transact-sql?view=sql-server-ver16) in Microsoft Transact\-SQL documentation\. | 

PostgreSQL implements system catalogs that are similar to the SQL Server object catalog views\. For a complete list of system catalogs, see [System Catalogs](https://www.postgresql.org/docs/current/catalogs.html) in the PostgreSQL documentation\.

## DDL exports supported by Babelfish<a name="babelfish-ddl-exports"></a>

For Babelfish 2\.4\.0 and 3\.1\.0 versions, Babelfish supports DDL exports from various tools\. For example, you can use this functionality from SQL Server Management Studio \(SSMS\) to generate the data definition scripts for various objects in a Babelfish for Aurora PostgreSQL database\. You can then use the generated DDL commands in this script to create the same objects in another Babelfish for Aurora PostgreSQL or SQL Server database\. 

Babelfish supports DDL exports for the following objects in the specified versions\.


| List of objects | 2\.4\.0 | 3\.1\.0 | 
| --- | --- | --- | 
| User tables | Yes | Yes | 
| Primary keys | Yes | Yes | 
| Foreign keys | Yes | Yes | 
| Unique constraints | Yes | Yes | 
| Indexes | Yes | Yes | 
| Check constraints | Yes | Yes | 
| Views | Yes | Yes | 
| Stored procedures | Yes | Yes | 
| User\-defined functions | Yes | Yes | 
| Table\-valued functions | Yes | Yes | 
| Triggers | Yes | Yes | 
| User Defined Datatypes | No | No | 
| User Defined Table Types | No | No | 
| Users | No | No | 
| Logins | No | No | 
| Sequences | No | No | 
| Roles | No | No | 

### Limitations with the exported DDLs<a name="babelfish-ddl-exports-limitations"></a>
+ **Scripting multiple objects at once** – When you try to perform DDL export for multiple objects in a database at the same time, SSMS produces an error when there is at least one view involved\. As a workaround, script only one view at a time, and don't combine the view with other objects\.
+ **Use escape hatches before recreating the objects with the exported DDLs** – Babelfish doesn't support all the commands in the exported DDL script\. Use escape hatches to avoid errors caused when recreating the objects from the DDL commands in Babelfish\. For more information on escape hatches, see [Managing Babelfish error handling with escape hatches](babelfish-strict.md)
+ **Objects containing CHECK constraints with explicit COLLATE clauses** – The scripts with these objects generated from a SQL Server database have different but equivalent collations as in the Babelfish database\. For example, a few collations, such as sql\_latin1\_general\_cp1\_cs\_as, sql\_latin1\_general\_cp1251\_cs\_as, and latin1\_general\_cs\_as are generated as latin1\_general\_cs\_as, which is the closest Windows collation\.