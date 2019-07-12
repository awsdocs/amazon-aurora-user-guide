# Viewing Aurora Serverless DB Clusters<a name="aurora-serverless.viewing"></a>

After you create one or more Aurora Serverless DB clusters, you can view which DB clusters are type **Serverless** and which are type **Instance**\. You can also view the current number of Aurora capacity units \(ACUs\) each Aurora Serverless DB cluster is using\. Each ACU is a combination of processing and memory capacity\.

**To view your Aurora Serverless DB clusters**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the Aurora Serverless DB clusters\.

1. In the navigation pane, choose **Databases**\.

   For each DB cluster, the DB cluster type is shown under **Role**\. The Aurora Serverless DB clusters show **Serverless** for the type\. You can view an Aurora Serverless DB cluster's current capacity under **Size**\.   
![\[Viewing Aurora Serverless DB clusters\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-viewing.png)

1. Choose the name of an Aurora Serverless DB cluster to display its details\.

   On the **Connectivity & security** tab, note the database endpoint, because it's required to connect to the DB cluster\.  
![\[Viewing Aurora Serverless DB cluster database endpoint\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-endpoint.png)

   Choose the Configuration tab to view the capacity settings\.  
![\[Viewing Aurora Serverless DB cluster capacity settings\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity-settings.png)

   A *scaling event *is generated when the DB cluster scales up, scales down, pauses, or resumes\. Choose the **Logs & events** tab to see recent events\. The following image shows examples of these events\.  
![\[Aurora Serverless scaling events\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-scaling.png)

 You can also monitor your Aurora Serverless DB cluster in CloudWatch\. In particular, you can monitor the capacity allocated to the DB cluster with the `ServerlessDatabaseCapacity` metric\. You can also monitor all of the standard Aurora CloudWatch metrics, such as `CPUUtilization`, `DatabaseConnections`, `Queries`, and so on\. For details about how to monitor Aurora clusters through CloudWatch, see [Publishing Amazon Aurora MySQL Logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\. 

 You can have Aurora publish some or all database logs to CloudWatch\. You select which logs to publish by enabling the configuration parameters such as `general_log` and `slow_query_log` in the DB cluster parameter group associated with the Serverless cluster\. Unlike provisioned clusters, Serverless clusters don't require you to specify in the cluster settings which log types to upload to CloudWatch\. Serverless clusters automatically upload all the available logs\. For information about enabling the log configuration parameters for Serverless clusters, see [Aurora Serverless and Parameter Groups](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\. For information about monitoring Aurora clusters through CloudWatch, see [Publishing Amazon Aurora MySQL Logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\. 

To connect to an Aurora Serverless DB cluster, use the database endpoint\. Follow the instructions in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md) to connect to your Aurora Serverless DB cluster\.

**Note**  
With Aurora Serverless, you can't connect directly to specific DB instances in the DB cluster\.