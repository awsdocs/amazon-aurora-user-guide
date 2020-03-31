# Modifying an Aurora Serverless DB Cluster<a name="aurora-serverless.modifying"></a>

After you configure an Aurora Serverless DB cluster, you can modify its scaling configuration with the AWS Management Console, the AWS CLI, or the RDS API\.

You can set the minimum and maximum capacity for the DB cluster\. Each capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless automatically creates scaling rules for thresholds for CPU utilization, connections, and available memory\. You can also set whether Aurora Serverless pauses the database when there's no activity and then resumes when activity begins again\.

You can set the following specific values:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+ **Timeout action** – The action to take when a capacity modification times out because it can't find a scaling point\. Aurora can force the capacity change to set the capacity to the specified value as soon as possible\. Or, it can roll back the capacity change to cancel it\. For more information, see [Timeout Action for Capacity Changes](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

## Console<a name="aurora-serverless.modifying.console"></a>

You can modify the scaling configuration of an Aurora DB cluster with the AWS Management Console\.

**To modify an Aurora Serverless DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless DB cluster that you want to modify\.

1. For **Actions**, choose **Modify cluster**\.

1. In the **Capacity settings** section, modify the scaling configuration\.  
![\[Setting minimum and maximum capacity for an Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

1. Choose **Continue**\.

1. Choose **Modify cluster**\.

   The change is applied immediately\.

## AWS CLI<a name="aurora-serverless.modifying.cli"></a>

To modify the scaling configuration of an Aurora Serverless DB cluster using the AWS CLI, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\. Specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

In this example, you modify the scaling configuration of an Aurora Serverless DB cluster named *sample\-cluster*\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster --db-cluster-identifier sample-cluster \
--scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=500,TimeoutAction='ForceApplyCapacityChange',AutoPause=true
```

For Windows:

```
aws rds modify-db-cluster --db-cluster-identifier sample-cluster ^
--scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=500,TimeoutAction='ForceApplyCapacityChange',AutoPause=true
```

## RDS API<a name="aurora-serverless.modifying.api"></a>

You can modify the scaling configuration of an Aurora DB cluster with the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\. Specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.