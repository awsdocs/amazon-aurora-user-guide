# Using Kerberos authentication with Aurora PostgreSQL<a name="postgresql-kerberos"></a>

You can use Kerberos to authenticate users when they connect to your DB cluster running PostgreSQL\. In this case, your DB instance works with AWS Directory Service for Microsoft Active Directory to enable Kerberos authentication\. AWS Directory Service for Microsoft Active Directory is also called AWS Managed Microsoft AD\. 

You create an AWS Managed Microsoft AD directory to store user credentials\. You then provide to your PostgreSQL DB cluster the Active Directory's domain and other information\. When users authenticate with the PostgreSQL DB cluster, authentication requests are forwarded to the AWS Managed Microsoft AD directory\. 

Keeping all of your credentials in the same directory can save you time and effort\. You have a centralized place for storing and managing credentials for multiple DB clusters\. Using a directory can also improve your overall security profile\.

You can also access credentials from your own on\-premises Microsoft Active Directory\. To do so you create a trusting domain relationship so that the AWS Managed Microsoft AD directory trusts your on\-premises Microsoft Active Directory\. In this way, your users can access your PostgreSQL clusters with the same Windows single sign\-on \(SSO\) experience as when they access workloads in your on\-premises network\.

A database can use Kerberos, AWS Identity and Access Management \(IAM\), or both Kerberos and IAM authentication\. However, since Kerberos and IAM authentication provide different authentication methods, a specific user can log in to a database using only one or the other authentication method but not both\. For more information about IAM authentication, see [IAM database authentication](UsingWithRDS.IAMDBAuth.md)\. 

**Topics**
+ [Availability of Kerberos authentication](#postgresql-kerberos-availability)
+ [Overview of Kerberos authentication for PostgreSQL DB clusters](#postgresql-kerberos-overview)
+ [Setting up Kerberos authentication for PostgreSQL DB clusters](postgresql-kerberos-setting-up.md)
+ [Managing a DB cluster in a Domain](postgresql-kerberos-managing.md)
+ [Connecting to PostgreSQL with Kerberos authentication](postgresql-kerberos-connecting.md)

## Availability of Kerberos authentication<a name="postgresql-kerberos-availability"></a>

Kerberos authentication is supported on the following engine versions:
+ All PostgreSQL 13 versions
+ PostgreSQL 12\.4 and higher 12 versions
+ PostgreSQL 11\.6 and higher 11 versions
+ PostgreSQL 10\.11 and higher 10 versions

For more information, see [Amazon Aurora PostgreSQL releases and engine versions](AuroraPostgreSQL.Updates.20180305.md)\.

Amazon Aurora supports Kerberos authentication for PostgreSQL DB clusters in the following AWS Regions: 


| Region name | Region | 
| --- | --- | 
| US East \(Ohio\) | us\-east\-2 | 
| US East \(N\. Virginia\) | us\-east\-1 | 
| US West \(N\. California\) | us\-west\-1 | 
| US West \(Oregon\) | us\-west\-2 | 
| Asia Pacific \(Mumbai\) | ap\-south\-1 | 
| Asia Pacific \(Seoul\) | ap\-northeast\-2 | 
| Asia Pacific \(Singapore\) | ap\-southeast\-1 | 
| Asia Pacific \(Sydney\) | ap\-southeast\-2 | 
| Asia Pacific \(Tokyo\) | ap\-northeast\-1 | 
| Canada \(Central\) | ca\-central\-1 | 
| China \(Beijing\) | cn\-north\-1  | 
| China \(Ningxia\) | cn\-northwest\-1 | 
| Europe \(Frankfurt\) | eu\-central\-1 | 
| Europe \(Ireland\) | eu\-west\-1 | 
| Europe \(London\) | eu\-west\-2 | 
|  Europe \(Paris\)  |  eu\-west\-3  | 
| Europe \(Stockholm\) | eu\-north\-1 | 
| South America \(São Paulo\) | sa\-east\-1 | 

## Overview of Kerberos authentication for PostgreSQL DB clusters<a name="postgresql-kerberos-overview"></a>

To set up Kerberos authentication for a PostgreSQL DB cluster, take the following steps, described in more detail later:

1. Use AWS Managed Microsoft AD to create an AWS Managed Microsoft AD directory\. You can use the AWS Management Console, the AWS CLI, or the AWS Directory Service API to create the directory\. Make sure to open the relevant outbound ports on the directory security group so that the directory can communicate with the cluster\.

1. Create a role that provides Amazon Aurora access to make calls to your AWS Managed Microsoft AD directory\. To do so, create an AWS Identity and Access Management \(IAM\) role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. 

   For the IAM role to allow access, the AWS Security Token Service \(AWS STS\) endpoint must be activated in the correct AWS Region for your AWS account\. AWS STS endpoints are active by default in all AWS Regions, and you can use them without any further actions\. For more information, see [Activating and deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#sts-regions-activate-deactivate) in the *IAM User Guide*\.

1. Create and configure users in the AWS Managed Microsoft AD directory using the Microsoft Active Directory tools\. For more information about creating users in your Active Directory, see [Manage users and groups in AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html) in the *AWS Directory Service Administration Guide*\.

1. If you plan to locate the directory and the DB instance in different AWS accounts or virtual private clouds \(VPCs\), configure VPC peering\. For more information, see [What is VPC peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

1. Create or modify a PostgreSQL DB cluster either from the console, CLI, or RDS API using one of the following methods:
   + [Creating a DB cluster and connecting to a database on an Aurora PostgreSQL DB cluster](CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL.md) 
   + [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)
   + [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)
   + [Restoring a DB cluster to a specified time](aurora-pitr.md)

   You can locate the cluster in the same Amazon Virtual Private Cloud \(VPC\) as the directory or in a different AWS account or VPC\. When you create or modify the PostgreSQL DB cluster, do the following:
   + Provide the domain identifier \(`d-*` identifier\) that was generated when you created your directory\.
   + Provide the name of the IAM role that you created\.
   + Ensure that the DB instance security group can receive inbound traffic from the directory security group\.

1. Use the RDS master user credentials to connect to the PostgreSQL DB cluster\. Create the user in PostgreSQL to be identified externally\. Externally identified users can log in to the PostgreSQL DB cluster using Kerberos authentication\.