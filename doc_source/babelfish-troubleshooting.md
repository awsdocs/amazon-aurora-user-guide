# Troubleshooting Babelfish<a name="babelfish-troubleshooting"></a>

Following, you can find troubleshooting ideas and workarounds for some Babelfish DB cluster issues\. 

**Topics**
+ [Connection failure](#babelfish-troubleshooting-connectivity)
+ [Using `pg_dump` and `pg_restore` requires extra setup](#babelfish-troubleshooting-pg-dump_pg_restore)

## Connection failure<a name="babelfish-troubleshooting-connectivity"></a>

Common causes of connection failures to a new Aurora DB cluster with Babelfish include the following:
+ **Security group doesn't allow access** – If you're having trouble connecting to a Babelfish, make sure that you added your IP address to the default Amazon EC2 security group\. You can use [https://checkip\.amazonaws\.com/](https://checkip.amazonaws.com/) to determine your IP address and then add it to your in\-bound rule for the TDS port and the PostgreSQL port\. For more information, see [Add rules to a security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#adding-security-group-rule.html) in the *Amazon EC2 User Guide*\.
+ **Mismatching SSL configurations** – If the `rds.force_ssl` parameter is turned on \(set to 1\) on Aurora PostgreSQL, then clients must connect to Babelfish over SSL\. If your client isn't set up correctly, you see an error message such as the following:

  ```
  Cannot connect to your-Babelfish-DB-cluster, 1433
  ---------------------
  ADDITIONAL INFORMATION:
  no pg_hba_conf entry for host "256.256.256.256", user "your-user-name",
  "database babelfish_db", SSL off (Microsoft SQL Server, Error: 33557097)
  ...
  ```

  This error indicates a possible SSL configuration issue between your local client and the Babelfish DB cluster, and that the cluster requires clients to use SSL \(its `rds.force_ssl` parameter is set to 1\)\. For more information about configuring SSL, see [Using SSL with a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Concepts.General.SSL.html#PostgreSQL.Concepts.General.SSL.Status) in the *Amazon RDS User Guide*\. 

  If you are using SQL Server Management Studio \(SSMS\) to connect to Babelfish and you see this error, you can choose **Encrypt connection** and **Trust server certificate** connection options on the Connection Properties pane and try again\. These settings handle the SSL connection requirement for SSMS\. 

For more information about troubleshooting Aurora connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. 

## Using `pg_dump` and `pg_restore` requires extra setup<a name="babelfish-troubleshooting-pg-dump_pg_restore"></a>

Currently, if you try to use the PostgreSQL utilities `pg_dump` and `pg_restore` to move a database from one Babelfish DB cluster to another, you see the following error message: 

```
psql:bbf.sql:29: ERROR:  role "db_owner" does not exist
psql:bbf.sql:49: ERROR:  role "dbo" does not exist
```

To workaround this issue, you first create the same logical database on the target cluster that is on the source\. Once that exists, you can create the needed roles to run `pg_dump` and `pg_restore`\. 

**To use `pg_dump` and `pg_restore` to move a database between Babelfish DB clusters**

1. Use `psql` or `pgAdmin` to connect to the target Babelfish DB cluster\. The following examples use `psql`\. For more information, see [Using psql to connect to the DB cluster](babelfish-connect-PostgreSQL.md#babelfish-connect-psql)\.

1. Create the same logical database on the target that is on the source\.

   ```
   CREATE DATABASE your-DB-name
   ```

1. Connect to the Babelfish DB instance and create the necessary roles\. 

   ```
   CREATE ROLE db_owner;
   ALTER ROLE db_owner WITH NOSUPERUSER INHERIT NOCREATEROLE NOCREATEDB NOLOGIN NOREPLICATION NOBYPASSRLS;
   CREATE ROLE dbo;
   ALTER ROLE dbo WITH NOSUPERUSER INHERIT NOCREATEROLE NOCREATEDB NOLOGIN NOREPLICATION NOBYPASSRLS;
   GRANT db_owner TO dbo GRANTED BY sysadmin;
   GRANT dbo TO sysadmin GRANTED BY sysadmin;
   ```

1. Use `pg_restore` to restore the DB instance from the source to the target\.

To learn more about using these PostgreSQL utilities, see [pg\_dump](https://www.postgresql.org/docs/13/app-pgdump.html) and [pg\_restore](https://www.postgresql.org/docs/13/app-pgrestore.html)\.