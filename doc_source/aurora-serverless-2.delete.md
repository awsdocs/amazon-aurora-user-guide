# Deleting an Aurora Serverless v2 \(preview\) DB cluster<a name="aurora-serverless-2.delete"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. All resources and data will be deleted when the preview ends\.  | 

When you create your Aurora Serverless v2 \(preview\) DB clusters, you can choose to disable deletion protection or keep it as is\. If the Aurora Serverless v2 \(preview\) DB cluster that you want to delete was created with deletion protection, make sure to modify your DB cluster to remove deletion protection or you can't delete it\. To learn how do this, see [Modifying an Aurora Serverless v2 \(preview\) DB cluster](aurora-serverless-2.modify-db-cluster.md)\. 

Currently, you can delete an Amazon Aurora Serverless v2 \(preview\) DB cluster with the console only\.

**To delete an Aurora Serverless v2 \(preview\) DB cluster**

1. Sign in to the preview using the AWS Management Console and open the Amazon RDS console\. 

1. In the **Resources** section, choose **DB Clusters**\. 

1. Choose the Aurora Serverless v2 \(preview\) DB cluster that you want to delete\.  
![\[Screenshot of choosing DB cluster to delete\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-delete-db-1.png)

1. For **Actions**, choose **Delete**\. You're prompted to confirm that you want to delete your Aurora Serverless v2 \(preview\) DB cluster\. 

1. We recommend that you keep the preselected options:
   + **Yes** for **Create final snapshot?**
   + Your Aurora Serverless v2 \(preview\) DB cluster name plus `-final-snapshot` for **Final snapshot name**\. However, you can change the name for your final snapshot in this field\.  
![\[Screenshot of deleting Aurora Serverless v2 (preview) database cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-delete-db-2.png)

   If you choose **No** for **Create final snapshot?** you can't restore your DB cluster using snapshots or point\-in\-time recovery\.  
![\[Screenshot of deleting Aurora Serverless v2 (preview) database cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-delete-db-3.png)

1. Choose **Delete DB cluster**\. 

Aurora Serverless v2 \(preview\) deletes your DB cluster\. 