# Using Amazon Aurora Auto Scaling with Aurora Replicas<a name="Aurora.Integrating.AutoScaling"></a>

To meet your connectivity and workload requirements, Aurora Auto Scaling dynamically adjusts the number of Aurora Replicas provisioned for an Aurora DB cluster\. Aurora Auto Scaling is available for both Aurora MySQL and Aurora PostgreSQL\. Aurora Auto Scaling enables your Aurora DB cluster to handle sudden increases in connectivity or workload\. When the connectivity or workload decreases, Aurora Auto Scaling removes unnecessary Aurora Replicas so that you don't pay for unused provisioned DB instances\.

You define and apply a scaling policy to an Aurora DB cluster\. The *scaling policy* defines the minimum and maximum number of Aurora Replicas that Aurora Auto Scaling can manage\. Based on the policy, Aurora Auto Scaling adjusts the number of Aurora Replicas up or down in response to actual workloads, determined by using Amazon CloudWatch metrics and target values\.

You can use the AWS Management Console to apply a scaling policy based on a predefined metric\. Alternatively, you can use either the AWS CLI or Aurora Auto Scaling API to apply a scaling policy based on a predefined or custom metric\.

**Topics**
+ [Before you begin](#Aurora.Integrating.AutoScaling.BYB)
+ [Aurora Auto Scaling policies](#Aurora.Integrating.AutoScaling.Concepts)
+ [Adding a scaling policy to an Aurora DB cluster](#Aurora.Integrating.AutoScaling.Add)
+ [Editing a scaling policy](#Aurora.Integrating.AutoScaling.Edit)
+ [Deleting a scaling policy](#Aurora.Integrating.AutoScaling.Delete)
+ [DB instance IDs and tagging](#Aurora.Integrating.AutoScaling.Concepts.Tagging)
+ [Aurora Auto Scaling and Performance Insights](#aurora-auto-scaling-pi)

## Before you begin<a name="Aurora.Integrating.AutoScaling.BYB"></a>

Before you can use Aurora Auto Scaling with an Aurora DB cluster, you must first create an Aurora DB cluster with a primary DB instance\. For more information about creating an Aurora DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

Aurora Auto Scaling only scales a DB cluster if the DB cluster is in the available state\.

When Aurora Auto Scaling adds a new Aurora Replica, the new Aurora Replica is the same DB instance class as the one used by the primary instance\. For more information about DB instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. Also, the promotion tier for new Aurora Replicas is set to the last priority, which is 15 by default\. This means that during a failover, a replica with a better priority, such as one created manually, would be promoted first\. For more information, see [Fault tolerance for an Aurora DB cluster](Concepts.AuroraHighAvailability.md#Aurora.Managing.FaultTolerance)\.

Aurora Auto Scaling only removes Aurora Replicas that it created\.

To benefit from Aurora Auto Scaling, your applications must support connections to new Aurora Replicas\. To do so, we recommend using the Aurora reader endpoint\. For Aurora MySQL you can use a driver such as the AWS JDBC Driver for MySQL\. For more information, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.

**Note**  
Aurora global databases currently don't support Aurora Auto Scaling for secondary DB clusters\.

## Aurora Auto Scaling policies<a name="Aurora.Integrating.AutoScaling.Concepts"></a>

Aurora Auto Scaling uses a scaling policy to adjust the number of Aurora Replicas in an Aurora DB cluster\. Aurora Auto Scaling has the following components:
+ A service\-linked role
+ A target metric
+ Minimum and maximum capacity
+ A cooldown period

**Topics**
+ [Service linked role](#Aurora.Integrating.AutoScaling.Concepts.SLR)
+ [Target metric](#Aurora.Integrating.AutoScaling.Concepts.TargetMetric)
+ [Minimum and maximum capacity](#Aurora.Integrating.AutoScaling.Concepts.Capacity)
+ [Cooldown period](#Aurora.Integrating.AutoScaling.Concepts.Cooldown)
+ [Enable or disable scale\-in activities](#Aurora.Integrating.AutoScaling.Concepts.ScaleIn)

### Service linked role<a name="Aurora.Integrating.AutoScaling.Concepts.SLR"></a>

Aurora Auto Scaling uses the `AWSServiceRoleForApplicationAutoScaling_RDSCluster` service\-linked role\. For more information, see [Service\-linked roles for Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html) in the *Application Auto Scaling User Guide*\.

### Target metric<a name="Aurora.Integrating.AutoScaling.Concepts.TargetMetric"></a>

In this type of policy, a predefined or custom metric and a target value for the metric is specified in a target\-tracking scaling policy configuration\. Aurora Auto Scaling creates and manages CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and target value\. The scaling policy adds or removes Aurora Replicas as required to keep the metric at, or close to, the specified target value\. In addition to keeping the metric close to the target value, a target\-tracking scaling policy also adjusts to fluctuations in the metric due to a changing workload\. Such a policy also minimizes rapid fluctuations in the number of available Aurora Replicas for your DB cluster\.

For example, take a scaling policy that uses the predefined average CPU utilization metric\. Such a policy can keep CPU utilization at, or close to, a specified percentage of utilization, such as 40 percent\.

**Note**  
 For each Aurora DB cluster, you can create only one Auto Scaling policy for each target metric\. 

### Minimum and maximum capacity<a name="Aurora.Integrating.AutoScaling.Concepts.Capacity"></a>

You can specify the maximum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or greater than the value specified for the minimum number of Aurora Replicas\.

You can also specify the minimum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or less than the value specified for the maximum number of Aurora Replicas\.

**Note**  
The minimum and maximum capacity are set for an Aurora DB cluster\. The specified values apply to all of the policies associated with that Aurora DB cluster\. 

### Cooldown period<a name="Aurora.Integrating.AutoScaling.Concepts.Cooldown"></a>

You can tune the responsiveness of a target\-tracking scaling policy by adding cooldown periods that affect scaling your Aurora DB cluster in and out\. A cooldown period blocks subsequent scale\-in or scale\-out requests until the period expires\. These blocks slow the deletions of Aurora Replicas in your Aurora DB cluster for scale\-in requests, and the creation of Aurora Replicas for scale\-out requests\.

You can specify the following cooldown periods:
+ A scale\-in activity reduces the number of Aurora Replicas in your Aurora DB cluster\. A scale\-in cooldown period specifies the amount of time, in seconds, after a scale\-in activity completes before another scale\-in activity can start\.
+ A scale\-out activity increases the number of Aurora Replicas in your Aurora DB cluster\. A scale\-out cooldown period specifies the amount of time, in seconds, after a scale\-out activity completes before another scale\-out activity can start\.
**Note**  
A scale\-out cooldown period is ignored if a subsequent scale\-out request is for a larger number of Aurora Replicas than the first request\.

If you don't set the scale\-in or scale\-out cooldown period, the default for each is 300 seconds\.

### Enable or disable scale\-in activities<a name="Aurora.Integrating.AutoScaling.Concepts.ScaleIn"></a>

You can enable or disable scale\-in activities for a policy\. Enabling scale\-in activities allows the scaling policy to delete Aurora Replicas\. When scale\-in activities are enabled, the scale\-in cooldown period in the scaling policy applies to scale\-in activities\. Disabling scale\-in activities prevents the scaling policy from deleting Aurora Replicas\.

**Note**  
Scale\-out activities are always enabled so that the scaling policy can create Aurora Replicas as needed\.

## Adding a scaling policy to an Aurora DB cluster<a name="Aurora.Integrating.AutoScaling.Add"></a>

You can add a scaling policy using the AWS Management Console, the AWS CLI, or the Application Auto Scaling API\.

**Note**  
For an example that adds a scaling policy using AWS CloudFormation, see [Declaring a scaling policy for an Aurora DB cluster](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/quickref-autoscaling.html#w2ab1c19c22c15c21c11) in the *AWS CloudFormation User Guide\.*

### Console<a name="Aurora.Integrating.AutoScaling.AddConsole"></a>

You can add a scaling policy to an Aurora DB cluster by using the AWS Management Console\.

**To add an auto scaling policy to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the Aurora DB cluster that you want to add a policy for\.

1. Choose the **Logs & events** tab\.

1. In the **Auto scaling policies** section, choose **Add**\.

   The **Add Auto Scaling policy** dialog box appears\.

1. For **Policy Name**, type the policy name\.

1. For the target metric, choose one of the following:
   + **Average CPU utilization of Aurora Replicas** to create a policy based on the average CPU utilization\.
   + **Average connections of Aurora Replicas** to create a policy based on the average number of connections to Aurora Replicas\.

1. For the target value, type one of the following:
   + If you chose **Average CPU utilization of Aurora Replicas** in the previous step, type the percentage of CPU utilization that you want to maintain on Aurora Replicas\.
   + If you chose **Average connections of Aurora Replicas** in the previous step, type the number of connections that you want to maintain\.

   Aurora Replicas are added or removed to keep the metric close to the specified value\.

1. \(Optional\) Expand **Additional Configuration** to create a scale\-in or scale\-out cooldown period\.

1. For **Minimum capacity**, type the minimum number of Aurora Replicas that the Aurora Auto Scaling policy is required to maintain\.

1. For **Maximum capacity**, type the maximum number of Aurora Replicas the Aurora Auto Scaling policy is required to maintain\.

1. Choose **Add policy**\.

The following dialog box creates an Auto Scaling policy based an average CPU utilization of 40 percent\. The policy specifies a minimum of 5 Aurora Replicas and a maximum of 15 Aurora Replicas\.

![\[Creating an auto scaling policy based on average CPU utilization\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-autoscaling-cpu.png)

The following dialog box creates an auto scaling policy based an average number of connections of 100\. The policy specifies a minimum of two Aurora Replicas and a maximum of eight Aurora Replicas\.

![\[Creating an Auto Scaling policy based on average connections\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-autoscaling-connections.png)

### AWS CLI or Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.AddCode"></a>

You can apply a scaling policy based on either a predefined or custom metric\. To do so, you can use the AWS CLI or the Application Auto Scaling API\. The first step is to register your Aurora DB cluster with Application Auto Scaling\.

#### Registering an Aurora DB cluster<a name="Aurora.Integrating.AutoScaling.AddCode.Register"></a>

Before you can use Aurora Auto Scaling with an Aurora DB cluster, you register your Aurora DB cluster with Application Auto Scaling\. You do so to define the scaling dimension and limits to be applied to that cluster\. Application Auto Scaling dynamically scales the Aurora DB cluster along the `rds:cluster:ReadReplicaCount` scalable dimension, which represents the number of Aurora Replicas\. 

To register your Aurora DB cluster, you can use either the AWS CLI or the Application Auto Scaling API\. 

##### AWS CLI<a name="Aurora.Integrating.AutoScaling.AddCode.Register.CLI"></a>

To register your Aurora DB cluster, use the [https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/register-scalable-target.html](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/register-scalable-target.html) AWS CLI command with the following parameters:
+ `--service-namespace` – Set this value to `rds`\.
+ `--resource-id` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.
+ `--scalable-dimension` – Set this value to `rds:cluster:ReadReplicaCount`\.
+ `--min-capacity` – The minimum number of reader DB instances to be managed by Application Auto Scaling\. For information about the relationship between `--min-capacity`, `--max-capacity`, and the number of DB instances in your cluster, see [Minimum and maximum capacity](#Aurora.Integrating.AutoScaling.Concepts.Capacity)\.
+ `--max-capacity` – The maximum number of reader DB instances to be managed by Application Auto Scaling\. For information about the relationship between `--min-capacity`, `--max-capacity`, and the number of DB instances in your cluster, see [Minimum and maximum capacity](#Aurora.Integrating.AutoScaling.Concepts.Capacity)\.

**Example**  
In the following example, you register an Aurora DB cluster named `myscalablecluster`\. The registration indicates that the DB cluster should be dynamically scaled to have from one to eight Aurora Replicas\.  
For Linux, macOS, or Unix:  

```
aws application-autoscaling register-scalable-target \
    --service-namespace rds \
    --resource-id cluster:myscalablecluster \
    --scalable-dimension rds:cluster:ReadReplicaCount \
    --min-capacity 1 \
    --max-capacity 8 \
```
For Windows:  

```
aws application-autoscaling register-scalable-target ^
    --service-namespace rds ^
    --resource-id cluster:myscalablecluster ^
    --scalable-dimension rds:cluster:ReadReplicaCount ^
    --min-capacity 1 ^
    --max-capacity 8 ^
```

##### Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.AddCode.Register.API"></a>

To register your Aurora DB cluster with Application Auto Scaling, use the [https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_RegisterScalableTarget.html](https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_RegisterScalableTarget.html) Application Auto Scaling API operation with the following parameters:
+ `ServiceNamespace` – Set this value to `rds`\.
+ `ResourceID` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.
+ `ScalableDimension` – Set this value to `rds:cluster:ReadReplicaCount`\.
+ `MinCapacity` – The minimum number of reader DB instances to be managed by Application Auto Scaling\. For information about the relationship between `MinCapacity`, `MaxCapacity`, and the number of DB instances in your cluster, see [Minimum and maximum capacity](#Aurora.Integrating.AutoScaling.Concepts.Capacity)\.
+ `MaxCapacity` – The maximum number of reader DB instances to be managed by Application Auto Scaling\. For information about the relationship between `MinCapacity`, `MaxCapacity`, and the number of DB instances in your cluster, see [Minimum and maximum capacity](#Aurora.Integrating.AutoScaling.Concepts.Capacity)\.

**Example**  
In the following example, you register an Aurora DB cluster named `myscalablecluster` with the Application Auto Scaling API\. This registration indicates that the DB cluster should be dynamically scaled to have from one to eight Aurora Replicas\.  

```
POST / HTTP/1.1
Host: autoscaling.us-east-2.amazonaws.com
Accept-Encoding: identity
Content-Length: 219
X-Amz-Target: AnyScaleFrontendService.RegisterScalableTarget
X-Amz-Date: 20160506T182145Z
User-Agent: aws-cli/1.10.23 Python/2.7.11 Darwin/15.4.0 botocore/1.4.8
Content-Type: application/x-amz-json-1.1
Authorization: AUTHPARAMS

{
    "ServiceNamespace": "rds",
    "ResourceId": "cluster:myscalablecluster",
    "ScalableDimension": "rds:cluster:ReadReplicaCount",
    "MinCapacity": 1,
    "MaxCapacity": 8
}
```

#### Defining a scaling policy for an Aurora DB cluster<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy"></a>

A target\-tracking scaling policy configuration is represented by a JSON block that the metrics and target values are defined in\. You can save a scaling policy configuration as a JSON block in a text file\. You use that text file when invoking the AWS CLI or the Application Auto Scaling API\. For more information about policy configuration syntax, see [https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html](https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html) in the *Application Auto Scaling API Reference*\.

 The following options are available for defining a target\-tracking scaling policy configuration\.

**Topics**
+ [Using a predefined metric](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Predefined)
+ [Using a custom metric](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Custom)
+ [Using cooldown periods](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Cooldown)
+ [Disabling scale\-in activity](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.ScaleIn)

##### Using a predefined metric<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Predefined"></a>

By using predefined metrics, you can quickly define a target\-tracking scaling policy for an Aurora DB cluster that works well with both target tracking and dynamic scaling in Aurora Auto Scaling\. 

Currently, Aurora supports the following predefined metrics in Aurora Auto Scaling:
+ **RDSReaderAverageCPUUtilization** – The average value of the `CPUUtilization` metric in CloudWatch across all Aurora Replicas in the Aurora DB cluster\.
+ **RDSReaderAverageDatabaseConnections** – The average value of the `DatabaseConnections` metric in CloudWatch across all Aurora Replicas in the Aurora DB cluster\.

For more information about the `CPUUtilization` and `DatabaseConnections` metrics, see  [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md)\.

To use a predefined metric in your scaling policy, you create a target tracking configuration for your scaling policy\. This configuration must include a `PredefinedMetricSpecification` for the predefined metric and a `TargetValue` for the target value of that metric\.

**Example**  
The following example describes a typical policy configuration for target\-tracking scaling for an Aurora DB cluster\. In this configuration, the `RDSReaderAverageCPUUtilization` predefined metric is used to adjust the Aurora DB cluster based on an average CPU utilization of 40 percent across all Aurora Replicas\.  

```
{
    "TargetValue": 40.0,
    "PredefinedMetricSpecification":
    {
        "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
    }
}
```

##### Using a custom metric<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Custom"></a>

By using custom metrics, you can define a target\-tracking scaling policy that meets your custom requirements\. You can define a custom metric based on any Aurora metric that changes in proportion to scaling\. 

Not all Aurora metrics work for target tracking\. The metric must be a valid utilization metric and describe how busy an instance is\. The value of the metric must increase or decrease in proportion to the number of Aurora Replicas in the Aurora DB cluster\. This proportional increase or decrease is necessary to use the metric data to proportionally scale out or in the number of Aurora Replicas\.

**Example**  
The following example describes a target\-tracking configuration for a scaling policy\. In this configuration, a custom metric adjusts an Aurora DB cluster based on an average CPU utilization of 50 percent across all Aurora Replicas in an Aurora DB cluster named `my-db-cluster`\.  

```
{
    "TargetValue": 50,
    "CustomizedMetricSpecification":
    {
        "MetricName": "CPUUtilization",
        "Namespace": "AWS/RDS",
        "Dimensions": [
            {"Name": "DBClusterIdentifier","Value": "my-db-cluster"},
            {"Name": "Role","Value": "READER"}
        ],
        "Statistic": "Average",
        "Unit": "Percent"
    }
}
```

##### Using cooldown periods<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Cooldown"></a>

You can specify a value, in seconds, for `ScaleOutCooldown` to add a cooldown period for scaling out your Aurora DB cluster\. Similarly, you can add a value, in seconds, for `ScaleInCooldown` to add a cooldown period for scaling in your Aurora DB cluster\. For more information about `ScaleInCooldown` and `ScaleOutCooldown`, see [https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html](https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html) in the *Application Auto Scaling API Reference*\.

**Example**  
The following example describes a target\-tracking configuration for a scaling policy\. In this configuration, the `RDSReaderAverageCPUUtilization` predefined metric is used to adjust an Aurora DB cluster based on an average CPU utilization of 40 percent across all Aurora Replicas in that Aurora DB cluster\. The configuration provides a scale\-in cooldown period of 10 minutes and a scale\-out cooldown period of 5 minutes\.  

```
{
    "TargetValue": 40.0,
    "PredefinedMetricSpecification":
    {
        "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
    },
    "ScaleInCooldown": 600,
    "ScaleOutCooldown": 300
}
```

##### Disabling scale\-in activity<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.ScaleIn"></a>

You can prevent the target\-tracking scaling policy configuration from scaling in your Aurora DB cluster by disabling scale\-in activity\. Disabling scale\-in activity prevents the scaling policy from deleting Aurora Replicas, while still allowing the scaling policy to create them as needed\.

You can specify a Boolean value for `DisableScaleIn` to enable or disable scale in activity for your Aurora DB cluster\. For more information about `DisableScaleIn`, see [https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html](https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html) in the *Application Auto Scaling API Reference*\. 

**Example**  
The following example describes a target\-tracking configuration for a scaling policy\. In this configuration, the `RDSReaderAverageCPUUtilization` predefined metric adjusts an Aurora DB cluster based on an average CPU utilization of 40 percent across all Aurora Replicas in that Aurora DB cluster\. The configuration disables scale\-in activity for the scaling policy\.  

```
{
    "TargetValue": 40.0,
    "PredefinedMetricSpecification":
    {
        "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
    },
    "DisableScaleIn": true
}
```

#### Applying a scaling policy to an Aurora DB cluster<a name="Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy"></a>

After registering your Aurora DB cluster with Application Auto Scaling and defining a scaling policy, you apply the scaling policy to the registered Aurora DB cluster\. To apply a scaling policy to an Aurora DB cluster, you can use the AWS CLI or the Application Auto Scaling API\. 

##### AWS CLI<a name="Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy.CLI"></a>

To apply a scaling policy to your Aurora DB cluster, use the [https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scaling-policy.html](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scaling-policy.html) AWS CLI command with the following parameters:
+ `--policy-name` – The name of the scaling policy\.
+ `--policy-type` – Set this value to `TargetTrackingScaling`\.
+ `--resource-id` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.
+ `--service-namespace` – Set this value to `rds`\.
+ `--scalable-dimension` – Set this value to `rds:cluster:ReadReplicaCount`\.
+ `--target-tracking-scaling-policy-configuration` – The target\-tracking scaling policy configuration to use for the Aurora DB cluster\.

**Example**  
In the following example, you apply a target\-tracking scaling policy named `myscalablepolicy` to an Aurora DB cluster named `myscalablecluster` with Application Auto Scaling\. To do so, you use a policy configuration saved in a file named `config.json`\.  
For Linux, macOS, or Unix:  

```
aws application-autoscaling put-scaling-policy \
    --policy-name myscalablepolicy \
    --policy-type TargetTrackingScaling \
    --resource-id cluster:myscalablecluster \
    --service-namespace rds \
    --scalable-dimension rds:cluster:ReadReplicaCount \
    --target-tracking-scaling-policy-configuration file://config.json
```
For Windows:  

```
aws application-autoscaling put-scaling-policy ^
    --policy-name myscalablepolicy ^
    --policy-type TargetTrackingScaling ^
    --resource-id cluster:myscalablecluster ^
    --service-namespace rds ^
    --scalable-dimension rds:cluster:ReadReplicaCount ^
    --target-tracking-scaling-policy-configuration file://config.json
```

##### Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy.API"></a>

To apply a scaling policy to your Aurora DB cluster with the Application Auto Scaling API, use the [https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_PutScalingPolicy.html](https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_PutScalingPolicy.html) Application Auto Scaling API operation with the following parameters:
+ `PolicyName` – The name of the scaling policy\.
+ `ServiceNamespace` – Set this value to `rds`\.
+ `ResourceID` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.
+ `ScalableDimension` – Set this value to `rds:cluster:ReadReplicaCount`\.
+ `PolicyType` – Set this value to `TargetTrackingScaling`\.
+ `TargetTrackingScalingPolicyConfiguration` – The target\-tracking scaling policy configuration to use for the Aurora DB cluster\.

**Example**  
In the following example, you apply a target\-tracking scaling policy named `myscalablepolicy` to an Aurora DB cluster named `myscalablecluster` with Application Auto Scaling\. You use a policy configuration based on the `RDSReaderAverageCPUUtilization` predefined metric\.  

```
POST / HTTP/1.1
Host: autoscaling.us-east-2.amazonaws.com
Accept-Encoding: identity
Content-Length: 219
X-Amz-Target: AnyScaleFrontendService.PutScalingPolicy
X-Amz-Date: 20160506T182145Z
User-Agent: aws-cli/1.10.23 Python/2.7.11 Darwin/15.4.0 botocore/1.4.8
Content-Type: application/x-amz-json-1.1
Authorization: AUTHPARAMS

{
    "PolicyName": "myscalablepolicy",
    "ServiceNamespace": "rds",
    "ResourceId": "cluster:myscalablecluster",
    "ScalableDimension": "rds:cluster:ReadReplicaCount",
    "PolicyType": "TargetTrackingScaling",
    "TargetTrackingScalingPolicyConfiguration": {
        "TargetValue": 40.0,
        "PredefinedMetricSpecification":
        {
            "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
        }
    }
}
```

## Editing a scaling policy<a name="Aurora.Integrating.AutoScaling.Edit"></a>

You can edit a scaling policy using the AWS Management Console, the AWS CLI, or the Application Auto Scaling API\.

### Console<a name="Aurora.Integrating.AutoScaling.EditConsole"></a>

You can edit a scaling policy by using the AWS Management Console\.

**To edit an auto scaling policy for an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the Aurora DB cluster whose auto scaling policy you want to edit\.

1. Choose the **Logs & events** tab\.

1. In the **Auto scaling policies** section, choose the auto scaling policy, and then choose **Edit**\.

1. Make changes to the policy\.

1. Choose **Save**\.

The following is a sample **Edit Auto Scaling policy** dialog box\.

![\[Editing an auto scaling policy based on average CPU utilization\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-autoscaling-edit-cpu.png)

### AWS CLI or Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.EditCode"></a>

You can use the AWS CLI or the Application Auto Scaling API to edit a scaling policy in the same way that you apply a scaling policy:
+ When using the AWS CLI, specify the name of the policy you want to edit in the `--policy-name` parameter\. Specify new values for the parameters you want to change\.
+ When using the Application Auto Scaling API, specify the name of the policy you want to edit in the `PolicyName` parameter\. Specify new values for the parameters you want to change\.

For more information, see [Applying a scaling policy to an Aurora DB cluster](#Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy)\.

## Deleting a scaling policy<a name="Aurora.Integrating.AutoScaling.Delete"></a>

You can delete a scaling policy using the AWS Management Console, the AWS CLI, or the Application Auto Scaling API\.

### Console<a name="Aurora.Integrating.AutoScaling.Delete.Console"></a>

You can delete a scaling policy by using the AWS Management Console\.

**To delete an auto scaling policy for an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the Aurora DB cluster whose auto scaling policy you want to delete\.

1. Choose the **Logs & events** tab\.

1. In the **Auto scaling policies** section, choose the auto scaling policy, and then choose **Delete**\.

### AWS CLI<a name="Aurora.Integrating.AutoScaling.Delete.CLI"></a>

To delete a scaling policy from your Aurora DB cluster, use the [https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/delete-scaling-policy.html](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/delete-scaling-policy.html) AWS CLI command with the following parameters:
+ `--policy-name` – The name of the scaling policy\.
+ `--resource-id` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.
+ `--service-namespace` – Set this value to `rds`\.
+ `--scalable-dimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

**Example**  
In the following example, you delete a target\-tracking scaling policy named `myscalablepolicy` from an Aurora DB cluster named `myscalablecluster`\.  
For Linux, macOS, or Unix:  

```
aws application-autoscaling delete-scaling-policy \
    --policy-name myscalablepolicy \
    --resource-id cluster:myscalablecluster \
    --service-namespace rds \
    --scalable-dimension rds:cluster:ReadReplicaCount \
```
For Windows:  

```
aws application-autoscaling delete-scaling-policy ^
    --policy-name myscalablepolicy ^
    --resource-id cluster:myscalablecluster ^
    --service-namespace rds ^
    --scalable-dimension rds:cluster:ReadReplicaCount ^
```

### Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.Delete.API"></a>

To delete a scaling policy from your Aurora DB cluster, use the [https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_DeleteScalingPolicy.html](https://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_DeleteScalingPolicy.html) the Application Auto Scaling API operation with the following parameters:
+ `PolicyName` – The name of the scaling policy\.
+ `ServiceNamespace` – Set this value to `rds`\.
+ `ResourceID` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.
+ `ScalableDimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

**Example**  
In the following example, you delete a target\-tracking scaling policy named `myscalablepolicy` from an Aurora DB cluster named `myscalablecluster` with the Application Auto Scaling API\.  

```
POST / HTTP/1.1
Host: autoscaling.us-east-2.amazonaws.com
Accept-Encoding: identity
Content-Length: 219
X-Amz-Target: AnyScaleFrontendService.DeleteScalingPolicy
X-Amz-Date: 20160506T182145Z
User-Agent: aws-cli/1.10.23 Python/2.7.11 Darwin/15.4.0 botocore/1.4.8
Content-Type: application/x-amz-json-1.1
Authorization: AUTHPARAMS

{
    "PolicyName": "myscalablepolicy",
    "ServiceNamespace": "rds",
    "ResourceId": "cluster:myscalablecluster",
    "ScalableDimension": "rds:cluster:ReadReplicaCount"
}
```

## DB instance IDs and tagging<a name="Aurora.Integrating.AutoScaling.Concepts.Tagging"></a>

When a replica is added by Aurora Auto Scaling, its DB instance ID is prefixed by `application-autoscaling-`, for example, `application-autoscaling-61aabbcc-4e2f-4c65-b620-ab7421abc123`\.

The following tag is automatically added to the DB instance\. You can view it on the **Tags** tab of the DB instance detail page\.


| Tag | Value | 
| --- | --- | 
| application\-autoscaling:resourceId | cluster:mynewcluster\-cluster | 

For more information on Amazon RDS resource tags, see [Tagging Amazon RDS resources](USER_Tagging.md)\.

## Aurora Auto Scaling and Performance Insights<a name="aurora-auto-scaling-pi"></a>

You can use Performance Insights to monitor replicas that have been added by Aurora Auto Scaling, the same as with any Aurora reader DB instance\.

You can't turn on Performance Insights for an Aurora DB cluster\. You can manually turn on Performance Insights for each DB instance in the DB cluster\.

When you turn on Performance Insights for the writer DB instance in your Aurora DB cluster, Performance Insights isn't turned on automatically for reader DB instances\. You have to turn on Performance Insights manually for the existing reader DB instances and new replicas added by Aurora Auto Scaling\.

For more information on using Performance Insights to monitor Aurora DB clusters, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\.