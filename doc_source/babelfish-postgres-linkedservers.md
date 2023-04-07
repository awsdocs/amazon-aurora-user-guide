# Babelfish supports linked servers<a name="babelfish-postgres-linkedservers"></a>

Babelfish for Aurora PostgreSQL supports linked servers by using the PostgreSQL `tds_fdw extension` in version 3\.1\.0\. To work with linked servers, you must install the `tds_fdw` extension\. For more information about the `tds_fdw` extension, see [Working with the supported foreign data wrappers for Amazon Aurora PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md)\.

## Installing the tds\_fdw extension<a name="babelfish-postgres-linkedservers-tdsfdw"></a>

1. Connect to your PostgreSQL DB instance on the Babelfish database in the PostgreSQL port\. Use an account that has the rds\_superuser role\.

   ```
   psql --host=your-DB-instance.aws-region.rds.amazonaws.com --port=5432 --username=test --dbname=babelfish_db --password
   ```

1. Install the tds\_fdw extension\. This is a one\-time installation process\. You don't need to reinstall when the DB cluster restarts\.

   ```
   babelfish_db=> CREATE EXTENSION tds_fdw;
   CREATE EXTENSION
   ```

## Supported functionality<a name="babelfish-postgres-linkedservers-supportfunction"></a>

Babelfish supports adding remote RDS for SQL Server or Babelfish for Aurora PostgreSQL endpoints as the linked server\. You can also add other remote SQL Server instances as linked servers\. Then, use `OPENQUERY()` to retrieve data from these linked servers\.

The following stored procedures and catalog views are supported in order to use the linked servers\.

**Stored procedures**
+ **sp\_addlinkedserver** – Babelfish doesn't support the @provstr parameter\.
+ **sp\_addlinkedsrvlogin** – You must provide an explicit remote username and password to connect to the remote data source\. Connection using the user’s self credentials isn't supported\. Babelfish supports only `@useself = true`\.

  Babelfish doesn't support the @locallogin parameter since configuring remote server access specific to local login isn't supported\. 
+ **sp\_linkedservers**
+ **sp\_helplinkedsrvlogin**
+ **sp\_dropserver**
+ **sp\_droplinkedsrvlogin** – Babelfish doesn't support the @locallogin parameter since configuring remote server access specific to local login isn't supported\.

**Catalog views**
+ **sys\.servers**
+ **sys\.linked\_logins**

## Using encryption in transit for the connection<a name="babelfish-postgres-linkedservers-encrytion"></a>

The connection from the source Babelfish for Aurora PostgreSQL server to the target remote server uses encryption in transit \(TLS/SSL\), depending on the remote server database configuration\. If the remote server isn't configured for encryption, the Babelfish server making the request to the remote database falls back to unencrypted\.

**To enforce connection encryption**
+ If the target linked server is a RDS for SQL Server instance, set `rds.force_ssl = on` for the target SQL Server instance\. For more information about SSL/TLS configuration for RDS for SQL Server, see [Using SSL with a Microsoft SQL Server DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)
+ If the target linked server is a Babelfish for Aurora PostgreSQL cluster, set `babelfishpg_tsql.tds_ssl_encrypt = on` and `ssl = on` for the target server\. For more information about SSL/TLS, see [Babelfish SSL settings and client connections](babelfish-configuration.md#babelfish-ssl)\.

## Limitations<a name="babelfish-postgres-linkedservers-limitations"></a>
+ Using a remote Babelfish for Aurora PostgreSQL cluster as a linked server from SQL Server isn't supported\.
+ Referencing remote objects using four\-part names isn't supported\.
+ Babelfish only supports passthrough SELECT statements with OPENQUERY\(\)\. Passthrough INSERT/UPDATE/DELETE statements aren't currently supported with OPENQUERY\(\)\.
+ Babelfish major version upgrade might not work if there are objects dependent on OPENQUERY\(\)\. You must ensure that any objects referencing OPENQUERY\(\) are dropped before a major version upgrade\.

## Troubleshooting<a name="babelfish-postgres-linkedservers-troubleshooting"></a>

You must provide access on the security group or the access control list \(ACL\) of the target server for the TDS port \(1433 by default\) of the source Babelfish server\. Both the source and target server need access to the TDS port\. If access isn't configured correctly, an error message similar to the following example appears when you try to query the remote server\.

```
TDS client library error: DB #: 20009, DB Msg: Unable to connect: server is unavailable or does not exist (mssql2019.aws-region.rds.amazonaws.com), OS #: 110, OS Msg: Connection timed out, Level: 9                        
```