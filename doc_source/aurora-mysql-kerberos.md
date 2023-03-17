# Using Kerberos authentication for Aurora MySQL<a name="aurora-mysql-kerberos"></a>

You can use Kerberos authentication to authenticate users when they connect to your Aurora MySQL DB cluster\. To do so, configure your DB cluster to use AWS Directory Service for Microsoft Active Directory for Kerberos authentication\. AWS Directory Service for Microsoft Active Directory is also called AWS Managed Microsoft AD\. It's a feature available with AWS Directory Service\. To learn more, see [What is AWS Directory Service?](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html) in the *AWS Directory Service Administration Guide*\.

To start, create an AWS Managed Microsoft AD directory to store user credentials\. Then, provide the Active Directory's domain and other information to your Aurora MySQL DB cluster\. When users authenticate with the Aurora MySQL DB cluster, authentication requests are forwarded to the AWS Managed Microsoft AD directory\.

Keeping all of your credentials in the same directory can save you time and effort\. With this approach, you have a centralized location for storing and managing credentials for multiple DB clusters\. Using a directory can also improve your overall security profile\.

In addition, you can access credentials from your own on\-premises Microsoft Active Directory\. To do so, create a trusting domain relationship so that the AWS Managed Microsoft AD directory trusts your on\-premises Microsoft Active Directory\. In this way, your users can access your Aurora MySQL DB clusters with the same Windows single sign\-on \(SSO\) experience as when they access workloads in your on\-premises network\.

A database can use Kerberos, AWS Identity and Access Management \(IAM\), or both Kerberos and IAM authentication\. However, because Kerberos and IAM authentication provide different authentication methods, a specific user can log in to a database using only one or the other authentication method, but not both\. For more information about IAM authentication, see [IAM database authentication](UsingWithRDS.IAMDBAuth.md)\.

**Contents**
+ [Overview of Kerberos authentication for Aurora MySQL DB clusters](#aurora-mysql-kerberos-setting-up-overview)
+ [Limitations of Kerberos authentication for Aurora MySQL](#aurora-mysql-kerberos.limitations)
+ [Setting up Kerberos authentication for Aurora MySQL DB clusters](aurora-mysql-kerberos-setting-up.md)
  + [Step 1: Create a directory using AWS Managed Microsoft AD](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-directory)
  + [Step 2: \(Optional\) Create a trust for an on\-premises Active Directory](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-trust)
  + [Step 3: Create an IAM role for use by Amazon Aurora](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.CreateIAMRole)
  + [Step 4: Create and configure users](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-users)
  + [Step 5: Create or modify an Aurora MySQL DB cluster](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-modify)
  + [Step 6: Create Aurora MySQL users that use Kerberos authentication](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-logins)
    + [Modifying an existing Aurora MySQL login](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos.modify-login)
  + [Step 7: Configure a MySQL client](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.configure-client)
+ [Connecting to Aurora MySQL with Kerberos authentication](aurora-mysql-kerberos-connecting.md)
  + [Using the Aurora MySQL Kerberos login to connect to the DB cluster](aurora-mysql-kerberos-connecting.md#aurora-mysql-kerberos-connecting.login)
  + [Kerberos authentication with Aurora global databases](aurora-mysql-kerberos-connecting.md#aurora-mysql-kerberos-connecting.global)
  + [Migrating from RDS for MySQL to Aurora MySQL](aurora-mysql-kerberos-connecting.md#aurora-mysql-kerberos-connecting.rds)
  + [Preventing ticket caching](aurora-mysql-kerberos-connecting.md#aurora-mysql-kerberos.destroy-tickets)
  + [Logging for Kerberos authentication](aurora-mysql-kerberos-connecting.md#aurora-mysql-kerberos.logging)
+ [Managing a DB cluster in a domain](aurora-mysql-kerberos-managing.md)
  + [Understanding domain membership](aurora-mysql-kerberos-managing.md#aurora-mysql-kerberos-managing.understanding)

## Overview of Kerberos authentication for Aurora MySQL DB clusters<a name="aurora-mysql-kerberos-setting-up-overview"></a>

To set up Kerberos authentication for an Aurora MySQL DB cluster, complete the following general steps\. These steps are described in more detail later\.

1. Use AWS Managed Microsoft AD to create an AWS Managed Microsoft AD directory\. You can use the AWS Management Console, the AWS CLI, or the AWS Directory Service to create the directory\. For detailed instructions, see [Create your AWS Managed Microsoft AD directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_create_directory.html) in the *AWS Directory Service Administration Guide*\.

1. Create an AWS Identity and Access Management \(IAM\) role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. The role allows Amazon Aurora to make calls to your directory\.

   For the role to allow access, the AWS Security Token Service \(AWS STS\) endpoint must be activated in the AWS Region for your AWS account\. AWS STS endpoints are active by default in all AWS Regions, and you can use them without any further action\. For more information, see [ Activating and deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#sts-regions-activate-deactivate) in the *IAM User Guide*\.

1. Create and configure users in the AWS Managed Microsoft AD directory using the Microsoft Active Directory tools\. For more information about creating users in your Active Directory, see [Manage users and groups in AWS managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html) in the *AWS Directory Service Administration Guide*\.

1. Create or modify an Aurora MySQL DB cluster\. If you use either the CLI or RDS API in the create request, specify a domain identifier with the `Domain` parameter\. Use the `d-*` identifier that was generated when you created your directory and the name of the IAM role that you created\.

   If you modify an existing Aurora MySQL DB cluster to use Kerberos authentication, set the domain and IAM role parameters for the DB cluster\. Locate the DB cluster in the same VPC as the domain directory\.

1. Use the Amazon RDS primary user credentials to connect to the Aurora MySQL DB cluster\. Create the database user in Aurora MySQL by using the instructions in [Step 6: Create Aurora MySQL users that use Kerberos authentication](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-logins)\.

   Users that you create this way can log in to the Aurora MySQL DB cluster using Kerberos authentication\. For more information, see [Connecting to Aurora MySQL with Kerberos authentication](aurora-mysql-kerberos-connecting.md)\.

To use Kerberos authentication with an on\-premises or self\-hosted Microsoft Active Directory, create a *forest trust*\. A forest trust is a trust relationship between two groups of domains\. The trust can be one\-way or two\-way\. For more information about setting up forest trusts using AWS Directory Service, see [When to create a trust relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

## Limitations of Kerberos authentication for Aurora MySQL<a name="aurora-mysql-kerberos.limitations"></a>

The following limitations apply to Kerberos authentication for Aurora MySQL:
+ Kerberos authentication is supported for Aurora MySQL version 3\.03 and higher\.

  For information about AWS Region support, see [Kerberos authentication with Aurora MySQL](Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.amy)\.
+ To use Kerberos authentication with Aurora MySQL, your MySQL client or connector must use version 8\.0\.26 or higher on Unix platforms, 8\.0\.27 or higher on Windows\. Otherwise, the client\-side `authentication_kerberos_client` plugin isn't available and you can't authenticate\.
+ Only AWS Managed Microsoft AD is supported on Aurora MySQL\. However, you can join Aurora MySQL DB clusters to shared Managed Microsoft AD domains owned by different accounts in the same AWS Region\.

  You can also use your own on\-premises Active Directory\. For more information, see [Step 2: \(Optional\) Create a trust for an on\-premises Active Directory](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-trust)
+ When using Kerberos to authenticate a user connecting to the Aurora MySQL cluster from MySQL clients or from drivers on the Windows operating system, the character case of the database user name must match the case of the user in the Active Directory\. For example, if the user in the Active Directory appears as `Admin`, the database user name must be `Admin`\.
+ You must reboot the reader DB instances after turning on the feature to install the `authentication_kerberos` plugin\.
+ Replicating to DB instances that don't support the `authentication_kerberos` plugin can lead to replication failure\.
+ For Aurora global databases to use Kerberos authentication, you must configure it for every DB cluster in the global database\.
+ The domain name must be less than 62 characters long\.
+ Don't modify the DB cluster port after turning on Kerberos authentication\. If you modify the port, then Kerberos authentication will no longer work\. turned or some other time? 