# Security with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Security"></a>

For a general overview of Aurora security, see [Security in Amazon Aurora](UsingWithRDS.md)\. You can manage security for Amazon Aurora PostgreSQL at a few different levels:
+ To control who can perform Amazon RDS management actions on Aurora PostgreSQL DB clusters and DB instances, use AWS Identity and Access Management \(IAM\)\. IAM handles the authentication of user identity before the user can access the service\. It also handles authorization, that is, whether the user is allowed to do what they're trying to do\. IAM database authentication is an additional authentication method that you can choose when you create your Aurora PostgreSQL DB cluster\. For more information, see [Identity and access management in Amazon Aurora](UsingWithRDS.IAM.md)\.

  If you do use IAM with your Aurora PostgreSQL DB cluster, sign in to the AWS Management Console with your IAM credentials first, before opening the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ Make sure to create Aurora DB clusters in a virtual public cloud \(VPC\) based on the Amazon VPC service\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora DB clusters in a VPC, use a VPC security group\. You can make these endpoint and port connections by using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.

  The supported VPC tenancy depends on the DB instance class used by your Aurora PostgreSQL DB clusters\. With `default` VPC tenancy, the VPC runs on shared hardware\. With `dedicated` VPC tenancy, the VPC runs on a dedicated hardware instance\. The burstable performance DB instance classes support default VPC tenancy only\. The burstable performance DB instance classes include the db\.t3 and db\.t4g DB instance classes\. All other Aurora PostgreSQL DB instance classes support both default and dedicated VPC tenancy\.

  For more information about instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. For more information about `default` and `dedicated` VPC tenancy, see [Dedicated instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\.
+ To grant permissions to the PostgreSQL databases running on your Amazon Aurora DB cluster, you can take the same general approach as with stand\-alone instances of PostgreSQL\. Commands such as `CREATE ROLE`, `ALTER ROLE`, `GRANT`, and `REVOKE` work just as they do in on\-premises databases, as does directly modifying databases, schemas, and tables\.

  PostgreSQL manages privileges by using *roles*\. The `rds_superuser` role is the most privileged role on an Aurora PostgreSQL DB cluster\. This role is created automatically, and it's granted to the user that creates the DB cluster \(the master user account, `postgres` by default\)\. To learn more, see [Understanding PostgreSQL roles and permissions](#Appendix.PostgreSQL.CommonDBATasks.Roles)\. 
**Note**  
The Salted Challenge Response Authentication Mechanism \(SCRAM\), an alternative to PostgreSQL's default message digest \(MD5\) algorithm used by PostgreSQL for user authentication, isn't supported by Aurora PostgreSQL\.

## Understanding PostgreSQL roles and permissions<a name="Appendix.PostgreSQL.CommonDBATasks.Roles"></a>

When you create an Aurora PostgreSQL DB cluster using the AWS Management Console, an administrator account is created at the same time\. By default, its name is `postgres`, as shown in the following screenshot:

![\[The default login identity for Credentials in the Create database page is postgres.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/default-login-identity-apg-rpg.png)

You can choose another name rather than accept the default \(`postgres`\)\. If you do, the name you choose must start with a letter and be between 1 and 16 alphanumeric characters\. For simplicity's sake, we refer to this main user account by its default value \(`postgres`\) throughout this guide\.

 If you use the `create-db-cluster` AWS CLI rather than the AWS Management Console, you create the user name by passing it with the `master-username` parameter\. For more information, see [Create an Aurora PostgreSQL DB cluster](CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL.md#CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster)\.

Whether you use the AWS Management Console, the AWS CLI, or the Amazon RDS API, and whether you use the default `postgres` name or choose a different name, this first database user account is a member of the `rds_superuser` group and has `rds_superuser` privileges\.

**Topics**
+ [Understanding the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser)
+ [Controlling user access to the PostgreSQL database](#Appendix.PostgreSQL.CommonDBATasks.Access)
+ [Delegating and controlling user password management](#Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt)

### Understanding the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser"></a>

In PostgreSQL, a *role* can define a user, a group, or a set of specific permissions granted to a group or user for various objects in the database\. PostgreSQL commands to `CREATE USER` and `CREATE GROUP` have been replaced by the more general, `CREATE ROLE` with specific properties to distinguish database users\. A database user can be thought of as a role with the LOGIN privilege\. 

**Note**  
The `CREATE USER` and `CREATE GROUP` commands can still be used\. For more information, see [Database Roles](https://www.postgresql.org/docs/current/user-manag.html) in the PostgreSQL documentation\.

The `postgres` user is the most highly privileged database user on your Aurora PostgreSQL DB cluster\. It has the characteristics defined by the following `CREATE ROLE` statement\. 

```
CREATE ROLE postgres WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION VALID UNTIL 'infinity'
```

The properties `NOSUPERUSER`, `NOREPLICATION`, `INHERIT`, and `VALID UNTIL 'infinity'` are the default options for CREATE ROLE, unless otherwise specified\. 

By default, `postgres` has privileges granted to the the `rds_superuser` role\. The `rds_superuser` role allows the `postgres` user to do the following: 
+ Add extensions that are available for use with Aurora PostgreSQL\. For more information, see [Working with extensions and foreign data wrappers](Appendix.PostgreSQL.CommonDBATasks.md)\. 
+ Create roles for users and grant privileges to users\. For more information, see [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html) and [GRANT](https://www.postgresql.org/docs/14/sql-grant.html) in the PostgreSQL documentation\. 
+ Create databases\. For more information, see [CREATE DATABASE](https://www.postgresql.org/docs/14/sql-createdatabase.html) in the PostgreSQL documentation\.
+ Grant `rds_superuser` privileges to other user roles that don't have these privileges, and revoke such privileges as needed\. We recommend that can grant this role to others only as needed\. In other words, don't grant this role to users unless they are DBAs or system administrators\. 
+ Grant \(and revoke\) the `rds_replication` role to database users that don't have the `rds_superuser` role\. 
+ Grant \(and revoke\) the `rds_password` role to database users that don't have the `rds_superuser` role\. 
+ Obtain status information about all database connections by using the `pg_stat_activity` view\. When needed, `rds_superuser` can stop any connections by using `pg_terminate_backend` or `pg_cancel_backend`\. 

In the `CREATE ROLE postgres...` statement, you can see that the `postgres` user role specifically disallows PostgreSQL `superuser` permissions\. Aurora PostgreSQL is a managed service, so you can't access the host OS, and you can't connect using the PostgreSQL `superuser` account\. Many of the tasks that require `superuser` access on a stand\-alone PostgreSQL are managed automatically by Aurora\. 

For more information about granting privileges, see [GRANT](http://www.postgresql.org/docs/current/sql-grant.html) in the PostgreSQL documentation\.

The `rds_superuser` role is one of several *predefined* roles in an Aurora PostgreSQL DB cluster\. 

**Note**  
In PostgreSQL 13 and earlier releases, *predefined* roles are known as *default* roles\.

In the following list, you find some of the other predefined roles that are created automatically for a new Aurora PostgreSQL DB cluster\. Predefined roles and their privileges can't be changed\. You can't drop, rename, or modify privileges for these predefined roles\. Attempting to do so results in an error\. 
+ **rds\_password** – A role that can change passwords and set up password constraints for databaes users\. The `rds_superuser` role is granted this role by default, and can grant the role to database users\. `For more information, see [Controlling user access to the PostgreSQL databaseControlling user access to PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Access)\. 
+ **rdsadmin** – A role that's created to handle many of the management tasks that the adminstrator with `superuser` privileges would perform on a standalone PostgreSQL database\. This role is used internally by Aurora PostgreSQL for many management tasks\. 

To see all predefined roles, you can connect to the primary instance of your Aurora PostgreSQL DB cluster and use the `psq1 \du` metacommand\. The output looks as follows: 

```
List of roles
  Role name   |      Attributes                   |      Member of      
--------------+-----------------------------------+------------------------------------
postgres      | Create role, Create DB           +| {rds_superuser}
              | Password valid until infinity     |
rds_superuser | Cannot login                      | {pg_monitor,pg_signal_backend,
              |                                  +|   rds_replication,rds_password}
...
```

In the output, you can see that `rds_superuser` isn't a database user role \(it can't login\), but it has the privileges of many other roles\. You can also see that database user `postgres` is a member of the `rds_superuser` role\. As mentioned previously, `postgres` is the default value in the Amazon RDS console's **Create database** page\. If you chose another name, that name is shown in the list of roles instead\. 

### Controlling user access to the PostgreSQL database<a name="Appendix.PostgreSQL.CommonDBATasks.Access"></a>

New databases in PostgreSQL are always created with a default set of privileges in the database's `public` schema that allow all database users and roles to create objects\. These privileges allow database users to connect to the database, for example, and create temporary tables while connected\.

To better control user access to the databases instances that you create on your Aurora PostgreSQL DB cluster primary node , we recommend that you revoke these default `public` privileges\. After doing so, you then grant specific privileges for database users on a more granular basis, as shown in the following procedure\. 

**To set up roles and privileges for a new database instance**

Suppose you're setting up a database on a newly created Aurora PostgreSQL DB cluster for use by several researchers, all of whom need read\-write access to the database\. 

1. Use `psql` \(or pgAdmin\) to connect to the primary DB instance on your Aurora PostgreSQL DB cluster: 

   ```
   psql --host=your-cluster-instance-1.666666666666.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

   When prompted, enter your password\. The `psql` client connects and displays the default administrative connection database, `postgres=>`, as the prompt\.

1. To prevent database users from creating objects in the `public` schema, do the following:

   ```
   postgres=> REVOKE CREATE ON SCHEMA public FROM PUBLIC;
   REVOKE
   ```

1. Next, you create a new database instance:

   ```
   postgres=> CREATE DATABASE lab_db;
   CREATE DATABASE
   ```

1. Revoke all privileges from the `PUBLIC` schema on this new database\.

   ```
   postgres=> REVOKE ALL ON DATABASE lab_db FROM public;
   REVOKE
   ```

1. Create a role for database users\.

   ```
   postgres=> CREATE ROLE lab_tech;
   CREATE ROLE
   ```

1. Give database users that have this role the ability to connect to the database\.

   ```
   postgres=> GRANT CONNECT ON DATABASE lab_db TO lab_tech;
   GRANT
   ```

1. Grant all users with the `lab_tech` role all privileges on this database\.

   ```
   postgres=> GRANT ALL PRIVILEGES ON DATABASE lab_db TO lab_tech;
   GRANT
   ```

1. Create database users, as follows:

   ```
   postgres=> CREATE ROLE lab_user1 LOGIN PASSWORD 'change_me';
   CREATE ROLE
   postgres=> CREATE ROLE lab_user2 LOGIN PASSWORD 'change_me';
   CREATE ROLE
   ```

1. Grant these two users the privileges associated with the lab\_tech role:

   ```
   postgres=> GRANT lab_tech TO lab_user1;
   GRANT ROLE
   postgres=> GRANT lab_tech TO lab_user2;
   GRANT ROLE
   ```

At this point, `lab_user1` and `lab_user2` can connect to the `lab_db` database\. This example doesn't follow best practices for enterprise usage, which might include creating multiple database instances, different schemas, and granting limited permissions\. For more complete information and additional scenarios, see [Managing PostgreSQL Users and Roles](http://aws.amazon.com/blogs/database/managing-postgresql-users-and-roles/)\. 

For more information about privileges in PostgreSQL databases, see the [GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) command in the PostgreSQL documentation\.

### Delegating and controlling user password management<a name="Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt"></a>

As a DBA, you might want to delegate the management of user passwords\. Or, you might want to prevent database users from changing their passwords or reconfiguring password constraints, such as password lifetime\. To ensure that only the database users that you choose can change password settings, you can turn on the restricted password management feature\. When you activate this feature, only those database users that have been granted the `rds_password` role can manage passwords\. 

**Note**  
To use restricted password management, your Aurora PostgreSQL DB cluster must be running Amazon Aurora PostgreSQL 10\.6 or higher\.

By default, this feature is `off`, as shown in the following:

```
postgres=> SHOW rds.restrict_password_commands;
  rds.restrict_password_commands
--------------------------------
 off
(1 row)
```

To turn on this feature, you use a custom parameter group and change the setting for `rds.restrict_password_commands` to 1\. Be sure to reboot your Aurora PostgreSQL's primary DB instance so that the setting takes effect\. 

With this feature active, `rds_password` privileges are needed for the following SQL commands:

```
CREATE ROLE myrole WITH PASSWORD 'mypassword';
CREATE ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2023-01-01';
ALTER ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2023-01-01';
ALTER ROLE myrole WITH PASSWORD 'mypassword';
ALTER ROLE myrole VALID UNTIL '2023-01-01';
ALTER ROLE myrole RENAME TO myrole2;
```

Renaming a role \(`ALTER ROLE myrole RENAME TO newname`\) is also restricted if the password uses the MD5 hashing algorithm\. 

With this feature active, attempting any of these SQL commands without the `rds_password` role permissions generates the following error: 

```
ERROR: must be a member of rds_password to alter passwords
```

We recommend that you grant the `rds_password` to only a few roles that you use solely for password management\. If you grant `rds_password` privileges to database users that don't have `rds_superuser` privileges, you need to also grant them the `CREATEROLE` attribute\.

Make sure that you verify password requirements such as expiration and needed complexity on the client side\. If you use your own client\-side utility for password related changes, the utility needs to be a member of `rds_password` and have `CREATE ROLE` privileges\. 

## Securing Aurora PostgreSQL data with SSL/TLS<a name="AuroraPostgreSQL.Security.SSL"></a>

Amazon RDS supports Secure Socket Layer \(SSL\) and Transport Layer Security \(TLS\) encryption for Aurora PostgreSQL DB clusters\. Using SSL/TLS, you can encrypt a connection between your applications and your Aurora PostgreSQL DB clusters\. You can also force all connections to your Aurora PostgreSQL DB cluster to use SSL/TLS\. Amazon Aurora PostgreSQL supports Transport Layer Security \(TLS\) versions 1\.1 and 1\.2\. We recommend using TLS 1\.2 for encrypted connections\.

For general information about SSL/TLS support and PostgreSQL databases, see [SSL support](https://www.postgresql.org/docs/current/libpq-ssl.html) in the PostgreSQL documentation\. For information about using an SSL/TLS connection over JDBC, see [Configuring the client](https://jdbc.postgresql.org/documentation/head/ssl-client.html) in the PostgreSQL documentation\.

**Topics**
+ [Requiring an SSL/TLS connection to an Aurora PostgreSQL DB cluster](#AuroraPostgreSQL.Security.SSL.Requiring)
+ [Determining the SSL/TLS connection status](#AuroraPostgreSQL.Security.SSL.Status)
+ [Configuring cipher suites for connections to Aurora PostgreSQL DB clusters](#AuroraPostgreSQL.Security.SSL.ConfiguringCipherSuites)

SSL/TLS support is available in all AWS Regions for Aurora PostgreSQL\. Amazon RDS creates an SSL/TLS certificate for your Aurora PostgreSQL DB cluster when the DB cluster is created\. If you enable SSL/TLS certificate verification, then the SSL/TLS certificate includes the DB cluster endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. 

**To connect to an Aurora PostgreSQL DB cluster over SSL/TLS**

1. Download the certificate\.

   For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.

1. Import the certificate into your operating system\.

1. Connect to your Aurora PostgreSQL DB cluster over SSL/TLS\.

   When you connect using SSL/TLS, your client can choose to verify the certificate chain or not\. If your connection parameters specify `sslmode=verify-ca` or `sslmode=verify-full`, then your client requires the RDS CA certificates to be in their trust store or referenced in the connection URL\. This requirement is to verify the certificate chain that signs your database certificate\.

   When a client, such as psql or JDBC, is configured with SSL/TLS support, the client first tries to connect to the database with SSL/TLS by default\. If the client can't connect with SSL/TLS, it reverts to connecting without SSL/TLS\. The default `sslmode` mode used is different between libpq\-based clients \(such as psql\) and JDBC\. The libpq\-based clients default to `prefer`, where JDBC clients default to `verify-full`\.

   Use the `sslrootcert` parameter to reference the certificate, for example `sslrootcert=rds-ssl-ca-cert.pem`\.

The following is an example of using psql to connect to an Aurora PostgreSQL DB cluster\.

```
$ psql -h testpg.cdhmuqifdpib.us-east-1.rds.amazonaws.com -p 5432 \
    "dbname=testpg user=testuser sslrootcert=rds-ca-2015-root.pem sslmode=verify-full"
```

### Requiring an SSL/TLS connection to an Aurora PostgreSQL DB cluster<a name="AuroraPostgreSQL.Security.SSL.Requiring"></a>

You can require that connections to your Aurora PostgreSQL DB cluster use SSL/TLS by using the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to 0 \(off\)\. You can set the `rds.force_ssl` parameter to 1 \(on\) to require SSL/TLS for connections to your DB cluster\. Updating the `rds.force_ssl` parameter also sets the PostgreSQL `ssl` parameter to 1 \(on\) and modifies your DB cluster's `pg_hba.conf` file to support the new SSL/TLS configuration\.

You can set the `rds.force_ssl` parameter value by updating the DB cluster parameter group for your DB cluster\. If the DB cluster parameter group isn't the default one, and the `ssl` parameter is already set to 1 when you set `rds.force_ssl` to 1, you don't need to reboot your DB cluster\. Otherwise, you must reboot your DB cluster for the change to take effect\. For more information on parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

When the `rds.force_ssl` parameter is set to 1 for a DB cluster, you see output similar to the following when you connect, indicating that SSL/TLS is now required:

```
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
psql (9.3.12, server 9.4.4)
WARNING: psql major version 9.3, server major version 9.4.
Some psql features might not work.
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

postgres=>
```

### Determining the SSL/TLS connection status<a name="AuroraPostgreSQL.Security.SSL.Status"></a>

The encrypted status of your connection is shown in the logon banner when you connect to the DB cluster\.

```
Password for user master: 
psql (9.3.12) 
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256) 
Type "help" for help.   

postgres=>
```

You can also load the `sslinfo` extension and then call the `ssl_is_used()` function to determine if SSL/TLS is being used\. The function returns `t` if the connection is using SSL/TLS, otherwise it returns `f`\.

```
postgres=> create extension sslinfo;
CREATE EXTENSION

postgres=> select ssl_is_used();
 ssl_is_used
---------
t
(1 row)
```

You can use the `select ssl_cipher()` command to determine the SSL/TLS cipher:

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

For information about the `sslmode` option, see [Database connection control functions](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLMODE) in the PostgreSQL documentation\.

### Configuring cipher suites for connections to Aurora PostgreSQL DB clusters<a name="AuroraPostgreSQL.Security.SSL.ConfiguringCipherSuites"></a>

By using configurable cipher suites, you can have more control over the security of your database connections\. You can specify a list of cipher suites that you want to allow to secure client SSL/TLS connections to your database\. With configurable cipher suites, you can control the connection encryption that your database server accepts\. Doing this helps prevent the use of insecure or deprecated ciphers\.

Configurable cipher suites is supported in Aurora PostgreSQL versions 11\.8 and higher\.

To specify the list of permissible ciphers for encrypting connections, modify the `ssl_ciphers` cluster parameter\. Set the `ssl_ciphers` parameter in a cluster parameter group using the AWS Management Console, the AWS CLI, or the RDS API\. To set cluster parameters, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

Set the `ssl_ciphers` parameter to a string of comma\-separated cipher values\. The valid ciphers include the following:
+ `DHE-RSA-AES128-SHA`
+ `DHE-RSA-AES128-SHA256`
+ `DHE-RSA-AES128-GCM-SHA256`
+ `DHE-RSA-AES256-SHA`
+ `DHE-RSA-AES256-SHA256`
+ `DHE-RSA-AES256-GCM-SHA384`
+ `ECDHE-RSA-AES128-SHA`
+ `ECDHE-RSA-AES128-SHA256`
+ `ECDHE-RSA-AES128-GCM-SHA256`
+ `ECDHE-RSA-AES256-SHA`
+ `ECDHE-RSA-AES256-SHA384`
+ `ECDHE-RSA-AES256-GCM-SHA384`

You can also use the [describe\-engine\-default\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-cluster-parameters.html) CLI command to determine which cipher suites are currently supported for a specific parameter group family\. The following example shows how to get the allowed values for the `ssl_cipher` cluster parameter for Aurora PostgreSQL 11\.

```
aws rds describe-engine-default-cluster-parameters --db-parameter-group-family aurora-postgresql11
                
    ...some output truncated...
	{
		"ParameterName": "ssl_ciphers",
		"Description": "Sets the list of allowed TLS ciphers to be used on secure connections.",
		"Source": "engine-default",
		"ApplyType": "dynamic",
		"DataType": "list",
		"AllowedValues": "ECDHE-RSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-SHA384,AES256-SHA,AES128-SHA,DES-CBC3-SHA,ADH-DES-CBC3-SHA,EDH-RSA-DES-CBC3-SHA,EDH-DSS-DES-CBC3-SHA,ADH-AES256-SHA,DHE-RSA-AES256-SHA,DHE-DSS-AES256-SHA,ADH-AES128-SHA,DHE-RSA-AES128-SHA,DHE-DSS-AES128-SHA,HIGH",
		"IsModifiable": true,
		"MinimumEngineVersion": "11.8",
		"SupportedEngineModes": [
			"provisioned"
		]
	},
    ...some output truncated...
```

The `ssl_ciphers` parameter has no default string of cipher suites\. For more information about ciphers, see the [ssl\_ciphers](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-SSL-CIPHERS) variable in the PostgreSQL documentation\. For more information about cipher suite formats, see the [openssl\-ciphers list format](https://www.openssl.org/docs/manmaster/man1/openssl-ciphers.html#CIPHER-LIST-FORMAT) and [openssl\-ciphers string format](https://www.openssl.org/docs/manmaster/man1/openssl-ciphers.html#CIPHER-STRINGS) documentation on the OpenSSL website\.