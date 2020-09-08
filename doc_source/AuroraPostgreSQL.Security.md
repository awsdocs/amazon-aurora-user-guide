# Security with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Security"></a>

Security for Amazon Aurora PostgreSQL is managed at three levels:
+ To control who can perform Amazon RDS management actions on Aurora MySQL DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your AWS account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Identity and Access Management in Amazon Aurora](UsingWithRDS.IAM.md)\.

  If you are using IAM to access the Amazon RDS console, you must first sign on to the AWS Management Console with your IAM user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ Aurora DB clusters must be created in an Amazon Virtual Private Cloud \(VPC\)\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora DB clusters in a VPC, you use a VPC security group\. These endpoint and port connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.

  Aurora PostgreSQL supports db\.r4 and db\.t3 instance classes with default VPC only\. With default VPC tenancy, the VPC runs on shared hardware\. With dedicated VPC tenancy, the VPC runs on a dedicated hardware instance\. For more information about instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. For more information about default and dedicated VPC tenancy, see [Dedicated Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the [Amazon EC2 User Guide for Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)\.
+ To authenticate login and permissions for an Amazon Aurora DB cluster, you can take the same approach as with a stand\-alone instance of PostgreSQL\.

  Commands such as `CREATE ROLE`, `ALTER ROLE`, `GRANT`, and `REVOKE` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For more information, see [Client Authentication](https://www.postgresql.org/docs/9.6/static/client-authentication.html) in the PostgreSQL documentation\.
**Note**  
The Salted Challenge Response Authentication Mechanism \(SCRAM\) is not supported with Aurora PostgreSQL\.

When you create an Amazon Aurora PostgreSQL DB instance, the master user has the following default privileges:
+  `LOGIN` 
+  `NOSUPERUSER` 
+  `INHERIT` 
+  `CREATEDB` 
+  `CREATEROLE` 
+  `NOREPLICATION` 
+  `VALID UNTIL 'infinity'` 

To provide management services for each DB cluster, the `rdsadmin` user is created when the DB cluster is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account will result in an error\.

## Restricting Password Management<a name="RestrictPasswordMgmt"></a>

You can restrict who can manage database user passwords to a special role\. By doing this, you can have more control over password management on the client side\.

You enable restricted password management with the static parameter `rds.restrict_password_commands` and use a role called `rds_password`\. When the parameter `rds.restrict_password_commands` is set to 1, only users that are members of the `rds_password` role can run certain SQL commands\. The restricted SQL commands are commands that modify database user passwords and password expiration time\. 

To use restricted password management, your DB cluster must be running Amazon Aurora for PostgreSQL 10\.6 or higher\. Because the `rds.restrict_password_commands` parameter is static, changing this parameter requires a database restart\.

When a database has restricted password management enabled, if you try to run restricted SQL commands you get the following error: ERROR: must be a member of rds\_password to alter passwords\.

Following are some examples of SQL commands that are restricted when restricted password management is enabled\.

```
postgres=> CREATE ROLE myrole WITH PASSWORD 'mypassword';
postgres=> CREATE ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole WITH PASSWORD 'mypassword';
postgres=> ALTER ROLE myrole VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole RENAME TO myrole2;
```

Some `ALTER ROLE` commands that include `RENAME TO` might also be restricted\. They might be restricted because renaming a PostgreSQL role that has an MD5 password clears the password\. 

The `rds_superuser` role has membership for the `rds_password` role by default, and you can't change this\. You can give other roles membership for the `rds_password` role by using the `GRANT` SQL command\. We recommend that you give membership to `rds_password` to only a few roles that you use solely for password management\. These roles require the `CREATEROLE` attribute to modify other roles\.

Make sure that you verify password requirements such as expiration and needed complexity on the client side\. We recommend that you restrict password\-related changes by using your own client\-side utility\. This utility should have a role that is a member of `rds_password` and has the `CREATEROLE` role attribute\.

## Securing Aurora PostgreSQL Data with SSL<a name="AuroraPostgreSQL.Security.SSL"></a>

Amazon RDS supports Secure Socket Layer \(SSL\) encryption for Aurora PostgreSQL DB clusters\. Using SSL, you can encrypt a connection between your applications and your Aurora PostgreSQL DB clusters\. You can also force all connections to your Aurora PostgreSQL DB cluster to use SSL\. 

For general information about SSL support and PostgreSQL databases, see [SSL Support](https://www.postgresql.org/docs/11/libpq-ssl.html) in the PostgreSQL documentation\. For information about using an SSL connection over JDBC, see [Configuring the Client](https://jdbc.postgresql.org/documentation/head/ssl-client.html) in the PostgreSQL documentation\.

**Topics**
+ [Requiring an SSL Connection to an Aurora PostgreSQL DB cluster](#AuroraPostgreSQL.Security.SSL.Requiring)
+ [Determining the SSL Connection Status](#AuroraPostgreSQL.Security.SSL.Status)

SSL support is available in all AWS Regions for Aurora PostgreSQL\. Amazon RDS creates an SSL certificate for your Aurora PostgreSQL DB cluster when the DB cluster is created\. If you enable SSL certificate verification, then the SSL certificate includes the DB cluster endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. 

**To connect to an Aurora PostgreSQL DB cluster over SSL**

1. Download the certificate\.

   For information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Cluster](UsingWithRDS.SSL.md)\.

1. Import the certificate into your operating system\.

1. Connect to your Aurora PostgreSQL DB cluster over SSL\.

   When you connect using SSL, your client can choose to verify the certificate chain or not\. If your connection parameters specify `sslmode=verify-ca` or `sslmode=verify-full`, then your client requires the RDS CA certificates to be in their trust store or referenced in the connection URL\. This requirement is to verify the certificate chain that signs your database certificate\.

   When a client, such as psql or JDBC, is configured with SSL support, the client first tries to connect to the database with SSL by default\. If the client can't connect with SSL, it reverts to connecting without SSL\. The default `sslmode` mode used is different between libpq\-based clients \(such as psql\) and JDBC\. The libpq\-based clients default to `prefer`, where JDBC clients default to `verify-full`\.

   Use the `sslrootcert` parameter to reference the certificate, for example `sslrootcert=rds-ssl-ca-cert.pem`\.

The following is an example of using psql to connect to an Aurora PostgreSQL DB cluster\.

```
$ psql -h testpg.cdhmuqifdpib.us-east-1.rds.amazonaws.com -p 5432 \
    "dbname=testpg user=testuser sslrootcert=rds-ca-2015-root.pem sslmode=verify-full"
```

### Requiring an SSL Connection to an Aurora PostgreSQL DB cluster<a name="AuroraPostgreSQL.Security.SSL.Requiring"></a>

You can require that connections to your Aurora PostgreSQL DB cluster use SSL by using the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to 0 \(off\)\. You can set the `rds.force_ssl` parameter to 1 \(on\) to require SSL for connections to your DB cluster\. Updating the `rds.force_ssl` parameter also sets the PostgreSQL `ssl` parameter to 1 \(on\) and modifies your DB cluster's `pg_hba.conf` file to support the new SSL configuration\.

You can set the `rds.force_ssl` parameter value by updating the DB cluster parameter group for your DB cluster\. If the DB cluster parameter group isn't the default one, and the `ssl` parameter is already set to 1 when you set `rds.force_ssl` to 1, you don't need to reboot your DB cluster\. Otherwise, you must reboot your DB cluster for the change to take effect\. For more information on parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

When the `rds.force_ssl` parameter is set to 1 for a DB cluster, you see output similar to the following when you connect, indicating that SSL is now required:

```
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
psql (9.3.12, server 9.4.4)
WARNING: psql major version 9.3, server major version 9.4.
Some psql features might not work.
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

postgres=>
```

### Determining the SSL Connection Status<a name="AuroraPostgreSQL.Security.SSL.Status"></a>

The encrypted status of your connection is shown in the logon banner when you connect to the DB cluster\.

```
Password for user master: 
psql (9.3.12) 
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256) 
Type "help" for help.   

postgres=>
```

You can also load the `sslinfo` extension and then call the `ssl_is_used()` function to determine if SSL is being used\. The function returns `t` if the connection is using SSL, otherwise it returns `f`\.

```
postgres=> create extension sslinfo;
CREATE EXTENSION

postgres=> select ssl_is_used();
 ssl_is_used
---------
t
(1 row)
```

You can use the `select ssl_cipher()` command to determine the SSL cipher:

```
postgres=> select ssl_cipher();
ssl_cipher
--------------------
DHE-RSA-AES256-SHA
(1 row)
```

 If you enable `set rds.force_ssl` and restart your DB cluster, non\-SSL connections are refused with the following message:

```
$ export PGSSLMODE=disable
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
psql: FATAL: no pg_hba.conf entry for host "host.ip", user "someuser", database "postgres", SSL off
$
```

For information about the `sslmode` option, see [Database Connection Control Functions](https://www.postgresql.org/docs/11/libpq-connect.html#LIBPQ-CONNECT-SSLMODE) in the PostgreSQL documentation\.