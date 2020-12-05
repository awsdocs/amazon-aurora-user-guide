# How Aurora Serverless v2 \(preview\) works<a name="aurora-serverless-2.how-it-works"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. | 

Amazon Aurora Serverless v2 \(preview\) has been architected from the ground up to support serverless DB clusters that are instantly scalable\. The Aurora Serverless v2 \(preview\) architecture rests on a lightweight foundation that's been engineered to provide the security and isolation needed in multi\-tenant serverless cloud environments\. This foundation has very little overhead so it can respond quickly\. It's also powerful enough to meet dramatic increases in processing demand\.

## Instant autoscaling<a name="aurora-serverless-2.how-it-works.autoscaling"></a>

When you create your Aurora Serverless v2 \(preview\) DB cluster, you define its capacity as a range between minimum and maximum number of Aurora capacity units \(ACUs\): 
+ Minimum Aurora capacity units – The smallest number of ACUs down to which your Aurora Serverless v2 \(preview\) DB cluster can scale\. 
+ Maximum Aurora capacity units – The largest number of ACUs up to which your Aurora Serverless v2 \(preview\) DB cluster can scale\.

Each ACU provides 2 GiB \(gibibytes\) of memory \(RAM\) and an approximation of a virtual processor \(vCPU\) with networking based on number of connections\. 

Unlike Aurora Serverless, which scales by doubling ACUs each time the DB cluster reaches a threshold, Aurora Serverless v2 \(preview\) can increase ACUs incrementally\. When your workload demand begins to reach the current resource capacity, your Aurora Serverless v2 \(preview\) DB cluster scales in discrete 1\-ACU increments\. 

 The following screenshot shows instant autoscaling in action\. It's an extract from Amazon CloudWatch comparing processing load to the number of ACUs consumed by an Aurora Serverless v2 \(preview\) DB cluster over time for a simulated "flash sale" scenario\. The simulation models an order system that processes about 10 orders per second \(using 4 ACUs\) during regular operations\. A load testing tool generates various increases in orders over several minutes, until the system is processing 275 orders per second \(and 22 ACUs\) at its peak\. 

![\[Aurora Serverless v2 (preview) autoscaling to meet demand, as seen in CloudWatch\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles2-autoscaling-in-action.png)

1. Orders processed each second – Processing load as customers respond to a "flash sale" for a product\. The line shows the number of orders being processed each second\. Order processing involves multiple database actions\. These include checking inventory, processing the new order, creating a shipment order, adjusting the inventory amount, and initiating shipping by notifying the warehouse system\. 

1. Aurora capacity units \(ACUs\) – Memory and CPU applied over time to increasing and decreasing demand\. The line shows the surge of ACUs applied to the workload \(line 1\) when orders reach their highest point, about 275 per second\. 

An ACU is made up of both memory \(RAM\) and processor \(CPU\)\. Increases in CPU utilization respond immediately to workload demands\. When the demand starts to decline from its peak, the scale down from the maximum ACU occurs more slowly, as memory is more gradually released \(than CPU\)\. This is a deliberate architectural choice\. Aurora Serverless v2 \(preview\) releases memory more gradually as demand lessens to avoid affecting the workload\.