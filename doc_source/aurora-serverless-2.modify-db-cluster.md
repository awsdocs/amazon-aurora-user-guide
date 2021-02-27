# Modifying an Aurora Serverless v2 \(preview\) DB cluster<a name="aurora-serverless-2.modify-db-cluster"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. All resources and data will be deleted when the preview ends\.  | 

You can change configuration settings for your Aurora Serverless v2 \(preview\) DB cluster at any time, such as to do the following: 
+ Change your Aurora Serverless v2 \(preview\) DB cluster name\. 
+ Enable \(or disable\) deletion protection for your Aurora Serverless v2 \(preview\) DB cluster\. 
+ Modify your Aurora Serverless v2 \(preview\) DB cluster's capacity settings\. 
+ Modify **Additional configuration** settings, such as choosing a custom DB cluster parameter group\. 

The only configuration option you can't change for Aurora Serverless v2 \(preview\) DB cluster is its virtual private cloud \(VPC\) that you chose when you created it\. 

Use the following procedure to modify your Aurora Serverless v2 \(preview\) DB cluster's configuration by using the console\. 

**To modify your Aurora Serverless v2 \(preview\) DB cluster's configuration**

1. Sign in to the preview using the AWS Management Console and open the Amazon RDS console\.

1. In the navigation pane, choose **DB clusters**\.

1. Choose your Aurora Serverless v2 \(preview\) DB cluster from the list\.  
![\[Screenshot of modifying DB cluster capacity, choosing the DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-modify-capacity-1.png)

The configuration settings for your Aurora Serverless v2 \(preview\) DB cluster appear, and you can change its name, password, credentials, and other settings\.

## Modifying Aurora Serverless v2 \(preview\) DB cluster capacity<a name="aurora-serverless-2.modify-db-cluster.capacity"></a>

Currently, you can modify the capacity of your Aurora Serverless v2 \(preview\) DB clusters with the console only\.

**To modify the capacity of your Aurora Serverless v2 \(preview\) DB cluster**

1. Sign in to the preview using the AWS Management Console and open the Amazon RDS console\. 

1. In the navigation pane, choose **DB Clusters**\.

1. Choose your Aurora Serverless v2 \(preview\) DB cluster from the list, and then choose **Modify** to open its configuration\.  
![\[Screenshot of modifying DB cluster capacity, changing capacity\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-modify-capacity-2.png)

1. For **Capacity settings**, change the minimum number of ACUs or the maximum number of ACUs that you now want for your Aurora Serverless v2 \(preview\) DB cluster\.

1. Choose **Continue**\. The **Summary of modifications** appears\.  
![\[Screenshot of modifying DB cluster capacity, summary of changes\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-modify-capacity-3.png)

1. Choose **Modify cluster** to accept the summary of modifications\. You can also choose **Back** to modify your changes or **Cancel** to discard your changes\.

To learn more about ACUs and scaling for Aurora Serverless v2 \(preview\), see [Instant autoscaling](aurora-serverless-2.how-it-works.md#aurora-serverless-2.how-it-works.autoscaling)\.

## Modifying your DB cluster to use a custom DB cluster parameter group<a name="aurora-serverless-2.modify-db-cluster.custom-db-cluster-parameters"></a>

To use a custom DB cluster parameter group after your Aurora Serverless v2 \(preview\) is running, modify your existing Aurora Serverless v2 \(preview\) DB cluster\. 

Before you can use the following procedure, your custom DB cluster parameter group must exist\. To learn how to create a custom DB cluster parameter group, see [Parameter groups and Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\. 

**To modify your Aurora Serverless v2 \(preview\) DB cluster to use a custom DB cluster parameter group**

1. Sign in to the preview using the AWS Management Console and open the Amazon RDS console\. 

1. In the navigation pane, choose **DB Clusters**\.

1. Choose your Aurora Serverless v2 \(preview\) DB cluster from the list, and then choose **Modify**\.

1. Under **Additional configuration**, choose your custom DB cluster parameter group\.  
![\[Using a custom DB cluster parameter group\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-custom-parameter-for-logging.png)

1. Choose **Continue**\. The **Summary of modifications** page appears\.

1. Choose **Modify cluster** to accept the summary of modifications\. Or choose **Back** to modify your changes or **Cancel** to discard your changes\.

To learn more about creating custom DB cluster parameter groups, see [Parameter groups and Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\.