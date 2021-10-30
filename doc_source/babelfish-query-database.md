# Querying a database for object information<a name="babelfish-query-database"></a>

To return information about database objects that are stored in your Aurora PostgreSQL cluster, you can query many of the same system views that you use on SQL Server\. You can access these views from either the TDS port or the PostgreSQL port\. 

For example, to find a list of schemas in your migrated database on the T\-SQL port, connect to the TDS port with sqlcmd, and use the following command\.

```
SELECT * FROM sys.schemas
```

If you migrate a single\-db or multi\-db database, Babelfish returns a list of schema names formatted in Babelfish style that includes both the SQL Server and PostgreSQL system schemas:

`mydb_dbo`

`public`

`sys`

`master_dbo`

`temp_dbo`

You get the same result set if you connect with a PostgreSQL client on the database port \(by default, 5432\)\. For example, querying the database with pgAdmin returns the following\.

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/pgadmin_list_schemas.png)

Use SQL Server *and* PostgreSQL views to return information about objects in your Aurora PostgreSQL cluster\. A few of the SQL Server views implemented by Babelfish follow:


| View name | Description | 
| --- | --- | 
| `sys.all_views` | All views in all schemas | 
| `sys.schemas` | All schemas | 
| `sys.databases` | All databases in all schemas | 
| `sys.server_principles` | All logins and roles | 
| `sys.all_objects` | All objects in all schemas | 
| `sys.tables` | All tables in a schema | 
| `sys.all_columns` | All columns in all tables and views | 
| `sys.columns` | All columns in user\-defined tables and views | 

PostgreSQL implements system catalogs that are similar to the SQL Server object catalog views\. For a complete list of system catalogs, see [System Catalogs](https://www.postgresql.org/docs/current/catalogs.html) in the PostgreSQL documentation\.