# Security with Amazon Aurora MySQL<a name="AuroraMySQL.Security"></a>

Security for Amazon Aurora MySQL is managed at three levels:
+ To control who can perform Amazon RDS management actions on Aurora MySQL DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your AWS account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Identity and Access Management in Amazon Aurora](UsingWithRDS.IAM.md)\.

  If you are using IAM to access the Amazon RDS console, you must first sign on to the AWS Management Console with your IAM user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ Aurora MySQL DB clusters must be created in an Amazon Virtual Private Cloud \(VPC\)\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora MySQL DB clusters in a VPC, you use a VPC security group\. These endpoint and port connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.

  The supported VPC tenancy depends on the instance class used by your Aurora MySQL DB clusters\. With `default` VPC tenancy, the VPC runs on shared hardware\. With `dedicated` VPC tenancy, the VPC runs on a dedicated hardware instance\. Aurora MySQL supports the following VPC tenancy based on the instance class:
  + The db\.r3 instance classes support both `default` and `dedicated` VPC tenancy\.
  + The db\.r4 instance classes support `default` and `dedicated` VPC tenancy only\.
  + The db\.t2 instance classes support `default` VPC tenancy only\.

  For more information about instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. For more information about `default` and `dedicated` VPC tenancy, see [Dedicated Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\.
+ To authenticate login and permissions for an Amazon Aurora MySQL DB cluster, you can take either of the following approaches, or a combination of them:
  + You can take the same approach as with a standalone instance of MySQL\.

    Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For more information, see [ Access Control and Account Management](https://dev.mysql.com/doc/refman/5.6/en/access-control.html) in the MySQL documentation\.
  + You can also use IAM database authentication\.

    With IAM database authentication, you authenticate to your DB cluster by using an IAM user or IAM role and an authentication token\. An *authentication token* is a unique value that is generated using the Signature Version 4 signing process\. By using IAM database authentication, you can use the same credentials to control access to your AWS resources and your databases\. For more information, see [IAM Database Authentication](UsingWithRDS.IAMDBAuth.md)\.

## Master User Privileges with Amazon Aurora MySQL<a name="AuroraMySQL.Security.MasterUser"></a>

When you create an Amazon Aurora MySQL DB instance, the master user has the following default privileges:
+  `ALTER` 
+  `ALTER ROUTINE` 
+  `CREATE` 
+  `CREATE ROUTINE` 
+  `CREATE TEMPORARY TABLES` 
+  `CREATE USER` 
+  `CREATE VIEW` 
+  `DELETE` 
+  `DROP` 
+  `EVENT` 
+  `EXECUTE` 
+  `GRANT OPTION` 
+  `INDEX` 
+  `INSERT` 
+  `LOAD FROM S3` 
+  `LOCK TABLES` 
+  `PROCESS` 
+  `REFERENCES` 
+  `RELOAD` 
+  `REPLICATION CLIENT` 
+  `REPLICATION SLAVE` 
+  `SELECT` 
+  `SHOW DATABASES` 
+  `SHOW VIEW` 
+  `TRIGGER` 
+  `UPDATE` 

To provide management services for each DB cluster, the `rdsadmin` user is created when the DB cluster is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account results in an error\.

For management of the Aurora MySQL DB cluster, the standard `kill` and `kill_query` commands have been restricted\. Instead, use the Amazon RDS commands `rds_kill` and `rds_kill_query` to terminate user sessions or queries on Aurora MySQL DB instances\. 

**Note**  
Encryption of a database instance and snapshots is not supported for the China \(Ningxia\) region\.

## Using SSL/TLS with Aurora MySQL DB Clusters<a name="AuroraMySQL.Security.SSL"></a>

Amazon Aurora MySQL DB clusters support Secure Sockets Layer \(SSL\) and Transport Layer Security \(TLS\) connections from applications using the same process and public key as Amazon RDS MySQL DB instances\.

Amazon RDS creates an SSL/TLS certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. As a result, you can only use the DB cluster endpoint to connect to a DB cluster using SSL/TLS if your client supports Subject Alternative Names \(SAN\)\. Otherwise, you must use the instance endpoint of a writer instance\. 

For information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Cluster](UsingWithRDS.SSL.md)\.

We recommend the MariaDB Connector/J client as a client that supports SAN with SSL\. For more information, see the [MariaDB Connector/J download](https://downloads.mariadb.org/connector-java/) page\.

**Topics**
+ [Requiring an SSL/TLS Connection to an Aurora MySQL DB Cluster](#AuroraMySQL.Security.SSL.RequireSSL)
+ [TLS Versions for Aurora MySQL](#AuroraMySQL.Security.SSL.TLS_Version)
+ [Encrypting Connections to an Aurora MySQL DB Cluster](#AuroraMySQL.Security.SSL.EncryptingConnections)

### Requiring an SSL/TLS Connection to an Aurora MySQL DB Cluster<a name="AuroraMySQL.Security.SSL.RequireSSL"></a>

You can require that all user connections to your Aurora MySQL DB cluster use SSL/TLS by using the `require_secure_transport` DB cluster parameter\. By default, the `require_secure_transport` parameter is set to `OFF`\. You can set the `require_secure_transport` parameter to `ON` to require SSL/TLS for connections to your DB cluster\.

You can set the `require_secure_transport` parameter value by updating the DB cluster parameter group for your DB cluster\. You don't need to reboot your DB cluster for the change to take effect\. For more information on parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

**Note**  
The `require_secure_transport` parameter is only available for Aurora MySQL version 5\.7\.

When the `require_secure_transport` parameter is set to `ON` for a DB cluster, a database client can connect to it if it can establish an encrypted connection\. Otherwise, an error message similar to the following is returned to the client:

```
MySQL Error 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

### TLS Versions for Aurora MySQL<a name="AuroraMySQL.Security.SSL.TLS_Version"></a>

Aurora MySQL supports Transport Layer Security \(TLS\) versions 1\.0, 1\.1, and 1\.2\. The following table shows the TLS support for Aurora MySQL versions\. 


****  

| Aurora MySQL Version | TLS 1\.0 | TLS 1\.1 | TLS 1\.2 | 
| --- | --- | --- | --- | 
|  Aurora MySQL 5\.7  |  Supported  |  Supported  |  Supported  | 
|  Aurora MySQL 5\.6  |  Supported  |  Not supported  |  Not supported  | 

For an Aurora MySQL 5\.7 DB cluster, you can use the `tls_version` DB cluster parameter to indicate the permitted protocol versions\. Similar client parameters exist for most client tools or database drivers\. Some older clients might not support newer TLS versions\. By default, the DB cluster attempts to use the highest TLS protocol version allowed by both the server and client configuration\.

Set the `tls_version` DB cluster parameter to one of the following values:
+ `TLSv1.2` – Only the TLS version 1\.2 protocol is permitted for encrypted connections\.
+ `TLSv1.1` – TLS version 1\.1 and 1\.2 protocols are permitted for encrypted connections\.
+ `TLSv1` – TLS version 1\.0, 1\.1, and 1\.2 protocols are permitted for encrypted connections\.

If the parameter isn't set, then TLS version 1\.0, 1\.1, and 1\.2 protocols are permitted for encrypted connections\.

**Note**  
The `tls_version` DB cluster parameter isn't available for Aurora MySQL 5\.6\.

### Encrypting Connections to an Aurora MySQL DB Cluster<a name="AuroraMySQL.Security.SSL.EncryptingConnections"></a>

To encrypt connections using the default `mysql` client, launch the mysql client using the `--ssl-ca parameter` to reference the public key, for example: 

For MySQL 5\.7 and later:

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-mode=VERIFY_IDENTITY
```

For MySQL 5\.6 and earlier:

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-verify-server-cert
```

You can require SSL/TLS connections for specific users accounts\. For example, you can use one of the following statements, depending on your MySQL version, to require SSL/TLS connections on the user account `encrypted_user`\.

For MySQL 5\.7 and later:

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For MySQL 5\.6 and earlier:

```
GRANT USAGE ON *.* TO 'encrypted_user'@'%' REQUIRE SSL;            
```

 When you use an RDS proxy, you connect to the proxy endpoint instead of the usual cluster endpoint\. You can make SSL/TLS required or optional for connections to the proxy, in the same way as for connections directly to the Aurora DB cluster\. For information about using the RDS Proxy, see [Managing Connections with Amazon RDS Proxy](rds-proxy.md)\. 

**Note**  
For more information on SSL/TLS connections with MySQL, see the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)\.