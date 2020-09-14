# DB Cluster Status<a name="Aurora.Status"></a>

The status of a DB cluster indicates the health of the DB cluster\. You can view the status of a DB cluster by using the Amazon RDS console, the AWS CLI command [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html), or the API operation [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html)\.

**Note**  
Aurora also uses another status called *maintenance status*, which is shown in the **Maintenance** column of the Amazon RDS console\. This value indicates the status of any maintenance patches that need to be applied to a DB cluster\. Maintenance status is independent of DB cluster status\. For more information on *maintenance status*, see [Applying Updates for a DB Cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\.

Find the possible status values for DB clusters in the following table\.


| DB Cluster Status | Billed | Description | 
| --- | --- | --- | 
| available | Billed |  The DB cluster is healthy and available\.  | 
| backing\-up | Billed |  The DB cluster is currently being backed up\.  | 
| backtracking | Billed |  The DB cluster is currently being backtracked\. This status only applies to Aurora MySQL\.  | 
| cloning\-failed | Not billed |  Cloning a DB cluster failed\.  | 
| creating | Not billed |  The DB cluster is being created\. The DB cluster is inaccessible while it is being created\.  | 
| deleting | Not billed |  The DB cluster is being deleted\.  | 
| failing\-over | Billed |  A failover from the primary instance to an Aurora Replica is being performed\.  | 
| inaccessible\-encryption\-credentials | Not billed |  The AWS Key Management Service customer master key \(CMK\) used to encrypt or decrypt the DB cluster can't be accessed\.  | 
| maintenance | Billed |  Amazon RDS is applying a maintenance update to the DB cluster\. This status is used for DB cluster\-level maintenance that RDS schedules well in advance\.  | 
| migrating | Billed |  A DB cluster snapshot is being restored to a DB cluster\.  | 
| migration\-failed | Not billed |  A migration failed\.  | 
| modifying | Billed |  The DB cluster is being modified because of a customer request to modify the DB cluster\.  | 
| promoting | Billed |  A read replica is being promoted to a standalone DB cluster\.  | 
| renaming | Billed |  The DB cluster is being renamed because of a customer request to rename it\.  | 
| resetting\-master\-credentials | Billed |  The master credentials for the DB cluster are being reset because of a customer request to reset them\.  | 
| starting | Billed for storage |  The DB cluster is starting\.  | 
| stopped | Billed for storage |  The DB cluster is stopped\.  | 
| stopping | Billed for storage |  The DB cluster is being stopped\.  | 
| update\-iam\-db\-auth | Billed |  IAM authorization for the DB cluster is being updated\.  | 
| upgrading | Billed |  The DB cluster engine version is being upgraded\.  | 