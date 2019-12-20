# Setting the Capacity of an Aurora Serverless DB Cluster<a name="aurora-serverless.setting-capacity"></a>

Set the capacity of an Aurora Serverless DB cluster to a specific value with the AWS Management Console, the AWS CLI, or the RDS API\.

Aurora Serverless scales seamlessly based on the workload on the DB cluster\. In some cases, the capacity might not scale fast enough to meet a sudden change in workload, such as a large number of new transactions\. In these cases, you can set the capacity explicitly\. After you set the capacity explicitly, Aurora Serverless can automatically scale the DB cluster\. It does so based on the cooldown period for scaling down\.

## Console<a name="aurora-serverless.setting-capacity.console"></a>

You can set the capacity of an Aurora DB cluster with the AWS Management Console\.

**To modify an Aurora Serverless DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless DB cluster that you want to modify\.

1. For **Actions**, choose **Set capacity**\.

1. In the **Scale database capacity** window, set the capacity, the capacity that the DB cluster should scale to\.  
![\[Setting capacity for an Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-set-capacity.png)

1. Enable or disable the option to forcibly scale capacity\. If you enable it, specify the amount of time to find a scaling point\. Aurora Serverless attempts to find a scaling point before timing out\. If the timeout is reached, Aurora Serverless performs one of the following actions:
   + If checked, Aurora Serverless will force the scaling capacity change to proceed after the timeout\. 
   + If unchecked, Aurora Serverless will not perform the scaling capacity change after the timeout\.
**Important**  
When the option is enabled, connections that prevent Aurora Serverless from finding a scaling point might be dropped\. For more information about scaling points and cooldown periods, see [Autoscaling for Aurora Serverless](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\.

1. Choose **Apply**\.

## AWS CLI<a name="aurora-serverless.setting-capacity.cli"></a>

To set the capacity of an Aurora Serverless DB cluster using the AWS CLI, run the [modify\-current\-db\-cluster\-capacity](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-current-db-cluster-capacity.html) AWS CLI command, and specify the `--capacity` option\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

In this example, you set the capacity of an Aurora Serverless DB cluster named *sample\-cluster* to *64*\.

```
aws rds modify-current-db-cluster-capacity --db-cluster-identifier sample-cluster --capacity 64
```

## RDS API<a name="aurora-serverless.setting-capacity.api"></a>

You can set the capacity of an Aurora DB cluster with the [ModifyCurrentDBClusterCapacity](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyCurrentDBClusterCapacity.html) API operation\. Specify the `Capacity` parameter\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.