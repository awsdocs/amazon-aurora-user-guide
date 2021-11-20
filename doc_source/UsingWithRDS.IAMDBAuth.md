# IAM database authentication<a name="UsingWithRDS.IAMDBAuth"></a>

You can authenticate to your DB cluster using AWS Identity and Access Management \(IAM\) database authentication\. IAM database authentication works with Aurora MySQL and Aurora PostgreSQL\. With this authentication method, you don't need to use a password when you connect to a DB cluster\. Instead, you use an authentication token\.

An *authentication token* is a unique string of characters that Amazon Aurora generates on request\. Authentication tokens are generated using AWS Signature Version 4\. Each token has a lifetime of 15 minutes\. You don't need to store user credentials in the database, because authentication is managed externally using IAM\. You can also still use standard database authentication\. The token is only used for authentication and doesn't affect the session after it is established\.

IAM database authentication provides the following benefits:
+ Network traffic to and from the database is encrypted using Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\)\. For more information about using SSL/TLS with Amazon Aurora, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.
+ You can use IAM to centrally manage access to your database resources, instead of managing access individually on each DB cluster\.
+ For applications running on Amazon EC2, you can use profile credentials specific to your EC2 instance to access your database instead of a password, for greater security\.

**Topics**
+ [Availability for IAM database authentication](#UsingWithRDS.IAMDBAuth.Availability)
+ [Limitations for IAM database authentication](#UsingWithRDS.IAMDBAuth.Limitations)
+ [Aurora MySQL recommendations for IAM database authentication](#UsingWithRDS.IAMDBAuth.ConnectionsPerSecond)
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)
+ [Connecting to your DB cluster using IAM authentication](UsingWithRDS.IAMDBAuth.Connecting.md)

## Availability for IAM database authentication<a name="UsingWithRDS.IAMDBAuth.Availability"></a>

IAM database authentication is available for the following database engines:
+ **Aurora MySQL**
  + Aurora MySQL version 3, all minor versions
  + Aurora MySQL version 2, all minor versions
  + Aurora MySQL version 1\.10 and higher 1\.1 minor versions
+ **Aurora PostgreSQL**
  + All Aurora PostgreSQL 13 versions
  + All Aurora PostgreSQL 12 versions
  + Aurora PostgreSQL 11\.6 and higher 11 versions
  + Aurora PostgreSQL 10\.11 and higher 10 versions
  + Aurora PostgreSQL 9\.6\.16 and higher 9\.6 versions

  For more information, see [Amazon Aurora PostgreSQL releases and engine versions](AuroraPostgreSQL.Updates.20180305.md)\.

For Aurora MySQL, all supported DB instance classes support IAM database authentication, except for db\.t2\.small and db\.t3\.small\. For information about the supported DB instance classes, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\.

## Limitations for IAM database authentication<a name="UsingWithRDS.IAMDBAuth.Limitations"></a>

When using IAM database authentication, the following limitations apply:
+ The maximum number of connections per second for your DB cluster might be limited depending on its DB instance class and your workload\.
+ Currently, IAM database authentication doesn't support all global condition context keys\.

  For more information about global condition context keys, see [ AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.
+ Currently, IAM database authentication isn't supported for CNAMEs\.
+ For PostgreSQL, if the IAM role \(`rds_iam`\) is added to the master user, IAM authentication takes precedence over Password authentication so the master user has to log in as an IAM user\.

## Aurora MySQL recommendations for IAM database authentication<a name="UsingWithRDS.IAMDBAuth.ConnectionsPerSecond"></a>

We recommend the following when using the Aurora MySQL DB engine:
+ Use IAM database authentication as a mechanism for temporary, personal access to databases\.
+ Use IAM database authentication only for workloads that can be easily retried\.
+ Use IAM database authentication when your application requires fewer than 200 new IAM database authentication connections per second\.

  The database engines that work with Amazon Aurora don't impose any limits on authentication attempts per second\. However, when you use IAM database authentication, your application must generate an authentication token\. Your application then uses that token to connect to the DB cluster\. If you exceed the limit of maximum new connections per second, then the extra overhead of IAM database authentication can cause connection throttling\. The extra overhead can cause even existing connections to drop\.   For information about the maximum total connections for Aurora MySQL, see [Maximum connections to an Aurora MySQL DB instance](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.MaxConnections)\. 

**Note**  
These recommendations don't apply to Aurora PostgreSQL DB clusters\.