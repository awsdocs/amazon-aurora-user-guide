# Security with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Security"></a>

Security for Amazon Aurora PostgreSQL is managed at three levels:
+ To control who can perform Amazon RDS management actions on Aurora DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Authentication and Access Control](UsingWithRDS.IAM.md)\.

  If you are using an IAM account to access the Amazon RDS console, you must first log on to the AWS Management Console with your IAM account, and then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds](https://console.aws.amazon.com/rds)\.
+ Aurora DB clusters must be created in an Amazon Virtual Private Cloud \(VPC\)\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora DB clusters in a VPC, you use a VPC security group\. These endpoint and port connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.
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