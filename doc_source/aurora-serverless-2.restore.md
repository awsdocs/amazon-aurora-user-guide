# Restoring an Aurora Serverless v2 \(preview\) DB cluster to a point in time<a name="aurora-serverless-2.restore"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. All resources and data will be deleted when the preview ends\.  | 

 You can create a new Aurora Serverless v2 \(preview\) DB cluster by restoring an existing DB cluster to a specific point in time\. You can also use this approach to recover from a failure by recreating your Aurora Serverless v2 \(preview\) DB cluster from its most recent log files\. 

 To restore your DB cluster to a specific point in time, Aurora creates a new DB cluster\. It then applies all transactions from the logs of the existing DB cluster to the new cluster\. Depending on the quantity and scope of transactions contained in the logs, this operation might take several hours to complete\. For more information about point\-in\-time restore, see [Restoring a DB cluster to a specified time](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PIT.html)\. 

 Currently, you can restore your Aurora Serverless v2 \(preview\) DB cluster using the console only\. 

**To restore an Aurora Serverless v2 \(preview\) DB cluster to a specified point in time**

1.  Sign in to the preview using the AWS Management Console and open the Amazon RDS console\. 

1.  Choose **Databases**\. 

1.  Choose the Aurora Serverless v2 \(preview\) DB cluster that you want to restore\. 

1.  For **Actions**, choose **Restore to point in time**\. The **Restore DB cluster** page appears\.   
![\[Screenshot of Restore DB cluster page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-pitr-db-1.png)

1.  Choose **Latest restorable time**\. Or choose **Custom** and specify a date and time that is earlier than the latest restorable time\. 

1.  For **Instance specifications**, keep Aurora MySQL selected for the database engine\. 

1.  For **DB cluster identifier**, enter the name for your newly restored DB cluster\. 

1.  In the **Capacity settings** section, choose the minimum and maximum values that you want for your restored Aurora Serverless v2 \(preview\) DB cluster\.   
![\[Screenshot of Instance specifications page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-pitr-db-2.png)

1.  In the **Connectivity** section, accept the defaults\. 

1.  For **Additional configuration**, choose the encryption key that you used for the DB cluster you're restoring\. Choose the default key unless you used your own key when creating the DB cluster\. 

1.  When you complete the settings on the page, choose **Restore DB Cluster**\. 