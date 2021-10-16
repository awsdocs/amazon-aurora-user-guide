# How Aurora Serverless v2 \(preview\) works<a name="aurora-serverless-2.how-it-works"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. All resources and data will be deleted when the preview ends\.  | 

 Amazon Aurora Serverless v2 \(preview\) has been architected from the ground up to support serverless DB clusters that are instantly scalable\. The Aurora Serverless v2 \(preview\) architecture rests on a lightweight foundation that's engineered to provide the security and isolation needed in multitenant serverless cloud environments\. This foundation has very little overhead so it can respond quickly\. It's also powerful enough to meet dramatic increases in processing demand\. 

## Instant autoscaling<a name="aurora-serverless-2.how-it-works.autoscaling"></a>

 When you create your Aurora Serverless v2 \(preview\) DB cluster, you define its capacity as a range between minimum and maximum number of Aurora capacity units \(ACUs\): 
+  Minimum Aurora capacity units – The smallest number of ACUs down to which your Aurora Serverless v2 \(preview\) DB cluster can scale\. 
+  Maximum Aurora capacity units – The largest number of ACUs up to which your Aurora Serverless v2 \(preview\) DB cluster can scale\. 

 Each ACU provides 2 GiB \(gibibytes\) of memory \(RAM\) and associated virtual processor \(vCPU\) with networking\. 

 Unlike Aurora Serverless v1, which scales by doubling ACUs each time the DB cluster reaches a threshold, Aurora Serverless v2 \(preview\) can increase ACUs incrementally\. When your workload demand begins to reach the current resource capacity, your Aurora Serverless v2 \(preview\) DB cluster scales the number of ACUs\. Your cluster scales ACUs in the increments required to provide the best performance for the resources consumed\. 

 The following screenshot shows instant autoscaling in action\. It's an extract from Amazon CloudWatch comparing processing load to the number of ACUs consumed by an Aurora Serverless v2 \(preview\) DB cluster over time for a simulated "flash sale" scenario\. The simulation models an order system that processes about 10 orders per second \(using 4 ACUs\) during regular operations\. A load testing tool generates various increases in orders mimicking a "flash sale," until the system is processing 275 orders per second \(and 22 ACUs\) at its peak\. 

![\[Aurora Serverless v2 (preview) autoscaling to meet demand, as seen in CloudWatch\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-autoscaling-in-action.png)

 In the screenshot, these numbers indicate this information: 

1.  Orders processed each second – Processing load as customers respond to a "flash sale" for a product\. The line shows the number of orders being processed each second\. Order processing involves multiple database actions\. These include checking inventory, processing the new order, creating a shipment order, adjusting the inventory amount, and initiating shipping by notifying the warehouse system\. 

1.  Aurora capacity units \(ACUs\) – Memory and CPU applied over time to increasing and decreasing demand\. The line shows the surge of ACUs applied to the workload \(line 1\) when orders reach their highest point, about 275 per second\. 

 An ACU is made up of both memory \(RAM\) and processor \(CPU\)\. Increases in CPU utilization respond immediately to workload demands\. When the demand starts to decline from its peak, the scale down from the maximum ACU occurs more slowly, as memory is more gradually released \(than CPU\)\. This is a deliberate architectural choice\. Aurora Serverless v2 \(preview\) releases memory more gradually as demand lessens to avoid affecting the workload\. 

## Logging with Amazon CloudWatch<a name="aurora-serverless-2.how-it-works.logging"></a>

 As with all Aurora DB clusters, error logs for Aurora Serverless v2 \(preview\) are enabled by default\. However, unlike with provisioned Aurora DB clusters, you can't view the logs for Aurora Serverless v2 \(preview\) in the Amazon RDS console\. Aurora Serverless v2 \(preview\) automatically uploads the error logs to Amazon CloudWatch\. 

 Aurora Serverless v2 \(preview\) also uploads your Aurora MySQL log data to CloudWatch for the types of logs that you specify\. You choose the logs for uploading by changing values for several log\-related DB cluster parameters for your Aurora Serverless v2 \(preview\) DB cluster\. As with any type of Aurora DB cluster, you can't modify the default DB cluster parameter group\. Instead, create your own DB cluster parameter group based on a default parameter for your DB cluster and engine type\. For Aurora Serverless v2 \(preview\) and Aurora Serverless v1, you use a DB cluster parameter group only\. 

 We recommend that you create your custom DB cluster parameter group before creating your Aurora Serverless v2 \(preview\) DB cluster, so that it's available to choose when you create a database on the console\. 

 You can also modify your Aurora Serverless v2 \(preview\) DB cluster later to use your custom DB cluster parameter group\. For more information, see [Modifying your DB cluster to use a custom DB cluster parameter group](aurora-serverless-2.modify-db-cluster.md#aurora-serverless-2.modify-db-cluster.custom-db-cluster-parameters)\. 

 For Aurora MySQL logging, you can activate the following parameters: 
+  `general_log` – Set to 1 to turn on the general log \(default is off, or 0\)\. 
+  `slow_query_log` – Set to 1 to turn on the slow query log\. \(default is off, or 0\)\. 
+  `server_audit_logging` – Set to 1 to turn on server audit logging\. If you turn this on, you can specify the audit events to send to CloudWatch by listing them in the `server_audit_events` parameter\. 
+  `server_audit_events` – The list of events to capture in the logs\. 

 For more information, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\. 

 After you apply your modified DB cluster parameter group to your Aurora Serverless v2 \(preview\) DB cluster, you can view the logs in CloudWatch\. 

**To view logs for your Aurora Serverless v2 \(preview\) DB cluster**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1.  Choose **US East \(N\. Virginia\)** for the Region\. 

1.  Choose **Log groups**\. 

1.  Choose your Aurora Serverless v2 \(preview\) DB cluster log from the list\. For error logs, the naming pattern is as follows\. 

   ```
   /aws/rds/cluster/cluster-name/error
   ```

 For more information on viewing details on your logs, see [Monitoring log events in Amazon CloudWatch](AuroraMySQL.Integrating.CloudWatch.md#AuroraMySQL.Integrating.CloudWatch.Monitor)\. 

### Monitoring capacity with Amazon CloudWatch<a name="aurora-serverless-2.how-it-works.logging.monitoring"></a>

 Aurora Serverless v2 \(preview\) introduces a new metric for monitoring Aurora DB cluster capacity, `ServerlessDatabaseCapacity`\. You can use CloudWatch to view your DB cluster's capacity as it scales up and down\. You can also compare `ServerlessDatabaseCapacity` to other metrics to see how changes in workloads affect resource consumption\. 

**To monitor your Aurora Serverless v2 \(preview\) DB cluster's capacity**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1.  Choose the US East \(N\. Virginia\) Region\. 

1.  Choose **Metrics**\. All available metrics appear as cards in the console, grouped by service name\. 

1.  Choose **RDS**\. 

 You can also use the **Search** box to find `ServerlessDatabaseCapacity`\. 

 We recommend that you set up a CloudWatch dashboard to monitor your Aurora Serverless v2 \(preview\) DB cluster capacity using this new metric\. To learn how, see [Building dashboards with CloudWatch](https://docs.aws.amazon.com/autoscaling/application/userguide/monitoring-cloudwatch.html)\. You can compare `ServerlessDatabaseCapacity` to `DatabaseUsedMemory`, `DatabaseConnections`, and `DMLThroughput` to assess how your DB cluster is responding during operations\. 

 To learn more about using Amazon CloudWatch with Amazon Aurora, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\. 