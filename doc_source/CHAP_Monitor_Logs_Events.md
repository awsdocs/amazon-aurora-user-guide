# Monitoring events, logs, and streams in an Amazon Aurora DB cluster<a name="CHAP_Monitor_Logs_Events"></a>

When you monitor your Amazon Aurora databases and your other AWS solutions, your goal is to maintain the following:
+ Reliability
+ Availability
+ Performance

[Monitoring metrics in an Amazon Aurora cluster](MonitoringAurora.md) explains how to monitor your cluster using metrics\. A complete solution must also monitor database events, log files, and activity streams\. AWS provides you with the following monitoring tools:
+ *Amazon EventBridge* is a serverless event bus service that makes it easy to connect your applications with data from a variety of sources\. EventBridge delivers a stream of real\-time data from your own applications, Software\-as\-a\-Service \(SaaS\) applications, and AWS services and routes that data to targets such as AWS Lambda\. This enables you to monitor events that happen in services, and build event\-driven architectures\. For more information, see the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.
+ *Amazon CloudWatch Logs* lets you monitor, store, and access your log files from Amazon Aurora instances, AWS CloudTrail, and other sources\. Amazon CloudWatch Logs can monitor information in the log files and notify you when certain thresholds are met\. You can also archive your log data in highly durable storage\. For more information, see the [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\.
+ *AWS CloudTrail* captures API calls and related events made by or on behalf of your AWS account and delivers the log files to an Amazon S3 bucket that you specify\. You can identify which users and accounts called AWS, the source IP address from which the calls were made, and when the calls occurred\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.
+ *Database Activity Streams* is an Amazon Aurora feature that provides a near\-real\-time stream of the activity in your DB cluster\. Amazon Aurora pushes activities to an Amazon Kinesis data stream\. The Kinesis stream is created automatically\. From Kinesis, you can configure AWS services such as Amazon Kinesis Data Firehose and AWS Lambda to consume the stream and store the data\.

**Topics**
+ [Viewing logs, events, and streams in the Amazon RDS console](logs-events-streams-console.md)
+ [Monitoring Amazon Aurora events](working-with-events.md)
+ [Monitoring Amazon Aurora log files](USER_LogAccess.md)
+ [Monitoring Amazon Aurora API calls in AWS CloudTrail](logging-using-cloudtrail.md)
+ [Monitoring Amazon Aurora with Database Activity Streams](DBActivityStreams.md)