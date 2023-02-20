# Starting a database activity stream<a name="DBActivityStreams.Enabling"></a>

To monitor database activity for all instances in your Aurora DB cluster, start an activity stream at the cluster level\. Any DB instances that you add to the cluster are also automatically monitored\. If you use an Aurora global database, start a database activity stream on each DB cluster separately\. Each cluster delivers audit data to its own Kinesis stream within its own AWS Region\.

When you start an activity stream, each database activity event that you configured in the audit policy generates an activity stream event\. SQL commands such as `CONNECT` and `SELECT` generate access events\. SQL commands such as `CREATE` and `INSERT` generate change events\.

## Console<a name="DBActivityStreams.Enabling-collapsible-section-E1"></a>

**To start a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster on which you want to start an activity stream\. 

1. For **Actions**, choose **Start activity stream**\. 

   The **Start database activity stream: ***name* window appears, where *name* is your DB cluster\.

1. Enter the following settings:
   + For **AWS KMS key**, choose a key from the list of AWS KMS keys\.
**Note**  
 If your Aurora MySQL cluster can't access KMS keys, follow the instructions in [Network prerequisites for Aurora MySQL database activity streams](DBActivityStreams.Prereqs.md) to enable such access first\. 

     Aurora uses the KMS key to encrypt the key that in turn encrypts database activity\. Choose a KMS key other than the default key\. For more information about encryption keys and AWS KMS, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide\.*
   + For **Database activity stream mode**, choose **Asynchronous** or **Synchronous**\.
**Note**  
This choice applies only to Aurora PostgreSQL\. For Aurora MySQL, you can use only asynchronous mode\.
   + Choose **Immediately**\.

     When you choose **Immediately**, the DB cluster restarts right away\. If you choose **During the next maintenance window**, the DB cluster doesn't restart right away\. In this case, the database activity stream doesn't start until the next maintenance window\.

1. Choose **Start database activity stream**\.

   The status for the DB cluster shows that the activity stream is starting\.
**Note**  
If you get the error `You can't start a database activity stream in this configuration`, check [Supported DB instance classes for database activity streams](DBActivityStreams.Overview.md#DBActivityStreams.Overview.requirements.classes) to see whether your DB cluster is using a supported instance class\.

## AWS CLI<a name="DBActivityStreams.Enabling-collapsible-section-E2"></a>

To start database activity streams for a DB cluster , configure the DB cluster using the [start\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/start-activity-stream.html) AWS CLI command\.
+ `--resource-arn arn` – Specifies the Amazon Resource Name \(ARN\) of the DB cluster\.
+ `--mode sync-or-async` – Specifies either synchronous \(`sync`\) or asynchronous \(`async`\) mode\. For Aurora PostgreSQL, you can choose either value\. For Aurora MySQL, specify `async`\. 
+ `--kms-key-id key` – Specifies the KMS key identifier for encrypting messages in the database activity stream\. The AWS KMS key identifier is the key ARN, key ID, alias ARN, or alias name for the AWS KMS key\.

The following example starts a database activity stream for a DB cluster in asynchronous mode\.

For Linux, macOS, or Unix:

```
aws rds start-activity-stream \
    --mode async \
    --kms-key-id my-kms-key-arn \
    --resource-arn my-cluster-arn \
    --apply-immediately
```

For Windows:

```
aws rds start-activity-stream ^
    --mode async ^
    --kms-key-id my-kms-key-arn ^
    --resource-arn my-cluster-arn ^
    --apply-immediately
```

## RDS API<a name="DBActivityStreams.Enabling-collapsible-section-E3"></a>

To start database activity streams for a DB cluster, configure the cluster using the [StartActivityStream](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartActivityStream.html) operation\.

Call the action with the parameters below:
+ `Region`
+ `KmsKeyId`
+ `ResourceArn`
+ `Mode`