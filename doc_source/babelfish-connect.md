# Connecting to a DB cluster with Babelfish turned on<a name="babelfish-connect"></a>

To connect to Babelfish, modify your database client configuration to connect to the endpoint of the Aurora PostgreSQL cluster running Babelfish\. Your client can use one of the following client drivers compliant with TDS version 7\.1 or higher:
+ Open Database Connectivity \(ODBC\)
+ OLE DB Driver/MSOLEDBSQL
+ Java Database Connectivity \(JDBC\)
+ Microsoft SqlClient Data Provider for SQL Server
+ \.NET Data Provider for SQL Server
+ SQL Server Native Client 11\.0 \(deprecated\)
+ OLEDB Provider/SQLOLEDB \(deprecated\)

With Babelfish, you run the following:
+ SQL Server tools, applications, and syntax on the TDS port, by default port 1433\.
+ PostgreSQL tools, applications, and syntax on the PostgreSQL port, by default port 5432\.

If you're new to connecting to an Amazon Aurora PostgreSQL database, see also [Connecting to an Amazon Aurora PostgreSQL DB cluster](Aurora.Connecting.md#Aurora.Connecting.AuroraPostgreSQL)\.

## Finding the DNS writer endpoint and port number<a name="babelfish-connect-endpoint"></a>

Use the following procedure to find your database endpoint\.

**To find your database endpoint**

1. Open the console for Babelfish\.

1. Choose **Databases** from the navigation pane\. 

   Your database should have a status of **Available**\. If it doesn't, wait until it displays as **Available**\. The status updates automatically without requiring you to refresh the page\. This process can take up to 20 minutes after creating a DB cluster\.

1. Choose your DB cluster that supports Babelfish to show its details\. 

1. On the **Connectivity & security** tab, note the available cluster **Endpoints** values\. Use the cluster endpoint for the writer instance in your connection strings for any applications that perform database write or read operations\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-database-endpoint.png)

For more information about DB cluster details, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

## Performing client authentication<a name="babelfish-authentication"></a>

Aurora PostgreSQL with Babelfish supports password authentication\. Passwords are stored in encrypted form on disk\. For more information about authentication on an Aurora PostgreSQL cluster, see [Security with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Security.md)\.

You might be prompted for credentials each time you connect to Babelfish\. Any user migrated to or created on Aurora PostgreSQL can use the same credentials on both the SQL Server port and the PostgreSQL port\. Babelfish doesn't enforce password policies, but we recommend you do the following:
+ Require a complex password that is at least eight characters long\.
+ Enforce a password expiration policy\.

To review a complete list of database users, use the command `SELECT * FROM pg_user;`\.