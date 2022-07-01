# Setting up your environment for Amazon Aurora<a name="CHAP_SettingUp_Aurora"></a>

Before you use Amazon Aurora for the first time, complete the following tasks\.

**Topics**
+ [Get an AWS account and your root user credentials](#getting-started-signup)
+ [Create an IAM user](#CHAP_SettingUp_Aurora.IAM)
+ [Sign in as an IAM user](#getting-started-signin-iam-user)
+ [Create IAM user access keys](#getting-started-iam-user-access-keys)
+ [Determine requirements](#CHAP_SettingUp_Aurora.Requirements)
+ [Provide access to the DB cluster in the VPC by creating a security group](#CHAP_SettingUp_Aurora.SecurityGroup)

If you already have an AWS account, know your Aurora requirements, and prefer to use the defaults for IAM and VPC security groups, skip ahead to [Getting started with Amazon Aurora](CHAP_GettingStartedAurora.md)\.

## Get an AWS account and your root user credentials<a name="getting-started-signup"></a>

To access AWS, you must sign up for an AWS account\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

 AWS sends you a confirmation email after the sign\-up process is complete\. At any time, you can view your current account activity and manage your account by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **My Account**\.

## Create an IAM user<a name="CHAP_SettingUp_Aurora.IAM"></a>

If your account already includes an IAM user with full AWS administrative permissions, you can skip this section\.

When you first create an Amazon Web Services \(AWS\) account, you begin with a single sign\-in identity\. That identity has complete access to all AWS services and resources in the account\. This identity is called the AWS account *root user*\. When you sign in, enter the email address and password that you used to create the account\.

**Important**  
We strongly recommend that you do not use the root user for your everyday tasks, even the administrative ones\. Instead, adhere to the [best practice of using the root user only to create your first IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users)\. Then securely lock away the root user credentials and use them to perform only a few account and service management tasks\. To view the tasks that require you to sign in as the root user, see [Tasks that require root user credentials](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user that follows and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add users**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \- job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

## Sign in as an IAM user<a name="getting-started-signin-iam-user"></a>

 Sign in to the [IAM console](https://console.aws.amazon.com/iam) by choosing **IAM user** and entering your AWS account ID or account alias\. On the next page, enter your IAM user name and your password\.

**Note**  
For your convenience, the AWS sign\-in page uses a browser cookie to remember your IAM user name and account information\. If you previously signed in as a different user, choose the sign\-in link beneath the button to return to the main sign\-in page\. From there, you can enter your AWS account ID or account alias to be redirected to the IAM user sign\-in page for your account\.

## Create IAM user access keys<a name="getting-started-iam-user-access-keys"></a>

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. As a best practice, do not use the AWS account root user access keys for any task where it's not required\. Instead, [create a new administrator IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) with access keys for yourself\.

The only time that you can view or download the secret access key is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions required to access IAM resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

**To create access keys for an IAM user**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose the name of the user whose access keys you want to create, and then choose the **Security credentials** tab\.

1. In the **Access keys** section, choose **Create access key**\.

1. To view the new access key pair, choose **Show**\. You will not have access to the secret access key again after this dialog box closes\. Your credentials will look something like this:
   + Access key ID: AKIAIOSFODNN7EXAMPLE
   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\. You will not have access to the secret access key again after this dialog box closes\.

   Keep the keys confidential in order to protect your AWS account and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

1. After you download the `.csv` file, choose **Close**\. When you create an access key, the key pair is active by default, and you can use the pair right away\.

**Related topics**
+ [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*
+ [AWS security credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Determine requirements<a name="CHAP_SettingUp_Aurora.Requirements"></a>

The basic building block of Aurora is the DB cluster\. One or more DB instances can belong to a DB cluster\. A DB cluster provides a network address called the *cluster endpoint*\. Your applications connect to the cluster endpoint exposed by the DB cluster whenever they need to access the databases created in that DB cluster\. The information you specify when you create the DB cluster controls configuration elements such as memory, database engine and version, network configuration, security, and maintenance periods\.

Before you create a DB cluster and a security group, you must know your DB cluster and network needs\. Here are some important things to consider:
+ **Resource requirements **– What are the memory and processor requirements for your application or service? You will use these settings when you determine what DB instance class you will use when you create your DB cluster\. For specifications about DB instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.
+ **VPC, subnet, and security group – **Your DB cluster will be in a virtual private cloud \(VPC\)\. Security group rules must be configured to connect to a DB cluster\. The following list describes the rules for each VPC option:
  + **Default VPC** — If your AWS account has a default VPC in the AWS Region, that VPC is configured to support DB clusters\. If you specify the default VPC when you create the DB cluster:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Aurora DB cluster\. Use the **Security Group** option on the VPC console or the AWS CLI to create VPC security groups\. For information, see [Step 3: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + You must specify the default DB subnet group\. If this is the first DB cluster you have created in the AWS Region, Amazon RDS will create the default DB subnet group when it creates the DB cluster\.
  + **User\-defined VPC** — If you want to specify a user\-defined VPC when you create a DB cluster:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Aurora DB cluster\. Use the **Security Group** option on the VPC console or the AWS CLI to create VPC security groups\. For information, see [Step 3: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + The VPC must meet certain requirements in order to host DB clusters, such as having at least two subnets, each in a separate availability zone\. For information, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.
    + You must specify a DB subnet group that defines which subnets in that VPC can be used by the DB cluster\. For information, see the DB Subnet Group section in [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ **High availability: **Do you need failover support? On Aurora, a Multi\-AZ deployment creates a primary instance and Aurora Replicas\. You can configure the primary instance and Aurora Replicas to be in different Availability Zones for failover support\. We recommend Multi\-AZ deployments for production workloads to maintain high availability\. For development and test purposes, you can use a non\-Multi\-AZ deployment\. For more information, see [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)\. 
+ **IAM policies: **Does your AWS account have policies that grant the permissions needed to perform Amazon RDS operations? If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.
+ **Open ports: **What TCP/IP port will your database be listening on? The firewall at some companies might block connections to the default port for your database engine\. If your company firewall blocks the default port, choose another port for the new DB cluster\. Note that once you create a DB cluster that listens on a port you specify, you can change the port by modifying the DB cluster\.
+ **AWS Region: **What AWS Region do you want your database in? Having the database close in proximity to the application or web service could reduce network latency\. For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.

Once you have the information you need to create the security group and the DB cluster, continue to the next step\.

## Provide access to the DB cluster in the VPC by creating a security group<a name="CHAP_SettingUp_Aurora.SecurityGroup"></a>

Your DB cluster will be created in a VPC\. Security groups provide access to the DB cluster in the VPC\. They act as a firewall for the associated DB cluster, controlling both inbound and outbound traffic at the cluster level\. DB clusters are created by default with a firewall and a default security group that prevents access to the DB cluster\. You must therefore add rules to a security group that enable you to connect to your DB cluster\. Use the network and configuration information you determined in the previous step to create rules to allow access to your DB cluster\.

For example, if you have an application that will access a database on your DB cluster in a VPC, you must add a custom TCP rule that specifies the port range and IP addresses that application will use to access the database\. If you have an application on an Amazon EC2 cluster, you can use the VPC security group you set up for the Amazon EC2 cluster\.

For more information about creating a VPC for use with Aurora, see [Tutorial: Create an Amazon VPC for use with a DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. For information about common scenarios for accessing a DB instance, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

**To create a VPC security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\.
**Note**  
Make sure you are in the VPC console, not the RDS console\.

1. In the top right corner of the AWS Management Console, choose the AWS Region where you want to create your VPC security group and DB cluster\. In the list of Amazon VPC resources for that AWS Region, you should see at least one VPC and several subnets\. If you don't, you don't have a default VPC in that AWS Region\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

   The **Create security group** page appears\.

1. In **Basic details**, enter the **Security group name** and **Description**\. For **VPC**, choose the VPC that you want to create your DB cluster in\.

1. In **Inbound rules**, choose **Add rule**\.

   1. For **Type**, choose **Custom TCP**\.

   1. For **Port range**, enter the port value to use for your DB cluster\.

   1. For **Source**, choose a security group name or type the IP address range \(CIDR value\) from where you access the DB cluster\. If you choose **My IP**, this allows access to the DB cluster from the IP address detected in your browser\.

1. If you need to add more IP addresses or different port ranges, choose **Add rule** and enter the information for the rule\.

1. \(Optional\) In **Outbound rules**, add rules for outbound traffic\. By default, all outbound traffic is allowed\.

1. Choose **Create security group**\.

You can use the VPC security group you just created as the security group for your DB cluster when you create it\.

**Note**  
If you use a default VPC, a default subnet group spanning all of the VPC's subnets is created for you\. When you create a DB cluster, you can select the default VPC and use **default** for **DB Subnet Group**\.

Once you have completed the setup requirements, you can create a DB cluster using your requirements and security group by following the instructions in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. For information about getting started by creating a DB cluster that uses a specific DB engine, see [Getting started with Amazon Aurora](CHAP_GettingStartedAurora.md)\.