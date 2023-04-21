# Overview of events for Aurora<a name="rds-cloudwatch-events.sample"></a>

An *RDS event* indicates a change in the Aurora environment\. For example, Amazon Aurora generates an event when a DB cluster is patched\. Amazon Aurora delivers events to CloudWatch Events and EventBridge in near\-real time\.

**Note**  
Amazon RDS emits events on a best effort basis\. We recommend that you avoid writing programs that depend on the order or existence of notification events, because they might be out of sequence or missing\.

Amazon RDS records events that relate to the following resources:
+ DB clusters

  For a list of cluster events, see [DB cluster events](USER_Events.Messages.md#USER_Events.Messages.cluster)\.
+ DB instances

  For a list of DB instance events, see [DB instance events](USER_Events.Messages.md#USER_Events.Messages.instance)\.
+ DB parameter groups

  For a list of DB parameter group events, see [DB parameter group events](USER_Events.Messages.md#USER_Events.Messages.parameter-group)\.
+ DB security groups

  For a list of DB security group events, see [DB security group events](USER_Events.Messages.md#USER_Events.Messages.security-group)\.
+ DB cluster snapshots

  For a list of DB cluster snapshot events, see [DB cluster snapshot events](USER_Events.Messages.md#USER_Events.Messages.cluster-snapshot)\.
+ RDS Proxy events

  For a list of RDS Proxy events, see [RDS Proxy events](USER_Events.Messages.md#USER_Events.Messages.rds-proxy)\.
+ Blue/green deployment events

  For a list of blue/green deployment events, see [Blue/green deployment events](USER_Events.Messages.md#USER_Events.Messages.BlueGreenDeployments)\.

This information includes the following: 
+ The date and time of the event
+ The source name and source type of the event
+ A message associated with the event
+ Event notifications include tags from when the message was sent and may not reflect tags at the time when the event occurred