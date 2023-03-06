# Security with Amazon Aurora MySQL<a name="AuroraMySQL.Security"></a>

Security for Amazon Aurora MySQL is managed at three levels:
+ To control who can perform Amazon RDS management actions on Aurora MySQL DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your AWS account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.

  If you are using IAM to access the Amazon RDS console, make sure to first sign in to the AWS Management Console with your IAM user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ Make sure to create Aurora MySQL DB clusters in a virtual public cloud \(VPC\) based on the Amazon VPC service\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora MySQL DB clusters in a VPC, use a VPC security group\. You can make these endpoint and port connections by using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon VPC VPCs and Amazon Aurora](USER_VPC.md)\.

  The supported VPC tenancy depends on the DB instance class used by your Aurora MySQL DB clusters\. With `default` VPC tenancy, the VPC runs on shared hardware\. With `dedicated` VPC tenancy, the VPC runs on a dedicated hardware instance\. The burstable performance DB instance classes support default VPC tenancy only\. The burstable performance DB instance classes include the db\.t2, db\.t3, and db\.t4g DB instance classes\. All other Aurora MySQL DB instance classes support both default and dedicated VPC tenancy\.
**Note**  
We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more details on the T instance classes, see [Using T instance classes for development and testing](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.T2Medium)\.

  For more information about instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. For more information about `default` and `dedicated` VPC tenancy, see [Dedicated instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\.
+ To authenticate login and permissions for an Amazon Aurora MySQL DB cluster, you can take either of the following approaches, or a combination of them:
  + You can take the same approach as with a standalone instance of MySQL\.

    Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For more information, see [ Access control and account management](https://dev.mysql.com/doc/refman/8.0/en/access-control.html) in the MySQL documentation\.
  + You can also use IAM database authentication\.

    With IAM database authentication, you authenticate to your DB cluster by using an IAM user or IAM role and an authentication token\. An *authentication token* is a unique value that is generated using the Signature Version 4 signing process\. By using IAM database authentication, you can use the same credentials to control access to your AWS resources and your databases\. For more information, see [IAM database authentication](UsingWithRDS.IAMDBAuth.md)\.

**Note**  
For more information, see [Security in Amazon Aurora](UsingWithRDS.md)\.

## Master user privileges with Amazon Aurora MySQL<a name="AuroraMySQL.Security.MasterUser"></a>

When you create an Amazon Aurora MySQL DB instance, the master user has the default privileges listed in [Master user account privileges](UsingWithRDS.MasterAccounts.md)\.

To provide management services for each DB cluster, the `admin` and `rdsadmin` users are created when the DB cluster is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account results in an error\.

In Aurora MySQL version 2 DB clusters, the `admin` and `rdsadmin` users are created when the DB cluster is created\. In Aurora MySQL version 3 DB clusters, the `admin`, `rdsadmin`, and `rds_superuser_role` users are created\.

**Important**  
We strongly recommend that you do not use the master user directly in your applications\. Instead, adhere to the best practice of using a database user created with the minimal privileges required for your application\.

For management of the Aurora MySQL DB cluster, the standard `kill` and `kill_query` commands have been restricted\. Instead, use the Amazon RDS commands `rds_kill` and `rds_kill_query` to terminate user sessions or queries on Aurora MySQL DB instances\. 

**Note**  
Encryption of a database instance and snapshots is not supported for the China \(Ningxia\) region\.

## Using SSL/TLS with Aurora MySQL DB clusters<a name="AuroraMySQL.Security.SSL"></a>

Amazon Aurora MySQL DB clusters support Secure Sockets Layer \(SSL\) and Transport Layer Security \(TLS\) connections from applications using the same process and public key as RDS for MySQL DB instances\.

Amazon RDS creates an SSL/TLS certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. As a result, you can only use the DB cluster endpoint to connect to a DB cluster using SSL/TLS if your client supports Subject Alternative Names \(SAN\)\. Otherwise, you must use the instance endpoint of a writer instance\. 

For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.

We recommend the AWS JDBC Driver for MySQL as a client that supports SAN with SSL/TLS\. For more information about the AWS JDBC Driver for MySQL and complete instructions for using it, see the [AWS JDBC Driver for MySQL GitHub repository](https://awslabs.github.io/aws-mysql-jdbc/)\.

**Topics**
+ [Requiring an SSL/TLS connection to an Aurora MySQL DB cluster](#AuroraMySQL.Security.SSL.RequireSSL)
+ [TLS versions for Aurora MySQL](#AuroraMySQL.Security.SSL.TLS_Version)
+ [Encrypting connections to an Aurora MySQL DB cluster](#AuroraMySQL.Security.SSL.EncryptingConnections)
+ [Configuring cipher suites for connections to Aurora MySQL DB clusters](#AuroraMySQL.Security.SSL.ConfiguringCipherSuites)

### Requiring an SSL/TLS connection to an Aurora MySQL DB cluster<a name="AuroraMySQL.Security.SSL.RequireSSL"></a>

You can require that all user connections to your Aurora MySQL DB cluster use SSL/TLS by using the `require_secure_transport` DB cluster parameter\. By default, the `require_secure_transport` parameter is set to `OFF`\. You can set the `require_secure_transport` parameter to `ON` to require SSL/TLS for connections to your DB cluster\.

You can set the `require_secure_transport` parameter value by updating the DB cluster parameter group for your DB cluster\. You don't need to reboot your DB cluster for the change to take effect\. For more information on parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

**Note**  
The `require_secure_transport` parameter is available for Aurora MySQL version 2 and 3\. You can set this parameter in a custom DB cluster parameter group\. The parameter isn't available in DB instance parameter groups\.

When the `require_secure_transport` parameter is set to `ON` for a DB cluster, a database client can connect to it if it can establish an encrypted connection\. Otherwise, an error message similar to the following is returned to the client:

```
MySQL Error 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

### TLS versions for Aurora MySQL<a name="AuroraMySQL.Security.SSL.TLS_Version"></a>

Aurora MySQL supports Transport Layer Security \(TLS\) versions 1\.0, 1\.1, and 1\.2\. The following table shows the TLS support for Aurora MySQL versions\. 


****  

| Aurora MySQL version | TLS 1\.0 | TLS 1\.1 | TLS 1\.2 | 
| --- | --- | --- | --- | 
|  Aurora MySQL version 3  |  Supported  |  Supported  |  Supported  | 
|  Aurora MySQL version 2  |  Supported  |  Supported  |  Supported  | 

 Although the community edition of MySQL 8\.0 supports TLS 1\.3, the MySQL 8\.0\-compatible Aurora MySQL version 3 currently doesn't support TLS 1\.3\. 

For an Aurora MySQL version 2 DB cluster, you can use the `tls_version` DB cluster parameter to indicate the permitted protocol versions\. Similar client parameters exist for most client tools or database drivers\. Some older clients might not support newer TLS versions\. By default, the DB cluster attempts to use the highest TLS protocol version allowed by both the server and client configuration\.

Set the `tls_version` DB cluster parameter to one of the following values:
+ `TLSv1.2` – Only the TLS version 1\.2 protocol is permitted for encrypted connections\.
+ `TLSv1.1` – TLS version 1\.1 and 1\.2 protocols are permitted for encrypted connections\.
+ `TLSv1` – TLS version 1\.0, 1\.1, and 1\.2 protocols are permitted for encrypted connections\.

If the parameter isn't set, then TLS version 1\.0, 1\.1, and 1\.2 protocols are permitted for encrypted connections\.

For information about modifying parameters in a DB cluster parameter group, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\. If you use the AWS CLI to modify the `tls_version` DB cluster parameter, the `ApplyMethod` must be set to `pending-reboot`\. When the application method is `pending-reboot`, changes to parameters are applied after you stop and restart the DB clusters associated with the parameter group\.

### Encrypting connections to an Aurora MySQL DB cluster<a name="AuroraMySQL.Security.SSL.EncryptingConnections"></a>

To encrypt connections using the default `mysql` client, launch the mysql client using the `--ssl-ca` parameter to reference the public key, for example: 

For MySQL 5\.7 and 8\.0:

```
mysql -h myinstance.123456789012.rds-us-east-1.amazonaws.com
--ssl-ca=full_path_to_CA_certificate --ssl-mode=VERIFY_IDENTITY
```

For MySQL 5\.6:

```
mysql -h myinstance.123456789012.rds-us-east-1.amazonaws.com
--ssl-ca=full_path_to_CA_certificate --ssl-verify-server-cert
```

Replace *full\_path\_to\_CA\_certificate* with the full path to your Certificate Authority \(CA\) certificate\. For information about downloading a certificate, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.

You can require SSL/TLS connections for specific users accounts\. For example, you can use one of the following statements, depending on your MySQL version, to require SSL/TLS connections on the user account `encrypted_user`\.

For MySQL 5\.7 and 8\.0:

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For MySQL 5\.6:

```
GRANT USAGE ON *.* TO 'encrypted_user'@'%' REQUIRE SSL;            
```

 When you use an RDS proxy, you connect to the proxy endpoint instead of the usual cluster endpoint\. You can make SSL/TLS required or optional for connections to the proxy, in the same way as for connections directly to the Aurora DB cluster\. For information about using the RDS Proxy, see [Using Amazon RDS Proxy for Aurora](rds-proxy.md)\. 

**Note**  
For more information on SSL/TLS connections with MySQL, see the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)\.

### Configuring cipher suites for connections to Aurora MySQL DB clusters<a name="AuroraMySQL.Security.SSL.ConfiguringCipherSuites"></a>

By using configurable cipher suites, you can have more control over the security of your database connections\. You can specify a list of cipher suites that you want to allow to secure client SSL/TLS connections to your database\. With configurable cipher suites, you can control the connection encryption that your database server accepts\. Doing this prevents the use of insecure or deprecated ciphers\.

Configurable cipher suites is supported in Aurora MySQL version 3 and Aurora MySQL version 2\.

To specify the list of permissible ciphers for encrypting connections, modify the `ssl_cipher` cluster parameter\. Set the `ssl_cipher` parameter in a cluster parameter group using the AWS Management Console, the AWS CLI, or the RDS API\.

For information about modifying parameters in a DB cluster parameter group, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\. If you use the CLI to modify the `ssl_cipher` DB cluster parameter, make sure to set the `ApplyMethod` to `pending-reboot`\. When the application method is `pending-reboot`, changes to parameters are applied after you stop and restart the DB clusters associated with the parameter group\.

For the client application, you can specify the ciphers to use for encrypted connections by using the `--ssl-cipher` option when connecting to the database\. For more about connecting to your database, see [Connecting to an Amazon Aurora MySQL DB cluster](Aurora.Connecting.md#Aurora.Connecting.AuroraMySQL)\.

Set the `ssl_cipher` parameter to a string of comma\-separated cipher values\. The following table shows the supported ciphers along with the TLS encryption protocol and valid Aurora MySQL engine versions for each cipher\.


| Cipher | Encryption protocol | Supported Aurora MySQL versions | 
| --- | --- | --- | 
| `DHE-RSA-AES128-SHA` | TLS 1\.0 | 3\.01\.0 and higher, all below 2\.11\.0 | 
| `DHE-RSA-AES128-SHA256` | TLS 1\.2 | 3\.01\.0 and higher, all below 2\.11\.0 | 
| `DHE-RSA-AES128-GCM-SHA256` | TLS 1\.2 | 3\.01\.0 and higher, all below 2\.11\.0 | 
| `DHE-RSA-AES256-SHA` | TLS 1\.0 | 3\.01\.0 and higher, all below 2\.11\.0 | 
| `DHE-RSA-AES256-SHA256` | TLS 1\.2 | 3\.01\.0 and higher, all below 2\.11\.0 | 
| `DHE-RSA-AES256-GCM-SHA384` | TLS 1\.2 | 3\.01\.0 and higher, all below 2\.11\.0 | 
| `ECDHE-RSA-AES128-SHA` | TLS 1\.0 | 3\.01\.0 and higher, 2\.09\.3 and higher, 2\.10\.2 and higher | 
| `ECDHE-RSA-AES128-SHA256` | TLS 1\.2 | 3\.01\.0 and higher, 2\.09\.3 and higher, 2\.10\.2 and higher | 
| `ECDHE-RSA-AES128-GCM-SHA256` | TLS 1\.2 | 3\.01\.0 and higher, 2\.09\.3 and higher, 2\.10\.2 and higher | 
| `ECDHE-RSA-AES256-SHA` | TLS 1\.0 | 3\.01\.0 and higher, 2\.09\.3 and higher, 2\.10\.2 and higher | 
| `ECDHE-RSA-AES256-SHA384` | TLS 1\.2 | 3\.01\.0 and higher, 2\.09\.3 and higher, 2\.10\.2 and higher | 
| `ECDHE-RSA-AES256-GCM-SHA384` | TLS 1\.2 | 3\.01\.0 and higher, 2\.09\.3 and higher, 2\.10\.2 and higher | 

**Note**  
`DHE-RSA` ciphers are only supported by Aurora MySQL versions before 2\.11\.0\. Versions 2\.11\.0 and higher support only `ECDHE` ciphers\.

You can also use the [describe\-engine\-default\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-cluster-parameters.html) CLI command to determine which cipher suites are currently supported for a specific parameter group family\. The following example shows how to get the allowed values for the `ssl_cipher` cluster parameter for Aurora MySQL version 2\.

```
aws rds describe-engine-default-cluster-parameters --db-parameter-group-family aurora-mysql5.7

        ...some output truncated...
	{
		"ParameterName": "ssl_cipher",
		"ParameterValue": "DHE-RSA-AES128-SHA,DHE-RSA-AES128-SHA256,DHE-RSA-AES128-GCM-SHA256,DHE-RSA-AES256-SHA,DHE-RSA-AES256-SHA256,DHE-RSA-AES256-GCM-SHA384,ECDHE-RSA-AES128-SHA,ECDHE-RSA-AES128-SHA256,ECDHE-RSA-AES128-GCM-SHA256,ECDHE-RSA-AES256-SHA,ECDHE-RSA-AES256-SHA384,ECDHE-RSA-AES256-GCM-SHA384",
		"Description": "The list of permissible ciphers for connection encryption.",
		"Source": "system",
		"ApplyType": "static",
		"DataType": "list",
		"AllowedValues": "DHE-RSA-AES128-SHA,DHE-RSA-AES128-SHA256,DHE-RSA-AES128-GCM-SHA256,DHE-RSA-AES256-SHA,DHE-RSA-AES256-SHA256,DHE-RSA-AES256-GCM-SHA384,ECDHE-RSA-AES128-SHA,ECDHE-RSA-AES128-SHA256,ECDHE-RSA-AES128-GCM-SHA256,ECDHE-RSA-AES256-SHA,ECDHE-RSA-AES256-SHA384,ECDHE-RSA-AES256-GCM-SHA384",
		"IsModifiable": true,
		"SupportedEngineModes": [
			"provisioned"
		]
	},
       ...some output truncated...
```

For more information about ciphers, see the [ssl\_cipher](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_ssl_cipher) variable in the MySQL documentation\. For more information about cipher suite formats, see the [openssl\-ciphers list format](https://www.openssl.org/docs/manmaster/man1/openssl-ciphers.html#CIPHER-LIST-FORMAT) and [openssl\-ciphers string format](https://www.openssl.org/docs/manmaster/man1/openssl-ciphers.html#CIPHER-STRINGS) documentation on the OpenSSL website\.