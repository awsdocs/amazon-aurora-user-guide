# Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions<a name="AuroraMySQL.Replication.CrossRegion"></a>

You can create an Amazon Aurora MySQL DB cluster as a read replica in a different AWS Region than the source DB cluster\. Taking this approach can improve your disaster recovery capabilities, let you scale read operations into an AWS Region that is closer to your users, and make it easier to migrate from one AWS Region to another\.

You can create read replicas of both encrypted and unencrypted DB clusters\. The read replica must be encrypted if the source DB cluster is encrypted\.

For each source DB cluster, you can have up to five cross\-Region DB clusters that are read replicas\. When you create an Aurora MySQL DB cluster read replica in another AWS Region, you should be aware of the following:
+ Both your source DB cluster and your cross\-Region read replica DB cluster can have up to 15 Aurora Replicas, along with the primary instance for the DB cluster\. By using this functionality, you can scale read operations for both your source AWS Region and your replication target AWS Region\.
+ In a cross\-Region scenario, there is more lag time between the source DB cluster and the read replica due to the longer network channels between AWS Regions\.
+ Data transferred for cross\-Region replication incurs Amazon RDS data transfer charges\. The following cross\-Region replication actions generate charges for the data transferred out of the source AWS Region:
  + When you create the read replica, Amazon RDS takes a snapshot of the source cluster and transfers the snapshot to the AWS Region that holds the read replica\.
  + For each data modification made in the source databases, Amazon RDS transfers data from the source region to the AWS Region that holds the read replica\.

  For more information about Amazon RDS data transfer pricing, see [Amazon Aurora Pricing](http://aws.amazon.com/rds/aurora/pricing/)\.
+ You can run multiple concurrent create or delete actions for read replicas that reference the same source DB cluster\. However, you must stay within the limit of five read replicas for each source DB cluster\.
+ For replication to operate effectively, each read replica should have the same amount of compute and storage resources as the source DB cluster\. If you scale the source DB cluster, you should also scale the read replicas\.

**Topics**
+ [Before You Begin](#AuroraMySQL.Replication.CrossRegion.Prerequisites)
+ [Creating an Amazon Aurora MySQL DB Cluster That Is a Cross\-Region Read Replica](#AuroraMySQL.Replication.CrossRegion.Creating)
+ [Viewing Amazon Aurora MySQL Cross\-Region Replicas](#AuroraMySQL.Replication.CrossRegion.Viewing)
+ [Promoting a Read Replica to Be a DB Cluster](#AuroraMySQL.Replication.CrossRegion.Promote)
+ [Troubleshooting Amazon Aurora MySQL Cross Region Replicas](#AuroraMySQL.Replication.CrossRegion.Troubleshooting)

## Before You Begin<a name="AuroraMySQL.Replication.CrossRegion.Prerequisites"></a>

Before you can create an Aurora MySQL DB cluster that is a cross\-Region read replica, you must enable binary logging on your source Aurora MySQL DB cluster\. Cross\-region replication for Aurora MySQL uses MySQL binary replication to replay changes on the cross\-Region read replica DB cluster\.

To enable binary logging on an Aurora MySQL DB cluster, update the `binlog_format` parameter for your source DB cluster\. The `binlog_format` parameter is a cluster\-level parameter that is in the default cluster parameter group\. If your DB cluster uses the default DB cluster parameter group, create a new DB cluster parameter group to modify `binlog_format` settings\. We recommend that you set the `binlog_format` to `MIXED`\. However, you can also set `binlog_format` to `ROW` or `STATEMENT` if you need a specific binlog format\. Reboot your Aurora DB cluster for the change to take effect\.

For more information, see [Amazon Aurora DB Cluster and DB Instance Parameters](USER_WorkingWithParamGroups.md#Aurora.Managing.ParameterGroups) and [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\. 

## Creating an Amazon Aurora MySQL DB Cluster That Is a Cross\-Region Read Replica<a name="AuroraMySQL.Replication.CrossRegion.Creating"></a>

You can create an Aurora DB cluster that is a cross\-Region read replica by using the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), or the Amazon RDS API\. You can create cross\-Region read replicas from both encrypted and unencrypted DB clusters\.

When you create a cross\-Region read replica for Aurora MySQL by using the AWS Management Console, Amazon RDS creates a DB cluster in the target AWS Region, and then automatically creates a DB instance that is the primary instance for that DB cluster\. 

When you create a cross\-Region read replica using the AWS CLI or RDS API, you first create the DB cluster in the target AWS Region and wait for it to become active\. Once it is active, you then create a DB instance that is the primary instance for that DB cluster\. 

Replication begins when the primary instance of the read replica DB cluster becomes available\. 

Use the following procedures to create a cross\-Region read replica from an Aurora MySQL DB cluster\. These procedures work for creating read replicas from either encrypted or unencrypted DB clusters\.

### Console<a name="AuroraMySQL.Replication.CrossRegion.Creating.Console"></a>

**To create an Aurora MySQL DB cluster that is a cross\-Region read replica with the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, select the AWS Region that hosts your source DB cluster\.

1. In the navigation pane, choose **Instances**\.

1. Choose the check box for the DB instance that you want to create a cross\-Region read replica for\. For **Actions**, choose **Create cross region read replica**\.

1. On the **Create cross region read replica** page, choose the option settings for your cross\-Region read replica DB cluster, as described in the following table\.    
<a name="cross-region-read-replica-settings"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.CrossRegion.html)

1. Choose **Create** to create your cross\-Region read replica for Aurora\.

### AWS CLI<a name="AuroraMySQL.Replication.CrossRegion.Creating.CLI"></a>

**To create an Aurora MySQL DB cluster that is a cross\-Region read replica with the CLI**

1. Call the AWS CLI `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` command in the AWS Region where you want to create the read replica DB cluster\. Include the `--replication-source-identifier` option and specify the Amazon Resource Name \(ARN\) of the source DB cluster to create a read replica for\. 

   For cross\-Region replication where the DB cluster identified by `--replication-source-identifier` is encrypted, you must specify the `--kms-key-id` option and the `--storage-encrypted` option\. You must also specify either the `--source-region` or `--pre-signed-url` option\. Using `--source-region` autogenerates a presigned URL that is a valid request for the `CreateDBCluster` API operation that can be performed in the source AWS Region that contains the encrypted DB cluster to be replicated\. Using `--pre-signed-url` requires you to construct a presigned URL manually instead\. The AWS KMS key identifier is used to encrypt the read replica\. It must be an AWS KMS customer master key \(CMK\) valid for the destination AWS Region\. To learn more about these options, see `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)`\. 
**Note**  
You can set up cross\-Region replication from an unencrypted DB cluster to an encrypted read replica by specifying `--storage-encrypted` and providing a value for `--kms-key-id`\. In this case, you don't need to specify `--source-region` or `--pre-signed-url`\.

   You can't specify the `--master-username` and `--master-user-password` parameters\. Those values are taken from the source DB cluster\. 

   The following code example creates a read replica in the us\-east\-1 Region from an unencrypted DB cluster snapshot in the us\-west\-2 Region\. The command is called in the us\-east\-1 Region\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster \
     --db-cluster-identifier sample-replica-cluster \
     --engine aurora \
     --replication-source-identifier arn:aws:rds:us-west-2:123456789012:cluster:sample-master-cluster
   ```

   For Windows:

   ```
   aws rds create-db-cluster ^
     --db-cluster-identifier sample-replica-cluster ^
     --engine aurora ^
     --replication-source-identifier arn:aws:rds:us-west-2:123456789012:cluster:sample-master-cluster
   ```

   The following code example creates a read replica in the us\-east\-1 Region from an encrypted DB cluster snapshot in the us\-west\-2 Region\. The command is called in the us\-east\-1 Region\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster \
     --db-cluster-identifier sample-replica-cluster \
     --engine aurora \
     --replication-source-identifier arn:aws:rds:us-west-2:123456789012:cluster:sample-master-cluster \
     --kms-key-id my-us-east-1-key \
     --source-region us-west-2 \
     --storage-encrypted
   ```

   For Windows:

   ```
   aws rds create-db-cluster ^
     --db-cluster-identifier sample-replica-cluster ^
     --engine aurora ^
     --replication-source-identifier arn:aws:rds:us-west-2:123456789012:cluster:sample-master-cluster ^
     --kms-key-id my-us-east-1-key ^
     --source-region us-west-2 ^
     --storage-encrypted
   ```

1. Check that the DB cluster has become available to use by using the AWS CLI `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` command, as shown in the following example\.

   ```
   aws rds describe-db-clusters --db-cluster-identifier sample-replica-cluster                                
   ```

   When the `describe-db-clusters` results show a status of `available`, create the primary instance for the DB cluster so that replication can begin\. To do so, use the AWS CLI `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` command as shown in the following example\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
     --db-cluster-identifier sample-replica-cluster \
     --db-instance-class db.r3.large \
     --db-instance-identifier sample-replica-instance \
     --engine aurora
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
     --db-cluster-identifier sample-replica-cluster ^
     --db-instance-class db.r3.large ^
     --db-instance-identifier sample-replica-instance ^
     --engine aurora
   ```

   When the DB instance is created and available, replication begins\. You can determine if the DB instance is available by calling the AWS CLI `[describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)` command\.

### RDS API<a name="AuroraMySQL.Replication.CrossRegion.Creating.API"></a>

**To create an Aurora MySQL DB cluster that is a cross\-Region read replica with the API**

1. Call the RDS API `[CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)` action in the AWS Region where you want to create the read replica DB cluster\. Include the `ReplicationSourceIdentifier` parameter and specify the Amazon Resource Name \(ARN\) of the source DB cluster to create a read replica for\. 

   For cross\-Region replication where the DB cluster identified by `ReplicationSourceIdentifier` is encrypted, you must specify the `KmsKeyId` parameter and set the `StorageEncrypted` parameter to `true`\. You must also specify the `PreSignedUrl` parameter\. The presigned URL must be a valid request for the `CreateDBCluster` API operation that can be performed in the source AWS Region that contains the encrypted DB cluster to be replicated\. The AWS KMS key identifier is used to encrypt the read replica, and must be a AWS KMS customer master key \(CMK\) valid for the destination AWS Region\. To automatically rather than manually generate a presigned URL, use the AWS CLI `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` command with the `--source-region` option instead\.
**Note**  
You can set up cross\-Region replication from an unencrypted DB cluster to an encrypted read replica by specifying `StorageEncrypted` as **true** and providing a value for `KmsKeyId`\. In this case, you don't need to specify `PreSignedUrl`\.

   You don't need to include the `MasterUsername` and `MasterUserPassword` parameters, because those values are taken from the source DB cluster\. 

   The following code example creates a read replica in the us\-east\-1 Region from an unencrypted DB cluster snapshot in the us\-west\-2 Region\. The action is called in the us\-east\-1 Region\. 

   ```
   https://rds.us-east-1.amazonaws.com/
     ?Action=CreateDBCluster
     &ReplicationSourceIdentifier=arn:aws:rds:us-west-2:123456789012:cluster:sample-master-cluster
     &DBClusterIdentifier=sample-replica-cluster
     &Engine=aurora
     &SignatureMethod=HmacSHA256
     &SignatureVersion=4
     &Version=2014-10-31
     &X-Amz-Algorithm=AWS4-HMAC-SHA256
     &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
     &X-Amz-Date=20160201T001547Z
     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
     &X-Amz-Signature=a04c831a0b54b5e4cd236a90dcb9f5fab7185eb3b72b5ebe9a70a4e95790c8b7
   ```

   The following code example creates a read replica in the us\-east\-1 Region from an encrypted DB cluster snapshot in the us\-west\-2 Region\. The action is called in the us\-east\-1 Region\. 

   ```
   https://rds.us-east-1.amazonaws.com/
     ?Action=CreateDBCluster
     &KmsKeyId=my-us-east-1-key
     &StorageEncrypted=true
     &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
            %253FAction%253DCreateDBCluster
            %2526DestinationRegion%253Dus-east-1
            %2526KmsKeyId%253Dmy-us-east-1-key
            %2526ReplicationSourceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Acluster%25253Asample-master-cluster
            %2526SignatureMethod%253DHmacSHA256
            %2526SignatureVersion%253D4
            %2526Version%253D2014-10-31
            %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
            %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
            %2526X-Amz-Date%253D20161117T215409Z
            %2526X-Amz-Expires%253D3600
            %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
            %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
     &ReplicationSourceIdentifier=arn:aws:rds:us-west-2:123456789012:cluster:sample-master-cluster
     &DBClusterIdentifier=sample-replica-cluster
     &Engine=aurora
     &SignatureMethod=HmacSHA256
     &SignatureVersion=4
     &Version=2014-10-31
     &X-Amz-Algorithm=AWS4-HMAC-SHA256
     &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
     &X-Amz-Date=20160201T001547Z
     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
     &X-Amz-Signature=a04c831a0b54b5e4cd236a90dcb9f5fab7185eb3b72b5ebe9a70a4e95790c8b7
   ```

1. Check that the DB cluster has become available to use by using the RDS API `[DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html)` action, as shown in the following example\.

   ```
   https://rds.us-east-1.amazonaws.com/
     ?Action=DescribeDBClusters
     &DBClusterIdentifier=sample-replica-cluster
     &SignatureMethod=HmacSHA256
     &SignatureVersion=4
     &Version=2014-10-31
     &X-Amz-Algorithm=AWS4-HMAC-SHA256
     &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
     &X-Amz-Date=20160201T002223Z
     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
     &X-Amz-Signature=84c2e4f8fba7c577ac5d820711e34c6e45ffcd35be8a6b7c50f329a74f35f426
   ```

   When the `DescribeDBClusters` results show a status of `available`, create the primary instance for the DB cluster so that replication can begin\. To do so, use the RDS API `[CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)` action as shown in the following example\.

   ```
   https://rds.us-east-1.amazonaws.com/
     ?Action=CreateDBInstance
     &DBClusterIdentifier=sample-replica-cluster
     &DBInstanceClass=db.r3.large
     &DBInstanceIdentifier=sample-replica-instance  
     &Engine=aurora
     &SignatureMethod=HmacSHA256
     &SignatureVersion=4
     &Version=2014-10-31
     &X-Amz-Algorithm=AWS4-HMAC-SHA256
     &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
     &X-Amz-Date=20160201T003808Z
     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
     &X-Amz-Signature=125fe575959f5bbcebd53f2365f907179757a08b5d7a16a378dfa59387f58cdb
   ```

   When the DB instance is created and available, replication begins\. You can determine if the DB instance is available by calling the AWS CLI `[DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)` command\.

## Viewing Amazon Aurora MySQL Cross\-Region Replicas<a name="AuroraMySQL.Replication.CrossRegion.Viewing"></a>

You can view the cross\-Region replication relationships for your Amazon Aurora MySQL DB clusters by calling the `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` AWS CLI command or the `[DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html)` RDS API operation\. In the response, refer to the `ReadReplicaIdentifiers` field for the DB cluster identifiers of any cross\-Region read replica DB clusters, and refer to the `ReplicationSourceIdentifier` element for the ARN of the source DB cluster that is the replication source\. 

## Promoting a Read Replica to Be a DB Cluster<a name="AuroraMySQL.Replication.CrossRegion.Promote"></a>

You can promote an Aurora MySQL Read Replica to a standalone DB cluster\. When you promote an Aurora MySQL Read Replica, its DB instances are rebooted before they become available\. 

Typically, you promote an Aurora MySQL Read Replica to a standalone DB cluster as a data recovery scheme if the source DB cluster fails\. 

To do this, first create a read replica and then monitor the source DB cluster for failures\. In the event of a failure, do the following: 

1. Promote the read replica\.

1. Direct database traffic to the promoted DB cluster\.

1. Create a replacement read replica with the promoted DB cluster as its source\.

When you promote a read replica, the read replica becomes a standalone Aurora DB cluster\. The promotion process can take several minutes or longer to complete, depending on the size of the read replica\. After you promote the read replica to a new DB cluster, it's just like any other DB cluster\. For example, you can create read replicas from it and perform point\-in\-time restore operations\. You can also create Aurora Replicas for the DB cluster\. 

Because the promoted DB cluster is no longer a read replica, you can't use it as a replication target\. 

The following steps show the general process for promoting a read replica to a DB cluster: 

1. Stop any transactions from being written to the read replica source DB cluster, and then wait for all updates to be made to the read replica\. Database updates occur on the read replica after they have occurred on the source DB cluster, and this replication lag can vary significantly\. Use the `ReplicaLag` metric to determine when all updates have been made to the read replica\. The `ReplicaLag` metric records the amount of time a read replica DB instance lags behind the source DB instance\. When the `ReplicaLag` metric reaches `0`, the read replica has caught up to the source DB instance\.

1. Promote the read replica by using the **Promote** option on the Amazon RDS console, the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html), or the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplicaDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplicaDBCluster.html) Amazon RDS API operation\.

   You choose an Aurora MySQL DB instance to promote the read replica\. After the read replica is promoted, the Aurora MySQL DB cluster is promoted to a standalone DB cluster\. The DB instance with the highest failover priority is promoted to the primary DB instance for the DB cluster\. The other DB instances become Aurora Replicas\.
**Note**  
The promotion process takes a few minutes to complete\. When you promote a read replica, replication is stopped and the DB instances are rebooted\. When the reboot is complete, the read replica is available as a new DB cluster\.

### Console<a name="AuroraMySQL.Replication.CrossRegion.Promote.Console"></a>

**To promote an Aurora MySQL read replica to a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. On the console, choose **Instances**\. 

   The **Instance** pane appears\. 

1. In the **Instances** pane, choose the read replica that you want to promote\. 

   The read replicas appear as Aurora MySQL DB instances\.

1. For **Actions**, choose **Promote read replica**\. 

1. On the acknowledgment page, choose **Promote Read Replica**\. 

### AWS CLI<a name="AuroraMySQL.Replication.CrossRegion.Promote.CLI"></a>

To promote a read replica to a DB cluster, use the AWS CLI [ `promote-read-replica-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html) command\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds promote-read-replica-db-cluster \
    --db-cluster-identifier mydbcluster
```
For Windows:  

```
aws rds promote-read-replica-db-cluster ^
    --db-cluster-identifier mydbcluster
```

### RDS API<a name="AuroraMySQL.Replication.CrossRegion.Promote.API"></a>

To promote a read replica to a DB cluster, call [ `PromoteReadReplicaDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplicaDBCluster.html)\. 

## Troubleshooting Amazon Aurora MySQL Cross Region Replicas<a name="AuroraMySQL.Replication.CrossRegion.Troubleshooting"></a>

Following you can find a list of common error messages that you might encounter when creating an Amazon Aurora cross\-Region read replica, and how to resolve the specified errors\.

### Source cluster \[DB cluster ARN\] doesn't have binlogs enabled<a name="AuroraMySQL.Replication.CrossRegion.Troubleshooting.1"></a>

To resolve this issue, enable binary logging on the source DB cluster\. For more information, see [Before You Begin](#AuroraMySQL.Replication.CrossRegion.Prerequisites)\.

### Source cluster \[DB cluster ARN\] doesn't have cluster parameter group in sync on writer<a name="AuroraMySQL.Replication.CrossRegion.Troubleshooting.2"></a>

You receive this error if you have updated the `binlog_format` DB cluster parameter, but have not rebooted the primary instance for the DB cluster\. Reboot the primary instance \(that is, the writer\) for the DB cluster and try again\.

### Source cluster \[DB cluster ARN\] already has a read replica in this region<a name="AuroraMySQL.Replication.CrossRegion.Troubleshooting.3"></a>

You can have up to five cross\-Region DB clusters that are read replicas for each source DB cluster in any AWS Region\. If you already have the maximum number of read replicas for a DB cluster in a particular AWS Region, you must delete an existing one before you can create a new cross\-Region DB cluster in that Region\.

### DB cluster \[DB cluster ARN\] requires a database engine upgrade for cross\-Region replication support<a name="AuroraMySQL.Replication.CrossRegion.Troubleshooting.4"></a>

To resolve this issue, upgrade the database engine version for all of the instances in the source DB cluster to the most recent database engine version, and then try creating a cross\-Region read replica DB again\. 