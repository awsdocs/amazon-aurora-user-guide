# Viewing Aurora Serverless DB Clusters<a name="aurora-serverless.viewing"></a>

After you create one or more Aurora Serverless DB clusters, you can view which DB clusters are type **Serverless** and which are type **Instance**\. For each Aurora Serverless DB cluster, you can view the current number of Aurora Capacity Units \(ACUs\) it is using\. Each ACU is a combination of processing and memory capacity\.

**To view your Aurora Serverless DB clusters**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the Aurora Serverless DB clusters\.

1. In the navigation pane, choose **Databases**\.

   For each DB cluster, the DB cluster type is shown under **Type**\. The provisioned DB clusters show **Provisioned** for the type, and the Aurora Serverless DB clusters show **Serverless** for the type\. You can view an Aurora Serverless DB cluster's current capacity under **Size**\.   
![\[Viewing Aurora Serverless DB clusters\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-viewing.png)

1. Choose an Aurora Serverless DB cluster name to display details about the DB cluster\.

   Note the database endpoint, because it's required to connect to the DB cluster\.  
![\[Viewing Aurora Serverless DB cluster database endpoint\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-endpoint.png)

   A *scaling event *is generated when the DB cluster scales up, scales down, pauses, or resumes\. The following image shows examples of these events\.  
![\[Aurora Serverless scaling events\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-scaling.png)

You can also monitor your Aurora Serverless DB cluster in CloudWatch\. In particular, you can monitor the capacity allocated to the DB cluster with the `ServerlessDatabaseCapacity` metric\. You can also monitor all of the standard Aurora CloudWatch metrics, such as `CPUUtilization`, `DatabaseConnections`, `Queries`, and so on\. You can also have Aurora publish some or all database logs to CloudWatch\. You select which logs to publish when you create the cluster, or by modifying an existing cluster\. For details about how to monitor Aurora clusters through CloudWatch, see [Publishing Amazon Aurora MySQL Logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\. 

To connect to an Aurora Serverless DB cluster, use the database endpoint\. Follow the instructions in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md) to connect to your Aurora Serverless DB cluster\.

**Note**  
With Aurora Serverless, you can't connect directly to specific DB instances in the DB cluster\.