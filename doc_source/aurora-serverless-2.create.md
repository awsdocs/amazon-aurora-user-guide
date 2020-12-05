# Creating an Aurora Serverless v2 \(preview\) DB cluster<a name="aurora-serverless-2.create"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. | 

To work with Amazon Aurora Serverless v2 \(preview\), you must apply for access\. For more information, see the [Aurora Serverless v2 \(preview\) page](https://pages.awscloud.com/AmazonAuroraServerlessv2Preview.html)\. 

After you've been approved for access, you can sign in to the preview using the console\. Currently, you can create an Amazon Aurora Serverless v2 \(preview\) DB cluster with the console only\.

**To create an Aurora Serverless v2 \(preview\) DB cluster**

1. Sign in to the preview using the AWS Management Console and open the Amazon RDS console\. 

1. Choose **Create Database**\. For this preview, you see the available choices preselected for **Engine options**: 
   + Amazon Aurora for **Engine type**
   + Amazon Aurora with MySQL compatibility for **Edition**
   + Aurora Serverless v2 \(preview\) for **Capacity configuration**
   + Aurora \(MySQL 5\.7\) 2\.07\.1 for **Version**  
![\[Screenshot of creating DB cluster with console, engine options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-create-db-1.png)

1. For **Settings**, do the following:

   1. Accept the default DB cluster identifier or choose your own\. 

   1. Enter your own password for the default `admin` account for the DB cluster, or have Aurora Serverless v2 \(preview\) generate one for you\. If you choose **Auto generate a password**, you get an option to copy the password\.  
![\[Screenshot Create DB cluster, Settings options.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-create-db-2.png)

1. For **Capacity settings**, you can accept the default range \(4 ACUs minimum to 32 ACUs maximum\)\. Or you can choose other values for minimum and maximum capacity units\. 

   For more information about Aurora Serverless v2 \(preview\) capacity units, see [Instant autoscaling](aurora-serverless-2.how-it-works.md#aurora-serverless-2.how-it-works.autoscaling)\.  
![\[Screenshot of creating DB cluster with console, Capacity settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-create-db-3.png)

1. For **Connectivity**, choose the virtual private cloud \(VPC\) based on Amazon VPC that defines the virtual networking environment for this DB instance\. You can choose the defaults to simplify this task\. 

   To use your own VPC, we recommend that you create it along with related subnets, subnet group, and security group in advance\. If you do this, these are available for you to choose when you're creating your Aurora Serverless v2 \(preview\) DB cluster\. 

   To learn how, see [How to create a VPC for use with Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateVPC.html)\.  
![\[Screenshot of creating DB cluster with console, Connectivity options.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-create-db-4.png)

1. For **Additional configuration**, enter a name for **Initial database name** to create a database for your Aurora Serverless v2 \(preview\) cluster\.   
![\[Screenshot of creating DB cluster with console, additional configuration options.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-create-db-5.png)

   The Aurora Serverless v2 \(preview\) cluster volume is always encrypted\. You can't disable encryption, but you can choose your own encryption key\. For more information, see [Encrypting Amazon Aurora resources](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Overview.Encryption.html#Overview.Encryption.Enabling)\. 

1. Acknowledge the limited service agreement\.  
![\[Screenshot of Create DB cluster, accepting the license agreement.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-create-db-6.png)

1. Choose **Create database** to create your Aurora Serverless v2 \(preview\) DB cluster\. 

You can connect to your Aurora Serverless v2 \(preview\) DB cluster by using its endpoint\. The endpoint is listed in the **Connectivity & security** tab of the console\. For more information, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.