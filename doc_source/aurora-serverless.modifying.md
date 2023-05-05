# Modifying an Aurora Serverless v1 DB cluster<a name="aurora-serverless.modifying"></a>

After you configure an Aurora Serverless v1 DB cluster, you can modify certain properties with the AWS Management Console, the AWS CLI, or the RDS API\. Most of the properties you can modify are the same as for other kinds of Aurora clusters\.

The most relevant changes for Aurora Serverless v1 are the following:
+ [Modifying the scaling configuration](#aurora-serverless.modifying.scaling)
+ [Upgrading the major version](#aurora-serverless.modifying.upgrade)
+ [Converting from Aurora Serverless v1 to provisioned](#aurora-serverless.modifying.convert)

## Modifying the scaling configuration of an Aurora Serverless v1 DB cluster<a name="aurora-serverless.modifying.scaling"></a>

You can set the minimum and maximum capacity for the DB cluster\. Each capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless automatically creates scaling rules for thresholds for CPU utilization, connections, and available memory\. You can also set whether Aurora Serverless pauses the database when there's no activity and then resumes when activity begins again\.

You can set the following specific values for the scaling configuration:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+  **Autoscaling timeout and action** – This section specifies how long Aurora Serverless waits to find a scaling point before timing out\. It also specifies the action to take when a capacity modification times out because it can't find a scaling point\. Aurora can force the capacity change to set the capacity to the specified value as soon as possible\. Or, it can roll back the capacity change to cancel it\. For more information, see [Timeout action for capacity changes](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – Use the optional **Scale the capacity to 0 ACUs when cluster is idle** setting to scale the database to zero processing capacity while it's inactive\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

### Console<a name="aurora-serverless.modifying.scaling.CON"></a>

You can modify the scaling configuration of an Aurora DB cluster with the AWS Management Console\.

**To modify an Aurora Serverless v1 DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless v1 DB cluster that you want to modify\.

1. For **Actions**, choose **Modify cluster**\.

1. In the **Capacity settings** section, modify the scaling configuration\.

1. Choose **Continue**\.

1. On the **Modify DB cluster** page, review your modifications, then choose when to apply them\.

1. Choose **Modify cluster**\.

### AWS CLI<a name="aurora-serverless.modifying.scaling.CLI"></a>

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

### RDS API<a name="aurora-serverless.modifying.scaling.API"></a>

You can modify the scaling configuration of an Aurora DB cluster with the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\. Specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

## Upgrading the major version of an Aurora Serverless v1 DB cluster<a name="aurora-serverless.modifying.upgrade"></a>

You can choose the major version for certain Aurora Serverless v1 DB clusters:
+ Compatible with MySQL 5\.6 – Choose a corresponding MySQL 5\.7–compatible version number\.
+ Compatible with PostgreSQL 10 – Choose a corresponding PostgreSQL 11–compatible version number\.
+ Compatible with PostgreSQL 11 – Choose a corresponding PostgreSQL 13–compatible version number\.

Doing so performs an in\-place upgrade of the Aurora Serverless v1 DB cluster\.

### Console<a name="aurora-serverless.modifying.upgrade.CON"></a>

You can perform an in\-place upgrade of an Aurora Serverless v1 DB cluster using the AWS Management Console\.

**To upgrade an Aurora Serverless v1 DB cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless v1 DB cluster that you want to upgrade\.

1. For **Actions**, choose **Modify cluster**\.

1. For **Version**:
   + To perform an in\-place upgrade from a MySQL 5\.6–compatible cluster to a MySQL 5\.7–compatible one, choose an Aurora MySQL version 2 version number\.
   + To perform an in\-place upgrade from a PostgreSQL 10–compatible cluster to a PostgreSQL 11–compatible one, choose an Aurora PostgreSQL version 11 version number\.
   + To perform an in\-place upgrade from a PostgreSQL 11–compatible cluster to a PostgreSQL 13–compatible one, choose an Aurora PostgreSQL version 13 version number\.

   The following example shows an in\-place upgrade from Aurora PostgreSQL 11\.16 to 13\.9\.  
![\[Upgrading an Aurora Serverless v1 DB cluster using the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/sv1-upgrade-apg11-to-13.png)

   If you perform a major version upgrade, leave all of the other properties the same\. To change any other properties, do another **Modify** operation after the upgrade finishes\.

1. Choose **Continue**\.

1. On the **Modify DB cluster** page, review your modifications, then choose when to apply them\.

1. Choose **Modify cluster**\.

### AWS CLI<a name="aurora-serverless.modifying.upgrade.CLI"></a>

To perform an in\-place upgrade from a MySQL 5\.6–compatible Aurora Serverless v1 DB cluster to a MySQL 5\.7–compatible one, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\. Specify the `--engine-version` parameter with an Aurora MySQL version 2 version number that's compatible with Aurora Serverless v1\. Also include the `--allow-major-version-upgrade` parameter\.

In this example, you modify the major version of a MySQL 5\.6–compatible Aurora Serverless v1 DB cluster named *sample\-cluster*\. Doing so performs an in\-place upgrade to a MySQL 5\.7–compatible Aurora Serverless v1 DB cluster\.

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

To perform an in\-place upgrade from a PostgreSQL 10–compatible Aurora Serverless v1 DB cluster to a PostgreSQL 11–compatible one, specify the `--engine-version` parameter with an Aurora PostgreSQL version 11 version number that's compatible with Aurora Serverless v1\. Also include the `--allow-major-version-upgrade` parameter\.

To perform an in\-place upgrade from a PostgreSQL 11–compatible Aurora Serverless v1 DB cluster to a PostgreSQL 13–compatible one, specify the `--engine-version` parameter with an Aurora PostgreSQL version 13 version number that's compatible with Aurora Serverless v1\. Also include the `--allow-major-version-upgrade` parameter\.

In this example, you modify the major version of a PostgreSQL 11–compatible Aurora Serverless v1 DB cluster named *sample\-cluster*\. Doing so performs an in\-place upgrade to a PostgreSQL 13–compatible Aurora Serverless v1 DB cluster\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier sample-cluster \
    --engine-version 13.9 \
    --allow-major-version-upgrade
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier sample-cluster ^
    --engine-version 13.9 ^
    --allow-major-version-upgrade
```

### RDS API<a name="aurora-serverless.modifying.upgrade.API"></a>

To perform an in\-place upgrade from a MySQL 5\.6–compatible Aurora Serverless v1 DB cluster to a MySQL 5\.7–compatible one, use the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\. Specify the `EngineVersion` parameter with an Aurora MySQL version 2 version number that's compatible with Aurora Serverless v1\. Also include the `AllowMajorVersionUpgrade` parameter\.

To perform an in\-place upgrade from a PostgreSQL 10–compatible Aurora Serverless v1 DB cluster to a PostgreSQL 11–compatible one, specify the `EngineVersion` parameter with an Aurora PostgreSQL version 11 version number that's compatible with Aurora Serverless v1\. Also include the `AllowMajorVersionUpgrade` parameter\.

To perform an in\-place upgrade from a PostgreSQL 11–compatible Aurora Serverless v1 DB cluster to a PostgreSQL 13–compatible one, specify the `EngineVersion` parameter with an Aurora PostgreSQL version 13 version number that's compatible with Aurora Serverless v1\. Also include the `AllowMajorVersionUpgrade` parameter\.

## Converting an Aurora Serverless v1 DB cluster to provisioned<a name="aurora-serverless.modifying.convert"></a>

You can convert an Aurora Serverless v1 DB cluster to a provisioned DB cluster\. To perform the conversion, you change the DB instance class to **Provisioned**\. You can use this conversion as part of upgrading your DB cluster from Aurora Serverless v1 to Aurora Serverless v2\. For more information, see [Upgrading from an Aurora Serverless v1 cluster to Aurora Serverless v2](aurora-serverless-v2.upgrade.md#aurora-serverless-v2.upgrade-from-serverless-v1-procedure)\.

The conversion process creates a reader DB instance in the DB cluster, promotes the reader instance to a writer instance, and then deletes the original Aurora Serverless v1 instance\. When you convert the DB cluster, you can't perform any other modifications at the same time, such as changing the DB engine version or DB cluster parameter group\. The conversion operation is applied immediately, and can't be undone\.

During the conversion, a backup DB cluster snapshot is taken of the DB cluster in case an error occurs\. The identifier for the DB cluster snapshot has the form `pre-modify-engine-mode-DB_cluster_identifier-timestamp`\.

**Note**  
Aurora PostgreSQL version 10 is deprecated\. Upgrade your PostgreSQL 10–compatible Aurora Serverless v1 DB clusters to PostgreSQL 11–compatible ones before converting them\.  
Aurora MySQL version 1 is deprecated\. Upgrade your MySQL 5\.6–compatible Aurora Serverless v1 DB clusters to MySQL 5\.7–compatible ones before converting them\.  
For more information, see [Upgrading the major version of an Aurora Serverless v1 DB cluster](#aurora-serverless.modifying.upgrade)\.

Aurora uses the current default DB minor engine version for the provisioned DB cluster\.

If you don't provide a DB instance class for your converted DB cluster, Aurora recommends one based on the maximum capacity of the original Aurora Serverless v1 DB cluster\. The recommended capacity to instance class mappings are shown in the following table\.


| Serverless maximum capacity \(ACUs\) | Provisioned DB instance class | 
| --- | --- | 
| 1 | db\.t3\.small | 
| 2 | db\.t3\.medium | 
| 4 | db\.t3\.large | 
| 8 | db\.r5\.large | 
| 16 | db\.r5\.xlarge | 
| 32 | db\.r5\.2xlarge | 
| 64 | db\.r5\.4xlarge | 
| 128 | db\.r5\.8xlarge | 
| 192 | db\.r5\.12xlarge | 
| 256 | db\.r5\.16xlarge | 
| 384 | db\.r5\.24xlarge | 

**Note**  
Depending on the DB instance class you choose, and your database usage, you might see different costs for a provisioned DB cluster compared to Aurora Serverless v1\.  
If you convert your Aurora Serverless v1 DB cluster to a burstable \(db\.t\*\) DB instance class, you might incur additional costs for using the DB cluster\. For more information, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.

### AWS CLI<a name="aurora-serverless.modifying.convert.CLI"></a>

To convert an Aurora Serverless v1 DB cluster to a provisioned cluster, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\.

The following parameters are required:
+ `--db-cluster-identifier` – The Aurora Serverless v1 DB cluster that you're converting to provisioned\.
+ `--engine-mode` – Use the value `provisioned`\.
+ `--allow-engine-mode-change`
+ `--db-cluster-instance-class` – Choose the DB instance class for the provisioned DB cluster based on the capacity of the Aurora Serverless v1 DB cluster\.

In this example, you convert an Aurora Serverless v1 DB cluster named `sample-cluster` and use the `db.r5.xlarge` DB instance class\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier sample-cluster \
    --engine-mode provisioned \
    --allow-engine-mode-change \
    --db-cluster-instance-class db.r5.xlarge
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier sample-cluster ^
    --engine-mode provisioned ^
    --allow-engine-mode-change ^
    --db-cluster-instance-class db.r5.xlarge
```

### RDS API<a name="aurora-serverless.modifying.convert.API"></a>

To convert an Aurora Serverless v1 DB cluster to a provisioned cluster, use the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\.

The following parameters are required:
+ `DBClusterIdentifier` – The Aurora Serverless v1 DB cluster that you're converting to provisioned\.
+ `EngineMode` – Use the value `provisioned`\.
+ `AllowEngineModeChange`
+ `DBClusterInstanceClass` – Choose the DB instance class for the provisioned DB cluster based on the capacity of the Aurora Serverless v1 DB cluster\.