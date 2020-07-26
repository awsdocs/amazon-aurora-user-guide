# Adding Aurora Replicas to a DB Cluster<a name="aurora-replicas-adding"></a>

In an Aurora DB cluster with single\-master replication, there is one primary DB instance and up to 15 Aurora Replicas\. The primary DB instance supports read and write operations, and performs all of the data modifications to the cluster volume\. Aurora Replicas connect to the same storage volume as the primary DB instance and support only read operations\. Aurora Replicas can offload read workloads from the primary DB instance\. 

We recommend that you distribute the primary instance and Aurora Replicas in your DB cluster over multiple Availability Zones to improve the availability of your DB cluster\. For more information, see [Region Availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\.

You can add Aurora Replicas to a DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

To remove an Aurora Replica from a DB cluster, delete the Aurora Replica DB instance by following the instructions in [Deleting a DB Instance in an Aurora DB Cluster](USER_DeleteInstance.md)\.

 For more information about Aurora Replicas, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)\. 

**Note**  
Amazon Aurora also supports replication with an external database, or an RDS DB instance\. When using Amazon Aurora, your RDS DB instance must be in the same AWS Region\. For more information, see [Replication with Amazon Aurora](Aurora.Replication.md)\.

## Console<a name="aurora-replicas-adding.Console"></a>

**To add an Aurora Replica to a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB cluster where you want to add the new DB instance\. 

1.  Make sure that both the cluster and the primary instance are in the **Available** state\. If the DB cluster or the primary instance are in a transitional state such as **Creating**, you can't add a replica\. 

    If the cluster doesn't have a primary instance, create one using the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command\. This situation can arise if you used the CLI to restore a DB cluster snapshot and then view the cluster in the AWS Management Console\.  

1. For **Actions**, choose **Add reader**\. 

   The **Add reader** page appears\.

1. On the **Add reader** page, specify options for your Aurora Replica\. The following table shows settings for an Aurora Replica\.    
<a name="aurora_replica_settings"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-replicas-adding.html)

1. Choose **Add reader** to create the Aurora Replica\.

## AWS CLI<a name="aurora-replicas-adding.CLI"></a>

To create an Aurora Replica in your DB cluster, run the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command\. Include the name of the DB cluster as the `--db-cluster-identifier` option\. You can optionally specify an Availability Zone for the Aurora Replica using the `--availability-zone` parameter, as shown in the following examples\.

For example, the following command creates a new MySQL 5\.7–compatible Aurora Replica named `sample-instance-us-west-2a`\.

For Linux, macOS, or Unix:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a \
    --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large \
    --availability-zone us-west-2a
```

For Windows:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a ^
    --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large ^
    --availability-zone us-west-2a
```

The following command creates a new MySQL 5\.6–compatible Aurora Replica named `sample-instance-us-west-2a`\.

For Linux, macOS, or Unix:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a \
    --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large \
    --availability-zone us-west-2a
```

For Windows:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a ^
    --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large ^
    --availability-zone us-west-2a
```

The following command creates a new PostgreSQL\-compatible Aurora Replica named `sample-instance-us-west-2a`\.

For Linux, macOS, or Unix:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a \
    --db-cluster-identifier sample-cluster --engine aurora-postgresql --db-instance-class db.r4.large \
    --availability-zone us-west-2a
```

For Windows:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a ^
    --db-cluster-identifier sample-cluster --engine aurora-postgresql --db-instance-class db.r4.large ^
    --availability-zone us-west-2a
```

## RDS API<a name="aurora-replicas-adding.API"></a>

To create an Aurora Replica in your DB cluster, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation\. Include the name of the DB cluster as the `DBClusterIdentifier` parameter\. You can optionally specify an Availability Zone for the Aurora Replica using the `AvailabilityZone` parameter\.