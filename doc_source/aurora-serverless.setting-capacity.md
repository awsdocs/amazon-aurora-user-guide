# Scaling Aurora Serverless v1 DB cluster capacity manually<a name="aurora-serverless.setting-capacity"></a>

Typically, Aurora Serverless v1 DB clusters scale seamlessly based on the workload\. However, capacity might not always scale fast enough to meet sudden extremes, such as an exponential increase in transactions\. In such cases you can initiate the scaling operation manually by setting a new capacity value\. After you set the capacity explicitly, Aurora Serverless v1 automatically scales the DB cluster\. It does so based on the cooldown period for scaling down\. 

You can explicitly set the capacity of an Aurora Serverless v1 DB cluster to a specific value with the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="aurora-serverless.setting-capacity.console"></a>

You can set the capacity of an Aurora DB cluster with the AWS Management Console\.

**To modify an Aurora Serverless v1 DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless v1 DB cluster that you want to modify\.

1. For **Actions**, choose **Set capacity**\.

1. In the **Scale database capacity** window, choose the following: 

   1. For the **Scale DB cluster to** drop\-down selector, choose the new capacity that you want for your DB cluster\.

   1. For the **If a seamless scaling point cannot be found\.\.\.** checkbox, choose the behavior you want for your Aurora Serverless v1 DB cluster's `TimeoutAction` setting, as follows: 
      + Uncheck this option if you want your capacity to remain unchanged if Aurora Serverless v1 can't find a scaling point before timing out\.
      + Check this option if you want to force your Aurora Serverless v1 DB cluster change its capacity even if it can't find a scaling point before timing out\. This option can result Aurora Serverless v1 dropping connections that prevent it from finding a scaling point\.

   1. In the **seconds** field, enter the amount of time you want to allow your Aurora Serverless v1 DB cluster to look for a scaling point before timing out\. You can specify anywhere from 10 seconds to 600 seconds \(10 minutes\)\. The default is five minutes \(300 seconds\)\. This following example forces the Aurora Serverless v1 DB cluster to scale down to 2 ACUs even if it can't find a scaling point within five minutes\.   
![\[Setting capacity for an Aurora Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-set-capacity.png)

1. Choose **Apply**\.

To learn more about scaling points, `TimeoutAction`, and cooldown periods, see [Autoscaling for Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\.

## AWS CLI<a name="aurora-serverless.setting-capacity.cli"></a>

To set the capacity of an Aurora Serverless v1 DB cluster using the AWS CLI, run the [modify\-current\-db\-cluster\-capacity](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-current-db-cluster-capacity.html) AWS CLI command, and specify the `--capacity` option\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

In this example, you set the capacity of an Aurora Serverless v1 DB cluster named *sample\-cluster* to *64*\.

```
aws rds modify-current-db-cluster-capacity --db-cluster-identifier sample-cluster --capacity 64
```

## RDS API<a name="aurora-serverless.setting-capacity.api"></a>

You can set the capacity of an Aurora DB cluster with the [ModifyCurrentDBClusterCapacity](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyCurrentDBClusterCapacity.html) API operation\. Specify the `Capacity` parameter\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.