# Starting a database activity stream<a name="DBActivityStreams.Enabling"></a>

To monitor database activity for all instances of the DB cluster, start an activity stream at the cluster level\. Any DB instances that you add to the cluster are also automatically monitored\.

When you start an activity stream, each database activity event, such as a change or access, generates an activity stream event\. SQL commands such as `CONNECT` and `SELECT` generate access events\. SQL commands such as `CREATE` and `INSERT` generate change events\.

**Important**  
Turning on an activity stream clears existing audit data\. It also revokes audit trail privileges\. 

## Console<a name="DBActivityStreams.Enabling-collapsible-section-E1"></a>

**To start a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster for which you want to enable an activity stream\.

1. For **Actions**, choose **Start activity stream**\. 

   The **Start database activity stream: *name*** window appears, where *name* is your \. 

1. Enter the following settings:
   + For **Master key**, choose a key from the list of AWS KMS customer master keys \(CMKs\)\.
**Note**  
 If your Aurora MySQL cluster can't access AWS KMS CMKs, follow the instructions in [Network prerequisites for Aurora MySQL database activity streams](DBActivityStreams.md#DBActivityStreams.Prereqs) to enable such access first\. 

     Aurora uses the master key to encrypt the key that in turn encrypts database activity\. Choose a master key other than the default key\. For more information about encryption keys and AWS KMS, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide\.*
   + For **Database activity stream mode**, choose **Asynchronous** or **Synchronous**\.
**Note**  
This choice applies only to Aurora PostgreSQL\. For Aurora MySQL, you can use only asynchronous mode\. 
   + Choose **Immediately**\.

     When you choose **Immediately**, the DB cluster restarts right away\. If you choose **During the next maintenance window**, the DB cluster doesn't restart right away\. In this case, the database activity stream doesn't start until the next maintenance window\.

   When you're done entering settings, choose **Start database activity stream**\.

   The status for the DB cluster shows that the activity stream is starting\. 

## AWS CLI<a name="DBActivityStreams.Enabling-collapsible-section-E2"></a>

To start database activity streams for a DB cluster , configure the DB cluster using the [start\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/start-activity-stream.html) AWS CLI command\.
+ `--kms-key-id key` – Specifies the AWS KMS key identifier for encrypting messages in the database activity stream\. The AWS KMS key identifier is the key ARN, key ID, alias ARN, or alias name for the AWS KMS customer master key \(CMK\)\.
+ `--resource-arn arn` – Specifies the Amazon Resource Name \(ARN\) of the DB cluster\.
+ `--region` – Identifies the AWS Region for the DB instance\.
+ `--mode sync-or-async` – Specifies either synchronous \(`sync`\) or asynchronous \(`async`\) mode\. For Aurora PostgreSQL, you can choose either value\. For Aurora MySQL, specify `sync`\. 
+ `--apply-immediately` – Applies the change immediately\. This parameter is optional\. If you don't specify this parameter, the database activity stream starts at the next maintenance interval\.

For Linux, macOS, or Unix:

```
aws rds --region MY_REGION \
    start-activity-stream \
    --mode [sync | async] \
    --kms-key-id MY_KMS_KEY_ARN \
    --resource-arn MY_CLUSTER_ARN \
    --apply-immediately
```

For Windows:

```
aws rds --region MY_REGION ^
    start-activity-stream ^
    --mode [sync | async] ^
    --kms-key-id MY_KMS_KEY_ARN ^
    --resource-arn MY_CLUSTER_ARN ^
    --apply-immediately
```

## RDS API<a name="DBActivityStreams.Enabling-collapsible-section-E3"></a>

To start database activity streams for a DB cluster, configure the cluster using the [StartActivityStream](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartActivityStream.html) operation\.

Call the action with the parameters below:
+ `Region`
+ `Mode`
+ `ApplyImmediately`