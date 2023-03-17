# Setting up Kerberos authentication for Aurora MySQL DB clusters<a name="aurora-mysql-kerberos-setting-up"></a>

Use AWS Managed Microsoft AD to set up Kerberos authentication for an Aurora MySQL DB cluster\. To set up Kerberos authentication, take the following steps\.

**Topics**
+ [Step 1: Create a directory using AWS Managed Microsoft AD](#aurora-mysql-kerberos-setting-up.create-directory)
+ [Step 2: \(Optional\) Create a trust for an on\-premises Active Directory](#aurora-mysql-kerberos-setting-up.create-trust)
+ [Step 3: Create an IAM role for use by Amazon Aurora](#aurora-mysql-kerberos-setting-up.CreateIAMRole)
+ [Step 4: Create and configure users](#aurora-mysql-kerberos-setting-up.create-users)
+ [Step 5: Create or modify an Aurora MySQL DB cluster](#aurora-mysql-kerberos-setting-up.create-modify)
+ [Step 6: Create Aurora MySQL users that use Kerberos authentication](#aurora-mysql-kerberos-setting-up.create-logins)
+ [Step 7: Configure a MySQL client](#aurora-mysql-kerberos-setting-up.configure-client)

## Step 1: Create a directory using AWS Managed Microsoft AD<a name="aurora-mysql-kerberos-setting-up.create-directory"></a>

AWS Directory Service creates a fully managed Active Directory in the AWS Cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and Domain Name System \(DNS\) servers on your behalf\. The directory servers are created in different subnets in a VPC\. This redundancy helps make sure that your directory remains accessible even if a failure occurs\.

When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf:
+ Sets up an Active Directory within the VPC\.
+ Creates a directory administrator account with the user name `Admin` and the specified password\. You use this account to manage your directory\.
**Note**  
Be sure to save this password\. AWS Directory Service doesn't store it\. You can reset it, but you can't retrieve it\.
+ Creates a security group for the directory controllers\.

When you launch an AWS Managed Microsoft AD, AWS creates an Organizational Unit \(OU\) that contains all of your directory's objects\. This OU has the NetBIOS name that you entered when you created your directory\. It is located in the domain root, which is owned and managed by AWS\.

The `Admin` account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU, including:
+ Create, update, or delete users
+ Add resources to your domain, such as file or print servers, and then assign permissions for those resources to users in your OU
+ Create additional OUs and containers
+ Delegate authority
+ Restore deleted objects from the Active Directory Recycle Bin
+ Run AD and DNS Windows PowerShell modules on the Active Directory Web Service 

The `Admin` account also has rights to perform the following domain\-wide activities:
+ Manage DNS configurations \(add, remove, or update records, zones, and forwarders\)
+ View DNS event logs
+ View security event logs

**To create a directory with AWS Managed Microsoft AD**

1. Sign in to the AWS Management Console and open the AWS Directory Service console at [https://console\.aws\.amazon\.com/directoryservicev2/](https://console.aws.amazon.com/directoryservicev2/)\.

1. In the navigation pane, choose **Directories** and choose **Set up Directory**\.

1. Choose **AWS Managed Microsoft AD**\. AWS Managed Microsoft AD is the only option that you can currently use with Amazon RDS\.

1. Enter the following information:  
**Directory DNS name**  
The fully qualified name for the directory, such as **corp\.example\.com**\.  
**Directory NetBIOS name**  
The short name for the directory, such as **CORP**\.  
**Directory description**  
\(Optional\) A description for the directory\.  
**Admin password**  
The password for the directory administrator\. The directory creation process creates an administrator account with the user name Admin and this password\.  
The directory administrator password and can't include the word "admin\." The password is case\-sensitive and must be 8–64 characters in length\. It must also contain at least one character from three of the following four categories:  
   + Lowercase letters \(a–z\)
   + Uppercase letters \(A–Z\)
   + Numbers \(0–9\)
   + Non\-alphanumeric characters \(\~\!@\#$%^&\*\_\-\+=`\|\\\(\)\{\}\[\]:;"'<>,\.?/\)  
**Confirm password**  
The administrator password re\-entered\.

1. Choose **Next**\.

1.  Enter the following information in the **Networking** section and then choose **Next**:  
**VPC**  
The VPC for the directory\. Create the Aurora MySQL DB cluster in this same VPC\.  
**Subnets**  
Subnets for the directory servers\. The two subnets must be in different Availability Zones\.

1. Review the directory information and make any necessary changes\. When the information is correct, choose **Create directory**\.  
![\[Directory details page during creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/WinAuth2.png)

It takes several minutes to create the directory\. When it has been successfully created, the **Status** value changes to **Active**\.

To see information about your directory, choose the directory name in the directory listing\. Note the **Directory ID** value because you need this value when you create or modify your Aurora MySQL DB cluster\.

![\[Directory ID in the Directory details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/WinAuth3.png)

## Step 2: \(Optional\) Create a trust for an on\-premises Active Directory<a name="aurora-mysql-kerberos-setting-up.create-trust"></a>

If you don't plan to use your own on\-premises Microsoft Active Directory, skip to [Step 3: Create an IAM role for use by Amazon Aurora](#aurora-mysql-kerberos-setting-up.CreateIAMRole)\.

To use Kerberos authentication with your on\-premises Active Directory, you need to create a trusting domain relationship using a forest trust between your on\-premises Microsoft Active Directory and the AWS Managed Microsoft AD directory \(created in [Step 1: Create a directory using AWS Managed Microsoft AD](#aurora-mysql-kerberos-setting-up.create-directory)\)\. The trust can be one\-way, where the AWS Managed Microsoft AD directory trusts the on\-premises Microsoft Active Directory\. The trust can also be two\-way, where both Active Directories trust each other\. For more information about setting up trusts using AWS Directory Service, see [When to create a trust relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

**Note**  
If you use an on\-premises Microsoft Active Directory:  
Windows clients must connect using the domain name of the AWS Directory Service in the endpoint rather than rds\.amazonaws\.com\. For more information, see [Connecting to Aurora MySQL with Kerberos authentication](aurora-mysql-kerberos-connecting.md)\.
Windows clients can't connect using Aurora custom endpoints\. To learn more, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\.
For [global databases](aurora-global-database.md):  
Windows clients can connect using instance endpoints or cluster endpoints in the primary AWS Region of the global database only\.
Windows clients can't connect using cluster endpoints in secondary AWS Regions\.

Make sure that your on\-premises Microsoft Active Directory domain name includes a DNS suffix routing that corresponds to the newly created trust relationship\. The following screenshot shows an example\.

![\[DNS routing corresponds to the created trust\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/kerberos-auth-trust.png)

## Step 3: Create an IAM role for use by Amazon Aurora<a name="aurora-mysql-kerberos-setting-up.CreateIAMRole"></a>

For Amazon Aurora to call AWS Directory Service for you, you need an AWS Identity and Access Management \(IAM\) role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. This role allows Aurora to make calls to the AWS Directory Service\.

When you create a DB cluster using the AWS Management Console, and you have the `iam:CreateRole` permission, the console creates this role automatically\. In this case, the role name is `rds-directoryservice-kerberos-access-role`\. Otherwise, you must create the IAM role manually\. When you create this IAM role, choose `Directory Service`, and attach the AWS managed policy `AmazonRDSDirectoryServiceAccess` to it\.

For more information about creating IAM roles for a service, see [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

Optionally, you can create policies with the required permissions instead of using the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. In this case, the IAM role must have the following IAM trust policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "directoryservice.rds.amazonaws.com",
          "rds.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The role must also have the following IAM role policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ds:DescribeDirectories",
        "ds:AuthorizeApplication",
        "ds:UnauthorizeApplication",
        "ds:GetAuthorizedApplicationDetails"
      ],
    "Effect": "Allow",
    "Resource": "*"
    }
  ]
}
```

## Step 4: Create and configure users<a name="aurora-mysql-kerberos-setting-up.create-users"></a>

You can create users with the Active Directory Users and Computers tool\. This tool is part of the Active Directory Domain Services and Active Directory Lightweight Directory Services tools\. Users represent individual people or entities that have access to your directory\.

To create users in an AWS Directory Service directory, you use an on\-premises or Amazon EC2 instance based on Microsoft Windows that is joined to your AWS Directory Service directory\. You must be logged in to the instance as a user that has privileges to create users\. For more information, see [Manage users and groups in AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/creating_ad_users_and_groups.html) in the *AWS Directory Service Administration Guide*\.

## Step 5: Create or modify an Aurora MySQL DB cluster<a name="aurora-mysql-kerberos-setting-up.create-modify"></a>

Create or modify an Aurora MySQL DB cluster for use with your directory\. You can use the console, AWS CLI, or RDS API to associate a DB cluster with a directory\. You can do this task in one of the following ways:
+ Create a new Aurora MySQL DB cluster using the console, the [ create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) CLI command, or the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) RDS API operation\.

  For instructions, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.
+ Modify an existing Aurora MySQL DB cluster using the console, the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) CLI command, or the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) RDS API operation\.

  For instructions, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.
+ Restore an Aurora MySQL DB cluster from a DB snapshot using the console, the [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) CLI command, or the [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) RDS API operation\.

  For instructions, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.
+ Restore an Aurora MySQL DB cluster to a point\-in\-time using the console, the [ restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) CLI command, or the [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) RDS API operation\.

  For instructions, see [Restoring a DB cluster to a specified time](aurora-pitr.md)\.

Kerberos authentication is only supported for Aurora MySQL DB clusters in a VPC\. The DB cluster can be in the same VPC as the directory, or in a different VPC\. The DB cluster's VPC must have a VPC security group that allows outbound communication to your directory\. 

### Console<a name="aurora-mysql-kerberos-setting-up.create-modify.CON"></a>

When you use the console to create, modify, or restore a DB cluster, choose **Kerberos authentication** in the **Database authentication** section\. Choose **Browse Directory** and then select the directory, or choose **Create a new directory**\.

![\[Kerberos authentication setting when creating a DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/kerberos-auth-create-cluster.png)

### AWS CLI<a name="aurora-mysql-kerberos-setting-up.create-modify.CLI"></a>

When you use the AWS CLI or RDS API, associate a DB cluster with a directory\. The following parameters are required for the DB cluster to use the domain directory you created:
+ For the `--domain` parameter, use the domain identifier \("d\-\*" identifier\) generated when you created the directory\.
+ For the `--domain-iam-role-name` parameter, use the role you created that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\.

For example, the following CLI command modifies a DB cluster to use a directory\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier mydbcluster \
    --domain d-ID \
    --domain-iam-role-name role-name
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier mydbcluster ^
    --domain d-ID ^
    --domain-iam-role-name role-name
```

**Important**  
If you modify a DB cluster to turn on Kerberos authentication, reboot the reader DB instances after making the change\.

## Step 6: Create Aurora MySQL users that use Kerberos authentication<a name="aurora-mysql-kerberos-setting-up.create-logins"></a>

The DB cluster is joined to the AWS Managed Microsoft AD domain\. Thus, you can create Aurora MySQL users from the Active Directory users in your domain\. Database permissions are managed through standard Aurora MySQL permissions that are granted to and revoked from these users\.

You can allow an Active Directory user to authenticate with Aurora MySQL\. To do this, first use the Amazon RDS primary user credentials to connect to the Aurora MySQL DB cluster as with any other DB cluster\. After you're logged in, create an externally authenticated user with Kerberos authentication in Aurora MySQL as shown here:

```
CREATE USER user_name@'host_name' IDENTIFIED WITH 'authentication_kerberos' BY 'realm_name';
```
+ Replace `user_name` with the user name\. Users \(both humans and applications\) from your domain can now connect to the DB cluster from a domain\-joined client machine using Kerberos authentication\.
+ Replace `host_name` with the hostname\. You can use `%` as a wild card\. You can also use specific IP addresses for the hostname\.
+ Replace *realm\_name* with the directory realm name of the domain\. The realm name is usually the same as the DNS domain name in uppercase letters, such as `CORP.EXAMPLE.COM`\. A realm is a group of systems that use the same Kerberos Key Distribution Center\.

The following example creates a database user with the name `Admin` that authenticates against the Active Directory with the realm name `MYSQL.LOCAL`\.

```
CREATE USER Admin@'%' IDENTIFIED WITH 'authentication_kerberos' BY 'MYSQL.LOCAL';
```

### Modifying an existing Aurora MySQL login<a name="aurora-mysql-kerberos.modify-login"></a>

You can also modify an existing Aurora MySQL login to use Kerberos authentication by using the following syntax:

```
ALTER USER user_name IDENTIFIED WITH 'authentication_kerberos' BY 'realm_name';
```

## Step 7: Configure a MySQL client<a name="aurora-mysql-kerberos-setting-up.configure-client"></a>

To configure a MySQL client, take the following steps:

1. Create a `krb5.conf` file \(or equivalent\) to point to the domain\.

1. Verify that traffic can flow between the client host and AWS Directory Service\. Use a network utility such as Netcat, for the following:
   + Verify traffic over DNS for port 53\.
   + Verify traffic over TCP/UDP for port 53 and for Kerberos, which includes ports 88 and 464 for AWS Directory Service\.

1. Verify that traffic can flow between the client host and the DB instance over the database port\. For example, use `mysql` to connect and access the database\.

The following is sample `krb5.conf` content for AWS Managed Microsoft AD\.

```
[libdefaults]
 default_realm = EXAMPLE.COM
[realms]
 EXAMPLE.COM = {
  kdc = example.com
  admin_server = example.com
 }
[domain_realm]
 .example.com = EXAMPLE.COM
 example.com = EXAMPLE.COM
```

The following is sample `krb5.conf` content for an on\-premises Microsoft Active Directory\.

```
[libdefaults]
 default_realm = EXAMPLE.COM
[realms]
 EXAMPLE.COM = {
  kdc = example.com
  admin_server = example.com
 }
 ONPREM.COM = {
  kdc = onprem.com
  admin_server = onprem.com
 }
[domain_realm]
 .example.com = EXAMPLE.COM
 example.com = EXAMPLE.COM
 .onprem.com = ONPREM.COM
 onprem.com = ONPREM.COM  
 .rds.amazonaws.com = EXAMPLE.COM
 .amazonaws.com.cn = EXAMPLE.COM
 .amazon.com = EXAMPLE.COM
```