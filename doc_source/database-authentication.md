# Database Authentication with Amazon Aurora<a name="database-authentication"></a>

Amazon Aurora supports several ways to authenticate database users\.

**Topics**
+ [Password Authentication](#password-authentication)
+ [IAM Database Authentication](#iam-database-authentication)
+ [Kerberos Authentication](#kerberos-authentication)

## Password Authentication<a name="password-authentication"></a>

With *password authentication,* your DB instance performs all administration of user accounts\. You create users with SQL statements such as `CREATE USER` and specify passwords in the `IDENTIFIED BY` clause\.

All RDS DB engines support password authentication\. For more information about password authentication, see the documentation for your DB engine\.

With password authentication, your database controls and authenticates user accounts\. If a DB engine has strong password management features, they can enhance security\. Database authentication might be easier to administer using password authentication when you have small user communities\. Because clear text passwords are generated in this case, integrating with AWS Secrets Manager can enhance security\.

For information about using Secrets Manager with Amazon Aurora, see [Creating a Basic Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) and [Rotating Secrets for Supported Amazon RDS Databases](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets-rds.html) in the *AWS Secrets Manager User Guide*\. For information about programmatically retrieving your secrets in your custom applications, see [Retrieving the Secret Value](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_retrieve-secret.html) in the *AWS Secrets Manager User Guide*\.

## IAM Database Authentication<a name="iam-database-authentication"></a>

You can authenticate to your DB cluster using AWS Identity and Access Management \(IAM\) database authentication\. IAM database authentication works with Aurora MySQL and Aurora PostgreSQL\. With this authentication method, you don't need to use a password when you connect to a DB cluster\. Instead, you use an authentication token\.

For more information about IAM database authentication, including information about availability for specific DB engines, see [IAM Database Authentication](UsingWithRDS.IAMDBAuth.md)\.

## Kerberos Authentication<a name="kerberos-authentication"></a>

Amazon Aurora supports external authentication of database users using Kerberos and Microsoft Active Directory\. Kerberos is a network authentication protocol that uses tickets and symmetric\-key cryptography to eliminate the need to transmit passwords over the network\. Kerberos has been built into Active Directory and is designed to authenticate users to network resources, such as databases\.

Amazon Aurora support for Kerberos and Active Directory provides the benefits of single sign\-on and centralized authentication of database users\. You can keep your user credentials in Active Directory\. Active Directory provides a centralized place for storing and managing credentials for multiple DB instances\.

You can enable your database users to authenticate against DB instances in two ways\. They can use credentials stored either in AWS Directory Service for Microsoft Active Directory or in your on\-premises Active Directory\.

Currently, Aurora supports Kerberos authentication for Aurora PostgreSQL DB clusters\. With Kerberos authentication, Aurora PostgreSQL DB clusters support one\- and two\-way external and forest trust relationships\. For more information, see [Using Kerberos Authentication with Aurora PostgreSQL](postgresql-kerberos.md)\.