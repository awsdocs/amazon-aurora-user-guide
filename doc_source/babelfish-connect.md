# Connecting to a Babelfish DB cluster<a name="babelfish-connect"></a>

To connect to Babelfish, you connect to the endpoint of the Aurora PostgreSQL cluster running Babelfish\. Your client can use one of the following client drivers compliant with TDS version 7\.1 through 7\.4:
+ Open Database Connectivity \(ODBC\)
+ OLE DB Driver/MSOLEDBSQL
+ Java Database Connectivity \(JDBC\) version 8\.2\.2 \(mssql\-jdbc\-8\.2\.2\) and higher
+ Microsoft SqlClient Data Provider for SQL Server
+ \.NET Data Provider for SQL Server
+ SQL Server Native Client 11\.0 \(deprecated\)
+ OLE DB Provider/SQLOLEDB \(deprecated\)

With Babelfish, you run the following:
+ SQL Server tools, applications, and syntax on the TDS port, by default port 1433\.
+ PostgreSQL tools, applications, and syntax on the PostgreSQL port, by default port 5432\.

To learn more about connecting to Aurora PostgreSQL in general, see [Connecting to an Amazon Aurora PostgreSQL DB cluster](Aurora.Connecting.md#Aurora.Connecting.AuroraPostgreSQL)\. 

**Topics**
+ [Finding the writer endpoint and port number](#babelfish-connect-endpoint)
+ [Creating C\# or JDBC client connections to Babelfish](babelfish-connect-configure.md)
+ [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)
+ [Using a PostgreSQL client to connect to your DB cluster](babelfish-connect-PostgreSQL.md)

## Finding the writer endpoint and port number<a name="babelfish-connect-endpoint"></a>

To connect to your Babelfish DB cluster, you use the endpoint associated with the DB cluster's writer \(primary\) instance\. The instance must have a status of **Available**\. It can take up to 20 minutes for the instances to be available after creating the Babelfish for Aurora PostgreSQL DB cluster\.

**To find your database endpoint**

1. Open the console for Babelfish\.

1. Choose **Databases** from the navigation pane\. 

1. Choose your Babelfish for Aurora PostgreSQL DB cluster from those listed to see its details\. 

1. On the **Connectivity & security** tab, note the available cluster **Endpoints** values\. Use the cluster endpoint for the writer instance in your connection strings for any applications that perform database write or read operations\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-database-endpoint.png)

For more information about Aurora DB cluster details, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.