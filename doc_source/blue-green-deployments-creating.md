# Creating a blue/green deployment<a name="blue-green-deployments-creating"></a>

When you create a blue/green deployment, you specify the DB cluster to copy in the deployment\. The DB cluster you choose is the production DB cluster, and it becomes the DB cluster in the blue environment\. RDS copies the blue environment's topology to a staging area, along with its configured features\. The DB cluster is copied to the green environment, and RDS configures replication from the DB cluster in the blue environment to the DB cluster in the green environment\. RDS also copies all of the DB instances in the DB cluster\.

**Topics**
+ [Preparing an Aurora DB cluster for a blue/green deployment](#blue-green-deployments-creating-preparing-aurora)
+ [Making changes in the green environment](#blue-green-deployments-creating-changes)
+ [Creating the blue/green deployment](#blue-green-deployments-creating-create)

## Preparing an Aurora DB cluster for a blue/green deployment<a name="blue-green-deployments-creating-preparing-aurora"></a>

Before you create a blue/green deployment for an Aurora MySQL DB cluster, the DB cluster must be associated with a custom DB cluster parameter group with binary logging \(`binlog_format`\) turned on\. Binary logging is required for replication from the blue environment to the green environment\. While any binlog format works, we recommend `ROW` to reduce the risk of replication inconsistencies\. For information about creating a custom DB cluster parameter group and setting parameters, see [Working with DB cluster parameter groups](USER_WorkingWithDBClusterParamGroups.md)\.

You can modify the DB cluster to associate it with a custom DB cluster parameter group\. For more information, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

If you change the `binlog_format` parameter setting for a DB cluster, reboot the writer instance of the DB cluster\. For more information, see [Rebooting a DB instance within an Aurora cluster](USER_RebootCluster.md#aurora-reboot-db-instance)\.

## Making changes in the green environment<a name="blue-green-deployments-creating-changes"></a>

You can make the following changes to the DB cluster in the green environment when you create the blue/green deployment:
+ You can specify a higher engine version if you want to test a DB engine upgrade\.
+ You can specify a DB cluster parameter group that is different from the one used by the DB cluster\. You can test how parameter changes affect the DB cluster in the green environment or specify a parameter group for a new major DB engine version in the case of an upgrade\.

  If you specify a different DB cluster parameter group, the specified parameter group is associated with the DB cluster in the green environment\. If you don't specify a different DB cluster parameter group, the DB cluster in the green environment is associated with the same parameter group as the blue DB cluster\.
+ You can specify a DB parameter group that is different from the one used by the DB instances in the blue environment\. You can test how parameter changes affect the DB instances in the green environment or specify a parameter group for a new major DB engine version in the case of an upgrade\.

  If you specify a different DB parameter group, the specified parameter group is associated with all of the DB instances in the DB cluster in the green environment\. If you don't specify a different DB parameter group, each DB instance in the green environment is associated with the parameter group of its corresponding blue DB instance\.

You can make other modifications to the DB cluster and its DB instances in the green environment after it is deployed\. For example, you might make schema changes to your database or change the DB instance class used by one or more DB instances in the green environment\.

For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

## Creating the blue/green deployment<a name="blue-green-deployments-creating-create"></a>

You can create the blue/green deployment using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="blue-green-deployments-creating-console"></a>

**To create a blue/green deployment;**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster that you want to copy to a green environment\.

1. For **Actions**, choose **Create Blue/Green Deployment**\.

   The **Create Blue/Green Deployment** page appears\.   
![\[Create blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/blue-green-deployment-create-aurora.png)

1. On the **Create Blue/Green Deployment** page, review the blue database identifiers\. Make sure they match the DB instances that you expect in the blue environment\. If they don't, choose **Cancel**\.

1. For **Blue/Green Deployment identifier**, enter a name for your blue/green deployment\.

1. \(Optional\) For **Blue/Green Deployment settings**, specify the settings for the green environment:
   + Choose a DB engine version if you want to test a DB engine version upgrade\.
   + Choose a DB cluster parameter group to associate with the DB cluster in the green environment\.
   + Choose a DB parameter group to associate with the DB instances in the green environment\.

   You can make other modifications to the databases in the green environment after it is deployed\.

1. Choose **Create Blue/Green Deployment**\.

### AWS CLI<a name="blue-green-deployments-creating-cli"></a>

To create a blue/green deployment by using the AWS CLI, use the [create\-blue\-green\-deployment](https://docs.aws.amazon.com/cli/latest/reference/rds/create-blue-green-deployment.html) command with the following options:
+ `--blue-green-deployment-name` – Specify the name of the blue/green deployment\.
+ `--source` – Specify the ARN of the DB cluster that you want to copy\.
+ `--target-engine-version` – Specify an engine version if you want to test a DB engine version upgrade in the green environment\. This option upgrades the DB cluster in the green environment to the specified DB engine version\.

  If not specified, the DB cluster in the green environment is created with the same engine version as DB cluster in the blue environment\.
+ `--target-db-cluster-parameter-group-name` – Specify a DB cluster parameter group to associate with the DB cluster in the green environment\.
+ `--target-db-parameter-group-name` – Specify a DB parameter group to associate with the DB instances in the green environment\.

**Example Create a blue/green deployment**  
For Linux, macOS, or Unix:  

```
aws rds create-blue-green-deployment \
    --blue-green-deployment-name aurora-blue-green-deployment \
    --source arn:aws:rds:us-east-2:123456789012:cluster:auroradb \
    --target-engine-version 8.0 \
    --target-db-cluster-parameter-group mydbclusterparametergroup \
    --target-db-parameter-group-name mydbparametergroup
```
For Windows:  

```
aws rds create-blue-green-deployment ^
    --blue-green-deployment-name aurora-blue-green-deployment ^
    --source arn:aws:rds:us-east-2:123456789012:cluster:auroradb ^
    --target-engine-version 8.0 ^
    --target-db-cluster-parameter-group mydbclusterparametergroup ^
    --target-db-parameter-group-name mydbparametergroup
```

### RDS API<a name="blue-green-deployments-creating-api"></a>

To create a blue/green deployment by using the Amazon RDS API, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateBlueGreenDeployment.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateBlueGreenDeployment.html) operation with the following parameters:
+ `BlueGreenDeploymentName` – Specify the name of the blue/green deployment\.
+ `Source` – Specify the ARN of the DB cluster that you want to copy to the green environment\.
+ `TargetEngineVersion` – Specify an engine version if you want to test a DB engine version upgrade in the green environment\. This option upgrades the DB cluster in the green environment to the specified DB engine version\.

  If not specified, the DB cluster in the green environment is created with the same engine version as DB cluster in the blue environment\.
+ `TargetDBClusterParameterGroupName` – Specify a DB cluster parameter group to associate with the DB cluster in the green environment\.
+ `TargetDBParameterGroupName` – Specify a DB parameter group to associate with the DB instances in the green environment\.