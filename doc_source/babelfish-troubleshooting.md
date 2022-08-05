# Troubleshooting Babelfish<a name="babelfish-troubleshooting"></a>

Following, you can find troubleshooting ideas and workarounds for some Babelfish DB cluster issues\. 

**Topics**
+ [Connection failure](#babelfish-troubleshooting-connectivity)
+ [Using pg\_dump and pg\_restore requires extra setup](#babelfish-troubleshooting-pg-dump_pg_restore)

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

## Using pg\_dump and pg\_restore requires extra setup<a name="babelfish-troubleshooting-pg-dump_pg_restore"></a>

Currently, if you try to use the PostgreSQL utilities pg\_dump and pg\_restore to move a database from one Babelfish DB cluster to another, you see the following error message\. 

```
psql:bbf.sql:29: ERROR:  role "db_owner" does not exist
psql:bbf.sql:49: ERROR:  role "dbo" does not exist
```

To work around this issue, first create the same logical database on the target cluster that is on the source\. After that exists, you can create the needed roles to run pg\_dump and pg\_restore\. 

**To use pg\_dump and pg\_restore to move a database between Babelfish DB clusters**

1. Connect to your SQL Server endpoint using SQL Server Management Studio \(SSMS\)\.

1. Create the same logical database on your target that is on your source\. To restore multiple databases, make sure to create those in advance\.

   ```
   CREATE DATABASE your-DB-name
   ```

1. Use pg\_restore to restore the DB instance from the source to the target\.

To learn more about using these PostgreSQL utilities, see [pg\_dump](https://www.postgresql.org/docs/13/app-pgdump.html) and [pg\_restore](https://www.postgresql.org/docs/13/app-pgrestore.html) in the PostgreSQL documentation\.