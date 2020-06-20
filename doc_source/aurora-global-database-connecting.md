# Connecting to an Aurora Global Database<a name="aurora-global-database-connecting"></a>

 For read\-only query traffic, you connect to the reader endpoint for the Aurora cluster in your AWS Region\. 

 To run data manipulation language \(DML\) or data definition language \(DDL\) statements, you connect to the cluster endpoint for the primary cluster\. This endpoint might be in a different AWS Region than your application\. 

When you view an Aurora global database in the console, you can see all the general\-purpose endpoints associated with all of its clusters\. The following screenshot shows an example\. There is a single cluster endpoint associated with the primary cluster that you use for write operations\. The primary cluster and each secondary cluster has a reader endpoint, which you use for read\-only queries\. To minimize latency, choose whichever reader endpoint is in your AWS Region or the AWS Region closest to you\. The following shows an Aurora MySQL example\.

![\[Screenshot showing an Aurora global database and associated connectivity settings (endpoints) in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-primary-cluster-connectivity.png)