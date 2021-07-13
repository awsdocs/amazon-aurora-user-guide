# Creating a snapshot of an Aurora Serverless v2 \(preview\) DB cluster<a name="aurora-serverless-2.create.snapshot"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. All resources and data will be deleted when the preview ends\.  | 

 Amazon Aurora Serverless v2 \(preview\) routinely creates snapshots of your DB cluster as backups\. Unlike automated backups, manual snapshots aren't subject to the backup retention period\. Snapshots don't expire\. For more information about snapshots in general, see [Creating a DB cluster snapshot](USER_CreateSnapshotCluster.md)\. 

 Currently, you can create an Aurora Serverless v2 \(preview\) DB cluster snapshot using the console only\. 

**To create a DB cluster snapshot**

1.  Sign in to the preview using the AWS Management Console and open the Amazon RDS console\. 

1.  In the navigation pane, choose **Databases**\. 

1.  Choose the Aurora Serverless v2 \(preview\) DB cluster use for your snapshot\. 

1.  Choose **Actions**, and then choose **Take snapshot**\. 

    The **Take DB Snapshot** window appears\. 

1.  For **Snapshot name**, enter the name of your Aurora Serverless v2 \(preview\) DB cluster\.   
![\[Create snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/DBSnapshotCluster.png)

1.  Choose **Take Snapshot**\. 