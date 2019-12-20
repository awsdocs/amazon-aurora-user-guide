# Logging Data API Calls with AWS CloudTrail<a name="logging-using-cloudtrail-data-api"></a>

Data API is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in Data API\. CloudTrail captures all API calls for Data API as events, including calls from the Amazon RDS console and from code calls to the Data API operations\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Data API\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the data collected by CloudTrail, you can determine a lot of information\. This information includes the request that was made to Data API, the IP address the request was made from, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Working with Data API Information in CloudTrail<a name="service-name-info-in-cloudtrail-data-api"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in Data API, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html) in the *AWS CloudTrail User Guide\.*

For an ongoing record of events in your AWS account, including events for Data API, create a trail\. A *trail* enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all AWS Regions\. The trail logs events from all AWS Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following topics in the *AWS CloudTrail User Guide*: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

All Data API operations are logged by CloudTrail and documented in the [ *Amazon RDS Data Service API Reference*](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/Welcome.html)\. For example, calls to the `BatchExecuteStatement`, `BeginTransaction`, `CommitTransaction`, and `ExecuteStatement` operations generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or IAM user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Understanding Data API Log File Entries<a name="understanding-service-name-entries-data-api"></a>

A *trail* is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An *event* represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files aren't an ordered stack trace of the public API calls, so they don't appear in any specific order\. 

The following example shows a CloudTrail log entry that demonstrates the `ExecuteStatement` operation\.

```
{
    "eventVersion": "1.05",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "AKIAIOSFODNN7EXAMPLE",
        "arn": "arn:aws:iam::123456789012:user/johndoe",
        "accountId": "123456789012",
        "accessKeyId": "AKIAI44QH8DHBEXAMPLE",
        "userName": "johndoe"
    },
    "eventTime": "2019-12-18T00:49:34Z",
    "eventSource": "rdsdata.amazonaws.com",
    "eventName": "ExecuteStatement",
    "awsRegion": "us-east-1",
    "sourceIPAddress": "192.0.2.0",
    "userAgent": "aws-cli/1.16.102 Python/3.7.2 Windows/10 botocore/1.12.92",
    "requestParameters": {
        "continueAfterTimeout": false,
        "database": "**********",
        "includeResultMetadata": false,
        "parameters": [],
        "resourceArn": "arn:aws:rds:us-east-1:123456789012:cluster:my-database-1",
        "schema": "**********",
        "secretArn": "arn:aws:secretsmanager:us-east-1:123456789012:secret:dataapisecret-ABC123",
        "sql": "**********"
    },
    "responseElements": null,
    "requestID": "6ba9a36e-b3aa-4ca8-9a2e-15a9eada988e",
    "eventID": "a2c7a357-ee8e-4755-a0d0-aed11ed4253a",
    "eventType": "AwsApiCall",
    "recipientAccountId": "123456789012"
}
```