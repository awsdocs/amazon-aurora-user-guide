# Create an Amazon Aurora DB cluster<a name="CHAP_Tutorials.WebServerDB.CreateDBCluster"></a>

Create an Amazon Aurora MySQL DB cluster that maintains the data used by a web application\.

**To create an Aurora MySQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, make sure the AWS Region is the same as the one where you created your EC2 instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. On the **Create database** page, shown following, make sure that the **Standard create** option is chosen, and then choose **Amazon Aurora**\. Keep the default values for **Version** and the other engine options\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch01.png)

1. In the **Templates** section, choose **Dev/Test**\.

1. In the **Settings** section, set these values:
   + **DB cluster identifier** – Type **tutorial\-db\-cluster**\.
   + **Master username** – Type **tutorial\_user**\.
   + **Auto generate a password** – Leave the option turned off\.
   + **Master password** – Type a password\.
   + **Confirm password** – Retype the password\.  
![\[Settings sections\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_Settings-Aurora.png)

1. In the **Instance configuration** section, set these values:
   + **Burstable classes \(includes t classes\)**
   + **db\.t3\.small** or **db\.t3\.medium**
**Note**  
We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more details on the T instance classes, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.  
![\[Instance configuration section\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_DB_instance_small_aurora.png)

1. In the **Availability and durability** section, use the default values\.

1. In the **Connectivity** section, set these values and keep the other values as their defaults:
   + For **Compute resource**, choose **Connect to an EC2 compute resource**\.
   + For **EC2 instance**, choose the EC2 instance you created previously, such as **tutorial\-ec2\-instance\-web\-server**\.  
![\[Connectivity section\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_Connectivity.png)

1. Open the **Additional configuration** section, and enter **sample** for **Initial database name**\. Keep the default settings for the other options\.

1. To create your Aurora MySQL DB cluster, choose **Create database**\.

   Your new DB cluster appears in the **Databases** list with the status **Creating**\.

1. Wait for the **Status** of your new DB cluster to show as **Available**\. Then choose the DB cluster name to show its details\.

1. In the **Connectivity & security** section, view the **Endpoint** and **Port** of the writer DB instance\.  
![\[DB cluster details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_Endpoint_Port_Aurora.png)

   Note the endpoint and port for your writer DB instance\. You use this information to connect your web server to your DB cluster\.

1. Complete [Install a web server on your EC2 instance](CHAP_Tutorials.WebServerDB.CreateWebServer.md)\.