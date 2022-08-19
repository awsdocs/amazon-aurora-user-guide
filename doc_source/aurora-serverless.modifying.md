# Modifying an Aurora Serverless v1 DB cluster<a name="aurora-serverless.modifying"></a>

After you configure an Aurora Serverless v1 DB cluster, you can modify certain properties with the AWS Management Console, the AWS CLI, or the RDS API\. Most of the properties you can modify are the same as for other kinds of Aurora clusters\. The properties that are most relevant for Aurora Serverless v1 are the scaling configuration of the cluster, and the major version for Aurora Serverless v1 clusters that are compatible with MySQL 5\.6 and PostgreSQL 10\.

You can set the minimum and maximum capacity for the DB cluster\. Each capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless automatically creates scaling rules for thresholds for CPU utilization, connections, and available memory\. You can also set whether Aurora Serverless pauses the database when there's no activity and then resumes when activity begins again\.

You can set the following specific values for the scaling configuration:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+  **Autoscaling timeout and action** – This section specifies how long Aurora Serverless waits to find a scaling point before timing out\. It also specifies the action to take when a capacity modification times out because it can't find a scaling point\. Aurora can force the capacity change to set the capacity to the specified value as soon as possible\. Or, it can roll back the capacity change to cancel it\. For more information, see [Timeout action for capacity changes](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

You can choose the major version for certain Aurora Serverless v1 DB clusters:
+ DB clusters compatible with MySQL 5\.6 – Choose a corresponding MySQL 5\.7–compatible version number\.
+ DB clusters compatible with PostgreSQL 10 – Choose a corresponding PostgreSQL 11–compatible version number\.

Doing so performs an in\-place upgrade of the Aurora Serverless v1 DB cluster\.

## Console<a name="aurora-serverless.modifying.console"></a>

You can modify the scaling configuration of an Aurora DB cluster with the AWS Management Console\.

**To modify an Aurora Serverless v1 DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless v1 DB cluster that you want to modify\.

1. For **Actions**, choose **Modify cluster**\.

1. For **Version**:
   + Choose an Aurora MySQL version 2 version number if you want to perform an in\-place upgrade from a MySQL 5\.6–compatible cluster to a MySQL 5\.7–compatible one\.
   + Choose an Aurora PostgreSQL version 11 version number if you want to perform an in\-place upgrade from a PostgreSQL 10–compatible cluster to a PostgreSQL 11–compatible one\.

   The following example shows an in\-place upgrade from Aurora MySQL 1\.22\.3 to 2\.08\.3\.  
![\[Upgrading an Aurora Serverless v1 DB cluster using the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/modify-serverless-v1-ams-cluster.png)

   If you perform a major version upgrade, leave all the other properties the same\. To change any of the other properties, do another **Modify** operation after the upgrade finishes\.

1. In the **Capacity settings** section, modify the scaling configuration\.

1. Choose **Continue**\.

1. On the **Modify DB cluster** page, review your modifications, then choose one of the following:
   + **Apply during the next scheduled maintenance window**
   + **Apply immediately**

1. Choose **Modify cluster**\.

## AWS CLI<a name="aurora-serverless.modifying.cli"></a>

To modify the scaling configuration of an Aurora Serverless v1 DB cluster using the AWS CLI, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\. Specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

In this example, you modify the scaling configuration of an Aurora Serverless v1 DB cluster named *sample\-cluster*\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier sample-cluster \
    --scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=500,TimeoutAction='ForceApplyCapacityChange',AutoPause=true
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier sample-cluster ^
    --scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=500,TimeoutAction='ForceApplyCapacityChange',AutoPause=true
```

To perform an in\-place upgrade from a MySQL 5\.6\-compatible Aurora Serverless v1 DB cluster to a MySQL 5\.7\-compatible one, specify the `--engine-version` parameter with an Aurora MySQL version 2 version number that's compatible with Aurora Serverless v1\. Also include the `--allow-major-version-upgrade` parameter\.

In this example, you modify the major version of a MySQL 5\.6\-compatible Aurora Serverless v1 DB cluster named *sample\-cluster*\. Doing so performs an in\-place upgrade to a MySQL 5\.7\-compatible Aurora Serverless v1 DB cluster\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier sample-cluster \
    --engine-version 5.7.mysql_aurora.2.08.3 \
    --allow-major-version-upgrade
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier sample-cluster ^
    --engine-version 5.7.mysql_aurora.2.08.3 ^
    --allow-major-version-upgrade
```

To perform an in\-place upgrade from a PostgreSQL 10\-compatible Aurora Serverless v1 DB cluster to a PostgreSQL 11\-compatible one, specify the `--engine-version` parameter with an Aurora PostgreSQL version 11 version number that's compatible with Aurora Serverless v1\. Also include the `--allow-major-version-upgrade` parameter\.

In this example, you modify the major version of a PostgreSQL 10\-compatible Aurora Serverless v1 DB cluster named *sample\-cluster*\. Doing so performs an in\-place upgrade to a PostgreSQL 11\-compatible Aurora Serverless v1 DB cluster\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier sample-cluster \
    --engine-version 11.13 \
    --allow-major-version-upgrade
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier sample-cluster ^
    --engine-version 11.13 ^
    --allow-major-version-upgrade
```

## RDS API<a name="aurora-serverless.modifying.api"></a>

You can modify the scaling configuration of an Aurora DB cluster with the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\. Specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

To perform an in\-place upgrade from a MySQL 5\.6\-compatible Aurora Serverless v1 DB cluster to a MySQL 5\.7\-compatible one, specify the `EngineVersion` parameter with an Aurora MySQL version 2 version number that's compatible with Aurora Serverless v1\. Also include the `AllowMajorVersionUpgrade` parameter\.

To perform an in\-place upgrade from a PostgreSQL 10\-compatible Aurora Serverless v1 DB cluster to a PostgreSQL 11\-compatible one, specify the `EngineVersion` parameter with an Aurora PostgreSQL version 11 version number that's compatible with Aurora Serverless v1\. Also include the `AllowMajorVersionUpgrade` parameter\.