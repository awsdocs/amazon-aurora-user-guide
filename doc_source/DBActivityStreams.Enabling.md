# Starting a database activity stream<a name="DBActivityStreams.Enabling"></a>

You start an activity stream at the DB cluster level to monitor database activity for all DB instances of the cluster\. Any DB instances added to the cluster are also automatically monitored\. 

When you start an activity stream, each database activity event, such as a change or access, generates an activity stream event\. Access events are generated from SQL commands such as `CONNECT` and `SELECT`\. Change events are generated from SQL commands such as `CREATE` and `INSERT`\. To make each activity stream event durable, Aurora encrypts and stores it\.

You can choose to have the database session handle database activity events either asynchronously or synchronously:
+ **Asynchronous mode** – In *asynchronous mode, *when a database session generates an activity stream event, the session returns to normal activities immediately\. In the background, the activity stream event is made a durable record\. If an error occurs in the background task, an RDS event is sent\. This event indicates the beginning and end of any time windows where activity stream event records might have been lost\.

  Asynchronous mode favors database performance over the accuracy of the activity stream\. 
**Note**  
 Asynchronous mode is available for both Aurora PostgreSQL and Aurora MySQL\. 
+ **Synchronous mode** – In *synchronous mode, *when a database session generates an activity stream event, the session blocks other activities until the event is made durable\. If the event can't be made durable for some reason, the database session returns to normal activities\. However, an RDS event is sent indicating that activity stream records might be lost for some time\. A second RDS event is sent after the system is back to a healthy state\.

  The synchronous mode favors the accuracy of the activity stream over database performance\.
**Note**  
 Synchronous mode is available for Aurora PostgreSQL\. You can't use synchronous mode with Aurora MySQL\. 

## Console<a name="DBActivityStreams.Enabling-collapsible-section-E1"></a>

**To start an activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to use\.

1. For **Actions**, choose **Start activity stream**\. The **Database Activity Stream** window appears\. 

1. Enter the following settings in the **Database Activity Stream** window:
   + For **Master key**, choose a key from the list of AWS KMS customer master keys \(CMKs\)\.
**Note**  
 If your Aurora MySQL cluster can't access AWS KMS CMKs, follow the instructions in [Network prerequisites for Aurora MySQL Database Activity Streams](DBActivityStreams.Prereqs.md) to enable such access first\. 

     The master key is used to encrypt the key that in turn encrypts the database activity logged\. You must choose a master key other than the default key\. For more information about encryption keys and AWS KMS, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide\.*
   + For **Database activity stream mode**, choose **Asynchronous** or **Synchronous**\.
**Note**  
 Currently, this choice only applies to Aurora PostgreSQL\. For Aurora MySQL, you can use only asynchronous mode\. 
   + Choose **Apply immediately**\.

     If you choose **Schedule for the next maintenance window**, the database doesn't restart right away\. Instead, it remains in the `PENDING REBOOT` state\. In this case, the database activity stream doesn't start until the next maintenance window\.

   When you're done entering settings, choose **Continue**\.

   The cluster's DB instance status shows that the database activity stream is being configured\. 

## AWS CLI<a name="DBActivityStreams.Enabling-collapsible-section-E2"></a>

To start database activity streams for a DB cluster, configure the DB cluster using the [start\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/start-activity-stream.html) AWS CLI command\. Identify the AWS Region for the DB cluster with the `--region` parameter\. The `--apply-immediately` parameter is optional\.

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

**Note**  
 The `--mode` parameter is required\. For Aurora PostgreSQL, you can choose either value\. For Aurora MySQL, you must always specify `async`\. 