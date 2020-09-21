# Stopping and Starting an Amazon Aurora DB Cluster<a name="aurora-cluster-stop-start"></a>

 Stopping and starting Amazon Aurora clusters helps you manage costs for development and test environments\. You can temporarily stop all the DB instances in your cluster, instead of setting up and tearing down all the DB instances each time that you use the cluster\. 

**Topics**
+ [Overview of Stopping and Starting an Aurora DB Cluster](#aurora-cluster-start-stop-overview)
+ [Limitations for Stopping and Starting Aurora DB Clusters](#aurora-cluster-stop-limitations)
+ [Stopping an Aurora DB Cluster](#aurora-cluster-stop)
+ [Possible Operations While an Aurora DB Cluster Is Stopped](#aurora-cluster-stopped)
+ [Starting an Aurora DB Cluster](#aurora-cluster-start)

## Overview of Stopping and Starting an Aurora DB Cluster<a name="aurora-cluster-start-stop-overview"></a>

 During periods where you don't need an Aurora cluster, you can stop all instances in that cluster at once\. You can start the cluster again anytime you need to use it\. Starting and stopping simplifies the setup and teardown processes for clusters used for development, testing, or similar activities that don't require continuous availability\. You can perform all the AWS Management Console procedures involved with only a single action, regardless of how many instances are in the cluster\. 

 While your DB cluster is stopped, you are charged only for cluster storage, manual snapshots, and automated backup storage within your specified retention window\. You aren't charged for any DB instance hours\. Aurora automatically starts your DB cluster after seven days so that it doesn't fall behind any required maintenance updates\. 

 To minimize charges for a lightly loaded Aurora cluster, you can stop the cluster instead of deleting all of its Aurora Replicas\. For clusters with more than one or two instances, frequently deleting and recreating the DB instances is only practical using the AWS CLI or Amazon RDS API\. Such a sequence of operations can also be difficult to perform in the right order, for example to delete all Aurora Replicas before deleting the primary instance to avoid activating the failover mechanism\. 

 Don't use starting and stopping if you need to keep your DB cluster running but it has more capacity than you need\. If your cluster is too costly or not very busy, delete one or more DB instances or change all your DB instances to a small instance class\. You can't stop an individual Aurora DB instance\. 

## Limitations for Stopping and Starting Aurora DB Clusters<a name="aurora-cluster-stop-limitations"></a>

 Some Aurora clusters can't be stopped and started: 
+  You can't stop and start a cluster that's part of an [Aurora global database](aurora-global-database.md)\. 
+  For a cluster that uses the [Aurora parallel query](aurora-mysql-parallel-query.md) feature, the minimum Aurora MySQL versions are 1\.23\.0 and 2\.09\.0\. 
+  You can't stop and start an [Aurora Serverless cluster](aurora-serverless.md)\. 
+  You can't stop and start an [Aurora multi\-master cluster](aurora-multi-master.md)\. 

 If an existing cluster can't be stopped and started, the **Stop** action isn't available from the **Actions** menu on the **Databases** page or the details page\. 

## Stopping an Aurora DB Cluster<a name="aurora-cluster-stop"></a>

To use an Aurora DB cluster or perform administration, you always begin with a running Aurora DB cluster, then stop the cluster, and then start the cluster again\. While your cluster is stopped, you are charged for cluster storage, manual snapshots, and automated backup storage within your specified retention window, but not for DB instance hours\. 

 The stop operation stops the Aurora Replica instances first, then the primary instance, to avoid activating the failover mechanism\. 

 You can't stop a DB cluster that acts as the replication target for data from another DB cluster, or acts as the replication master and transmits data to another cluster\. 

 You can't stop certain special kinds of clusters\. Currently, you can't stop a cluster that's part of an Aurora global database, or a multi\-master cluster\. 

### Console<a name="aurora-stop-cluster.CON"></a>

**To stop an Aurora cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**, and then choose a cluster\. You can perform the stop operation from this page, or navigate to the details page for the DB cluster that you want to stop\. 

    If an existing cluster can't be stopped and started, the **Stop** action isn't available from the **Actions** menu on the **Databases** page or the details page\. For the kinds of clusters that you can't start and stop, see [Limitations for Stopping and Starting Aurora DB Clusters](#aurora-cluster-stop-limitations)\. 

1. For **Actions**, choose **Stop**\. 

### AWS CLI<a name="aurora-stop-cluster.CLI"></a>

To stop a DB instance by using the AWS CLI, call the [stop\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-db-cluster.html) command with the following parameters: 
+ `--db-cluster-identifier` – the name of the Aurora cluster\. 

**Example**  

```
aws rds stop-db-cluster --db-cluster-identifier mydbcluster
```

### RDS API<a name="aurora-stop-cluster.API"></a>

To stop a DB instance by using the Amazon RDS API, call the [StopDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StopDBCluster.html) operation with the following parameter: 
+ `DBClusterIdentifier` – the name of the Aurora cluster\. 

## Possible Operations While an Aurora DB Cluster Is Stopped<a name="aurora-cluster-stopped"></a>

 While an Aurora cluster is stopped, you can do a point\-in\-time restore to any point within your specified automated backup retention window\. For details about doing a point\-in\-time restore, see [Restoring Data](Aurora.Managing.Backups.md#Aurora.Managing.Backups.Restore)\. 

 You can't modify the configuration of an Aurora DB cluster, or any of its DB instances, while the cluster is stopped\. You also can't add or remove DB instances from the cluster, or delete the cluster if it still has any associated DB instances\. You must start the cluster before performing any such administrative actions\. 

 Aurora applies any scheduled maintenance to your stopped cluster after it's started again\. Remember that after seven days, Aurora automatically starts any stopped clusters so that they don't fall too far behind in their maintenance status\. 

 Aurora also doesn't perform any automated backups, because the underlying data can't change while the cluster is stopped\. Aurora doesn't extend the backup retention period while the cluster is stopped\. 

## Starting an Aurora DB Cluster<a name="aurora-cluster-start"></a>

You always start an Aurora DB cluster beginning with an Aurora cluster that is already in the stopped state\. When you start the cluster, all its DB instances become available again\. The cluster keeps its configuration settings such as endpoints, parameter groups, and VPC security groups\. 

### Console<a name="aurora-start-cluster.CON"></a>

**To start an Aurora cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**, and then choose a cluster\. You can perform the start operation from this page, or navigate to the details page for the DB cluster that you want to start\.  

1.  For **Actions**, choose **Start**\. 

### AWS CLI<a name="aurora-start-cluster.CLI"></a>

To start a DB cluster by using the AWS CLI, call the [start\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-cluster.html) command with the following parameters: 
+ `--db-cluster-identifier` – the name of the Aurora cluster\. This name is either a specific cluster identifier you chose when creating the cluster, or the DB instance identifier you chose with `-cluster` appended to the end\. 

**Example**  

```
aws rds start-db-cluster --db-cluster-identifier mydbcluster
```

### RDS API<a name="aurora-start-cluster.API"></a>

To start an Aurora DB cluster by using the Amazon RDS API, call the [StartDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartDBCluster.html) operation with the following parameter: 
+ `DBCluster` – the name of the Aurora cluster\. This name is either a specific cluster identifier you chose when creating the cluster, or the DB instance identifier you chose with `-cluster` appended to the end\. 