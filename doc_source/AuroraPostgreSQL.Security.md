# Security with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Security"></a>

For a general overview of Aurora security, see [Security in Amazon Aurora](UsingWithRDS.md)\. You can manage security for Amazon Aurora PostgreSQL at a few different levels:
+ To control who can perform Amazon RDS management actions on Aurora PostgreSQL DB clusters and DB instances, use AWS Identity and Access Management \(IAM\)\. IAM handles the authentication of user identity before the user can access the service\. It also handles authorization, that is, whether the user is allowed to do what they're trying to do\. IAM database authentication is an additional authentication method that you can choose when you create your Aurora PostgreSQL DB cluster\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.

  If you do use IAM with your Aurora PostgreSQL DB cluster, sign in to the AWS Management Console with your IAM credentials first, before opening the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ Make sure to create Aurora DB clusters in a virtual private cloud \(VPC\) based on the Amazon VPC service\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora DB clusters in a VPC, use a VPC security group\. You can make these endpoint and port connections by using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon VPC VPCs and Amazon Aurora](USER_VPC.md)\.

  The supported VPC tenancy depends on the DB instance class used by your Aurora PostgreSQL DB clusters\. With `default` VPC tenancy, the DB cluster runs on shared hardware\. With `dedicated` VPC tenancy, the DB cluster runs on a dedicated hardware instance\. The burstable performance DB instance classes support default VPC tenancy only\. The burstable performance DB instance classes include the db\.t3 and db\.t4g DB instance classes\. All other Aurora PostgreSQL DB instance classes support both default and dedicated VPC tenancy\.

  For more information about instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. For more information about `default` and `dedicated` VPC tenancy, see [Dedicated instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\.
+ To grant permissions to the PostgreSQL databases running on your Amazon Aurora DB cluster, you can take the same general approach as with stand\-alone instances of PostgreSQL\. Commands such as `CREATE ROLE`, `ALTER ROLE`, `GRANT`, and `REVOKE` work just as they do in on\-premises databases, as does directly modifying databases, schemas, and tables\.

  PostgreSQL manages privileges by using *roles*\. The `rds_superuser` role is the most privileged role on an Aurora PostgreSQL DB cluster\. This role is created automatically, and it's granted to the user that creates the DB cluster \(the master user account, `postgres` by default\)\. To learn more, see [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)\. 

Aurora PostgreSQL 14\.3 and higher releases support the Salted Challenge Response Authentication Mechanism \(SCRAM\) for passwords as an alternative to message digest \(MD5\)\. We recommend that you use SCRAM because it's more secure than MD5\. For more information, including how to migrate database user passwords from MD5 to SCRAM, see [Using SCRAM for PostgreSQL password encryption](Appendix.PostgreSQL.CommonDBATasks.Roles.md#PostgreSQL_Password_Encryption_configuration)\.

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
+ `ECDHE-RSA-AES128-SHA`
+ `ECDHE-RSA-AES128-SHA256`
+ `ECDHE-RSA-AES128-GCM-SHA256`
+ `ECDHE-RSA-AES256-SHA`
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