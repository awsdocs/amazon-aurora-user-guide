# Stopping an activity stream<a name="DBActivityStreams.Disabling"></a>

You can stop an activity stream using the console or AWS CLI\. 

If you delete a DB cluster, the activity stream is stopped and the underlying Amazon Kinesis stream is deleted automatically\.

## Console<a name="DBActivityStreams.Disabling-collapsible-section-D1"></a>

**To turn off an activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to stop the database activity stream for\.

1. For **Actions**, choose **Stop activity stream**\. The **Database Activity Stream** window appears\.

   1. Choose **Apply immediately**\.

      If you choose **Schedule for the next maintenance window**, the database doesn't restart right away\. Instead, it remains in the `PENDING REBOOT` state\. In this case, the data activity stream isn't disabled until the next maintenance window\.

   1. Choose **Continue**\.

## AWS CLI<a name="DBActivityStreams.Disabling-collapsible-section-D2"></a>

To stop database activity streams for a DB cluster, configure the DB cluster using the AWS CLI command [stop\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-activity-stream.html)\. Identify the AWS Region for the DB cluster using the `--region` parameter\. The `--apply-immediately` parameter is optional\.

For Linux, macOS, or Unix:

```
aws rds --region MY_REGION \
        stop-activity-stream \
        --resource-arn MY_CLUSTER_ARN \
        --apply-immediately
```

For Windows:

```
aws rds --region MY_REGION ^
        stop-activity-stream ^
        --resource-arn MY_CLUSTER_ARN ^
        --apply-immediately
```