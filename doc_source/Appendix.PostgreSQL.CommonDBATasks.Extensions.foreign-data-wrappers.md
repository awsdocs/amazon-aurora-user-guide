# Working with the supported foreign data wrappers for Amazon Aurora PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers"></a>

A foreign data wrapper \(FDW\) is a specific type of extension that provides access to external data\. For example, the `oracle_fdw` extension allows your Aurora PostgreSQL DB instance to work with Oracle databases\. 

Following, you can find information about several supported PostgreSQL foreign data wrappers\. 

**Topics**
+ [Working with Oracle databases by using the oracle\_fdw extension](#postgresql-oracle-fdw)
+ [Working with SQL Server databases by using the tds\_fdw extension](#postgresql-tds-fdw)

## Working with Oracle databases by using the oracle\_fdw extension<a name="postgresql-oracle-fdw"></a>

To access an Oracle database from your Aurora PostgreSQL DB cluster you can install and use the `oracle_fdw` extension\. This extension is a foreign data wrapper for Oracle databases\. To learn more about this extension, see the [oracle\_fdw](https://github.com/laurenz/oracle_fdw) documentation\.

The `oracle_fdw` extension is supported on Aurora PostgreSQL 12\.7 \(Amazon Aurora release 4\.2\) and higher versions\. 

**Topics**
+ [Turning on the oracle\_fdw extension](#postgresql-oracle-fdw.enabling)
+ [Example: Using a foreign server linked to an Amazon RDS for Oracle database](#postgresql-oracle-fdw.example)
+ [Working with encryption in transit](#postgresql-oracle-fdw.encryption)
+ [Understanding the pg\_user\_mappings view and permissions](#postgresql-oracle-fdw.permissions)

### Turning on the oracle\_fdw extension<a name="postgresql-oracle-fdw.enabling"></a>

To use the oracle\_fdw extension, perform the following procedure\. 

**To turn on the oracle\_fdw extension**
+ Run the following command using an account that has `rds_superuser` permissions\.

  ```
  CREATE EXTENSION oracle_fdw;
  ```

### Example: Using a foreign server linked to an Amazon RDS for Oracle database<a name="postgresql-oracle-fdw.example"></a>

The following example shows the use of a foreign server linked to an Amazon RDS for Oracle database\.

**To create a foreign server linked to an RDS for Oracle database**

1. Note the following on the RDS for Oracle DB instance:
   + Endpoint
   + Port
   + Database name

1. Create a foreign server\.

   ```
   test=> CREATE SERVER oradb FOREIGN DATA WRAPPER oracle_fdw OPTIONS (dbserver '//endpoint:port/DB_name');
   CREATE SERVER
   ```

1. Grant usage to a user who doesn't have `rds_superuser` privileges, for example `user1`\.

   ```
   test=> GRANT USAGE ON FOREIGN SERVER oradb TO user1;
   GRANT
   ```

1. Connect as `user1`, and create a mapping to an Oracle user\.

   ```
   test=> CREATE USER MAPPING FOR user1 SERVER oradb OPTIONS (user 'oracleuser', password 'mypassword');
   CREATE USER MAPPING
   ```

1. Create a foreign table linked to an Oracle table\.

   ```
   test=> CREATE FOREIGN TABLE mytab (a int) SERVER oradb OPTIONS (table 'MYTABLE');
   CREATE FOREIGN TABLE
   ```

1. Query the foreign table\.

   ```
   test=>  SELECT * FROM mytab;
   a
   ---
   1
   (1 row)
   ```

If the query reports the following error, check your security group and access control list \(ACL\) to make sure that both instances can communicate\.

```
ERROR: connection for foreign table "mytab" cannot be established
DETAIL: ORA-12170: TNS:Connect timeout occurred
```

### Working with encryption in transit<a name="postgresql-oracle-fdw.encryption"></a>

PostgreSQL\-to\-Oracle encryption in transit is based on a combination of client and server configuration parameters\. For an example using Oracle 21c, see [About the Values for Negotiating Encryption and Integrity](https://docs.oracle.com/en/database/oracle/oracle-database/21/dbseg/configuring-network-data-encryption-and-integrity.html#GUID-3A2AF4AA-AE3E-446B-8F64-31C48F27A2B5) in the Oracle documentation\. The client used for oracle\_fdw on Amazon RDS is configured with `ACCEPTED`, meaning that the encryption depends on the Oracle database server configuration\.

If your database is on RDS for Oracle, see [Oracle native network encryption](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.NetworkEncryption.html) to configure the encryption\.

### Understanding the pg\_user\_mappings view and permissions<a name="postgresql-oracle-fdw.permissions"></a>

The PostgreSQL catalog `pg_user_mapping` stores the mapping from an Aurora PostgreSQL user to the user on a foreign data \(remote\) server\. Access to the catalog is restricted, but you use the `pg_user_mappings` view to see the mappings\. In the following, you can find an example that shows how permissions apply with an example Oracle database, but this information applies more generally to any foreign data wrapper\.

In the following output, you can find roles and permissions mapped to three different example users\. Users `rdssu1` and `rdssu2` are members of the `rds_superuser` role, and `user1` isn't\. The example uses the `psql` metacommand `\du` to list existing roles\.

```
test=>  \du
                                                               List of roles
    Role name    |                         Attributes                         |                          Member of
-----------------+------------------------------------------------------------+-------------------------------------------------------------
 rdssu1          |                                                            | {rds_superuser}
 rdssu2          |                                                            | {rds_superuser}
 user1           |                                                            | {}
```

All users, including users that have `rds_superuser` privileges, are allowed to view their own user mappings \(`umoptions`\) in the `pg_user_mappings` table\. As shown in the following example, when `rdssu1` tries to obtain all user mappings, an error is raised even though `rdssu1``rds_superuser` privileges:

```
test=> SELECT * FROM pg_user_mapping;
ERROR: permission denied for table pg_user_mapping
```

Following are some examples\.

```
test=> SET SESSION AUTHORIZATION rdssu1;
SET
test=> SELECT * FROM pg_user_mappings;
 umid  | srvid | srvname | umuser | usename    |            umoptions
-------+-------+---------+--------+------------+----------------------------------
 16414 | 16411 | oradb   |  16412 | user1      |
 16423 | 16411 | oradb   |  16421 | rdssu1     | {user=oracleuser,password=mypwd}
 16424 | 16411 | oradb   |  16422 | rdssu2     |
 (3 rows)

test=> SET SESSION AUTHORIZATION rdssu2;
SET
test=> SELECT * FROM pg_user_mappings;
 umid  | srvid | srvname | umuser | usename    |            umoptions
-------+-------+---------+--------+------------+----------------------------------
 16414 | 16411 | oradb   |  16412 | user1      |
 16423 | 16411 | oradb   |  16421 | rdssu1     |
 16424 | 16411 | oradb   |  16422 | rdssu2     | {user=oracleuser,password=mypwd}
 (3 rows)

test=> SET SESSION AUTHORIZATION user1;
SET
test=> SELECT * FROM pg_user_mappings;
 umid  | srvid | srvname | umuser | usename    |           umoptions
-------+-------+---------+--------+------------+--------------------------------
 16414 | 16411 | oradb   |  16412 | user1      | {user=oracleuser,password=mypwd}
 16423 | 16411 | oradb   |  16421 | rdssu1     |
 16424 | 16411 | oradb   |  16422 | rdssu2     |
 (3 rows)
```

Because of implementation differences between `information_schema._pg_user_mappings` and `pg_catalog.pg_user_mappings`, a manually created `rds_superuser` requires additional permissions to view passwords in `pg_catalog.pg_user_mappings`\.

No additional permissions are required for an `rds_superuser` to view passwords in `information_schema._pg_user_mappings`\.

Users who don't have the `rds_superuser` role can view passwords in `pg_user_mappings` only under the following conditions:
+ The current user is the user being mapped and owns the server or holds the `USAGE` privilege on it\.
+ The current user is the server owner and the mapping is for `PUBLIC`\.

## Working with SQL Server databases by using the tds\_fdw extension<a name="postgresql-tds-fdw"></a>

You can use the PostgreSQL `tds_fdw` extension to access databases that support the tabular data stream \(TDS\) protocol, such as Sybase and Microsoft SQL Server databases\. This foreign data wrapper lets you connect from your Aurora PostgreSQL DB cluster to databases that use the TDS protocol, including Amazon RDS for Microsoft SQL Server\. For more information, see [tds\-fdw/tds\_fdw](https://github.com/tds-fdw/tds_fdw) documentation on GitHub\. 

The `tds_fdw` extension is supported on Amazon Aurora PostgreSQL version 13\.6 and higher releases\. 

### Setting up your RDS for PostgreSQL DB to use the tds\_fdw extension<a name="postgresql-tds-fdw-setting-up"></a>

In the following procedures, you can find an example of setting up and using the `tds_fdw` with an Aurora PostgreSQL DB cluster\. Before you can connect to a SQL Server database using `tds_fdw`, you need to get the following details for the instance:
+ Hostname or endpoint\. For an RDS for SQL Server DB instance, you can find the endpoint by using the Console\. Choose the Connectivity & security tab and look in the "Endpoint and port" section\. 
+ Port number\. The default port number for Microsoft SQL Server is 1433\. 
+ Name of the database\. The DB identifier\. 

You also need to provide access on the security group or the access control list \(ACL\) for the SQL Server port, 1433\. Both the Aurora PostgreSQL DB cluster and the RDS for SQL Server DB instance need access to port 1433\. If access isn't configured correctly, when you try to query the Microsoft SQL Server you see the following error message:

```
ERROR: DB-Library error: DB #: 20009, DB Msg: Unable to connect:
Adaptive Server is unavailable or does not exist (mssql2019.aws-region.rds.amazonaws.com), OS #: 0, OS Msg: Success, Level: 9
```

**To use tds\_fdw to connect to a SQL Server database**

1. Connect to your Aurora PostgreSQL DB cluster's primary instance using an account that has the `rds_superuser` role:

   ```
   psql --host=your-cluster-name-instance-1.aws-region.rds.amazonaws.com --port=5432 --username=test –-password
   ```

1. Install the `tds_fdw` extension:

   ```
   test=> CREATE EXTENSION tds_fdw;
   CREATE EXTENSION
   ```

After the extension is installed on your Aurora PostgreSQL DB cluster , you set up the foreign server\.

**To create the foreign server**

Perform these tasks on the Aurora PostgreSQL DB cluster using an account that has `rds_superuser` privileges\. 

1. Create a foreign server in the Aurora PostgreSQL DB cluster:

   ```
   test=> CREATE SERVER sqlserverdb FOREIGN DATA WRAPPER tds_fdw OPTIONS (servername 'mssql2019.aws-region.rds.amazonaws.com, port '1433', database 'tds_fdw_testing');
   CREATE SERVER
   ```

1. Grant permissions to a user who doesn't have `rds_superuser` role privileges, for example, `user1`:

   ```
   test=> GRANT USAGE ON FOREIGN SERVER sqlserverdb TO user1;
   ```

1. Connect as user1 and create a mapping to a SQL Server user:

   ```
   test=> CREATE USER MAPPING FOR user1 SERVER sqlserverdb OPTIONS (username 'sqlserveruser', password 'password');
   CREATE USER MAPPING
   ```

1. Create a foreign table linked to a SQL Server table:

   ```
   test=> CREATE FOREIGN TABLE mytab (a int) SERVER sqlserverdb OPTIONS (table 'MYTABLE');
   CREATE FOREIGN TABLE
   ```

1. Query the foreign table:

   ```
   test=> SELECT * FROM mytab;
    a
   ---
    1
   (1 row)
   ```

#### Using encryption in transit for the connection<a name="postgresql-tds-fdw-ssl-tls-encryption"></a>

The connection from Aurora PostgreSQL to SQL Server uses encryption in transit \(TLS/SSL\) depending on the SQL Server database configuration\. If the SQL Server isn't configured for encryption, the RDS for PostgreSQL client making the request to the SQL Server database falls back to unencrypted\.

You can enforce encryption for the connection to RDS for SQL Server DB instances by setting the `rds.force_ssl` parameter\. To learn how, see [Forcing connections to your DB instance to use SSL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html#SQLServer.Concepts.General.SSL.Forcing)\. For more information about SSL/TLS configuration for RDS for SQL Server, see [Using SSL with a Microsoft SQL Server DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)\. 