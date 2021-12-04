# Analyzing performance anomalies with DevOps Guru for RDS<a name="devops-guru-for-rds"></a>

Amazon DevOps Guru is a fully managed operations service that helps developers and operators improve the performance and availability of their applications\. DevOps Guru offloads the tasks associated with identifying operational issues so that you can quickly implement recommendations to improve your application\. To learn more, see [What is Amazon DevOps Guru?](https://docs.aws.amazon.com/devops-guru/latest/userguide/welcome.html) in the *Amazon DevOps Guru User Guide*\.

DevOps Guru detects, analyzes, and makes recommendations for operational issues for all Amazon RDS DB engines\. DevOps Guru for RDS extends this capability by applying machine learning to Performance Insights metrics for Amazon Aurora databases\. These monitoring features allow DevOps Guru for RDS to detect and diagnose performance bottlenecks and recommend specific corrective actions\. To learn more, see [Overview of DevOps Guru for RDS](https://docs.aws.amazon.com/http://lascap-ug-rds/working-with-rds.overview.html) in the *Amazon DevOps Guru User Guide*\.

**Topics**
+ [Benefits of DevOps Guru for RDS](#devops-guru-for-rds.benefits)
+ [How DevOps Guru for RDS works](#devops-guru-for-rds.how-it-works)
+ [Setting up DevOps Guru for RDS](#devops-guru-for-rds.configuring)

## Benefits of DevOps Guru for RDS<a name="devops-guru-for-rds.benefits"></a>

If you're responsible for an Amazon Aurora database, you might not know that an event or regression that is affecting that database is occurring\. When you learn about the issue, you might not know why it's occurring or what to do about it\. Rather than turning to a database administrator \(DBA\) for help or relying on third\-party tools, you can follow recommendations from DevOps Guru for RDS\. 

You gain the following advantages from the detailed analysis of DevOps Guru for RDS:

**Fast diagnosis**  
DevOps Guru for RDS continuously monitors and analyzes database telemetry\. Performance Insights, Enhanced Monitoring, and Amazon CloudWatch collect telemetry data for your database cluster\. DevOps Guru for RDS uses statistical and machine learning techniques to mine this data and detect anomalies\. To learn more about telemetry data, see [Monitoring DB load with Performance Insights on Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.html) and [Monitoring the OS by using Enhanced Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_Monitoring.OS.html) in the *Amazon Aurora User Guide*\.

**Fast resolution**  
Each anomaly identifies the performance issue and suggests avenues of investigation or corrective actions\. For example, DevOps Guru for RDS might recommend that you investigate specific wait events\. Or it might recommend that you tune your application pool settings to limit the number of database connections\. Based on these recommendations, you can resolve performance issues more quickly than by troubleshooting manually\.

**Deep knowledge of Amazon engineers**  
To detect performance issues and help you resolve bottlenecks, DevOps Guru for RDS relies on machine learning \(ML\)\. Amazon database engineers contributed to the development of the DevOps Guru for RDS findings, which encapsulate many years of managing hundreds of thousands of databases\. By drawing on this collective knowledge, DevOps Guru for RDS can teach you best practices\.

## How DevOps Guru for RDS works<a name="devops-guru-for-rds.how-it-works"></a>

DevOps Guru for RDS collects data about your Aurora databases from Amazon RDS Performance Insights\. The most important metric is `DBLoad`\. DevOps Guru for RDS consumes the Performance Insights metrics, analyzes them with machine learning, and publishes insights to the dashboard\.

### Insights<a name="devops-guru-for-rds.how-it-works.insights"></a>

An *insight* is a collection of related anomalies that were detected by DevOps Guru\. If DevOps Guru for RDS finds performance issues in your Amazon Aurora DB instances, it publishes an insight in the DevOps Guru dashboard\. To learn more about insights, see [Working with insights in DevOps Guru](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-insights.html) in the *Amazon DevOps Guru User Guide*\.

### Anomalies<a name="devops-guru-for-rds.how-it-works.anomalies"></a>

In DevOps Guru for RDS, an *anomaly* is a pattern that deviates from what is considered normal performance for your Amazon Aurora database\. 

#### Causal anomalies<a name="devops-guru-for-rds.how-it-works.anomalies.causal"></a>

A *causal anomaly* is a top\-level anomaly within an insight\. **Database load \(DB load\)** is the causal anomaly for DevOps Guru for RDS\. 

An anomaly measures performance impact by assigning a severity level of **High**, **Medium**, or **Low**\. To learn more, see [Key concepts for DevOps Guru for RDS](https://docs.aws.amazon.com/http://lashdown-cap-ug-rds/working-with-rds.overview.definitions.html) in the *Amazon DevOps Guru User Guide*\.

If DevOps Guru detects an anomaly on your DB instance, you're alerted in the **Databases** page of the RDS console\. To go to the anomaly page from the RDS console, choose the link in the alert message\.

![\[DevOps Guru for RDS anomaly reported in the Databases page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-devops-guru-dbpage-alert.png)

 The RDS console also alerts you in the page for your Amazon Aurora cluster\.

![\[DevOps Guru for RDS anomaly reported in the page for your Amazon Aurora cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-devops-guru-alert.png)

#### Contextual anomalies<a name="devops-guru-for-rds.how-it-works.anomalies.contextual"></a>

A *contextual anomaly* is a finding within **Database load \(DB load\)**\. Each contextual anomaly describes a specific Amazon Aurora performance issue that requires investigation\. For example, DevOps Guru for RDS might recommend that you consider increasing CPU capacity or investigate wait events that are contributing to DB load\. [Amazon Aurora versions](Aurora.VersionPolicy.md)

**Important**  
We recommend that you test any changes on a test instance before modifying a production instance\. In this way, you understand the impact of the change\.

To learn more, see [Analyzing anomalies in Amazon Aurora clusters](https://docs.aws.amazon.com/cap-ug-rds/working-with-rds.analyzing.html) in the *Amazon DevOps Guru User Guide*\.

## Setting up DevOps Guru for RDS<a name="devops-guru-for-rds.configuring"></a>

To allow DevOps Guru for RDS to publish insights for an Amazon Aurora database, complete the following tasks\.

**Topics**
+ [Turning on Performance Insights for your Amazon Aurora DB instances](#devops-guru-for-rds.configuring.performance-insights)
+ [Configuring access policies for DevOps Guru for RDS](#devops-guru-for-rds.configuring.access)
+ [Adding Amazon Aurora resources to your DevOps Guru coverage](#devops-guru-for-rds.configuring.coverage)

### Turning on Performance Insights for your Amazon Aurora DB instances<a name="devops-guru-for-rds.configuring.performance-insights"></a>

DevOps Guru for RDS relies on Performance Insights for its data\. Without Performance Insights, DevOps Guru publishes anomalies, but doesn't include the detailed analysis and recommendations\. 

When you create or modify a DB instance, you can turn on Performance Insights\. For more information, see [Enabling and disabling Performance Insights](USER_PerfInsights.Enabling.md)\.

### Configuring access policies for DevOps Guru for RDS<a name="devops-guru-for-rds.configuring.access"></a>

For your IAM user or role to access DevOps Guru for RDS, it must have either of the following policies:
+ The AWS managed policy `AmazonRDSFullAccess`
+ A customer managed policy that allows the following actions:
  + `pi:GetResourceMetrics`
  + `pi:DescribeDimensionKeys`
  + `pi:GetDimensionKeyDetails`

For more information, see [Configuring access policies for Performance Insights](USER_PerfInsights.access-control.md)\.

### Adding Amazon Aurora resources to your DevOps Guru coverage<a name="devops-guru-for-rds.configuring.coverage"></a>

To set up DevOps Guru for the first time, perform the following steps:

1. Sign up to AWS if you aren't already signed up\.

1. Determine coverage for your resources\.

   To allow DevOps Guru for RDS to generate anomalies for your Amazon Aurora DB instances, specify the instances that you want to be covered\. By default, DevOps Guru analyzes all supported AWS resources in your AWS Region and account\. You can also specify individual resources by using AWS CloudFormation stacks or applying tags\. To learn more, see [Adding Amazon Aurora resources to your DevOps Guru coverage](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-rds.enabling.html#working-with-rds.enabling.cf) in the *Amazon DevOps Guru User Guide*\.

1. Identify Amazon SNS topics\.

   Use one or two Amazon SNS topics to generate notifications about important DevOps Guru for RDS events\. An example is when an insight is created for an Amazon Aurora DB instance\. In this way, you know about issues that DevOps Guru for RDS finds as soon as possible\. To learn more, see [Identify your Amazon SNS notifications topic](https://docs.aws.amazon.com/devops-guru/latest/userguide/setting-up.html#setting-up-notifications) in the *Amazon DevOps Guru User Guide*\.

For more information, see [Setting up Amazon DevOps Guru](https://docs.aws.amazon.com/devops-guru/latest/userguide/setting-up.html) in the *Amazon DevOps Guru User Guide*\.