# Tuning Aurora MySQL<a name="AuroraMySQL.Managing.Tuning"></a>

Wait events and thread states are important tuning tools for Aurora MySQL\. If you can find out why sessions are waiting for resources and what they are doing, you are better able to reduce bottlenecks\. You can use the information in this section to find possible causes and corrective actions\.

Amazon DevOps Guru for RDS can proactively determine whether your Aurora MySQL databases are experiencing problematic conditions that might cause bigger problems later\. Amazon DevOps Guru for RDS publishes an explanation and recommendations for corrective actions in a proactive insight\. This section contains insights for common problems\.

**Important**  
The wait events and thread states in this section are specific to Aurora MySQL\. Use the information in this section to tune only Amazon Aurora, not Amazon RDS for MySQL\.  
Some wait events in this section have no analogs in the open source versions of these database engines\. Other wait events have the same names as events in open source engines, but behave differently\. For example, Amazon Aurora storage works different from open source storage, so storage\-related wait events indicate different resource conditions\.

**Topics**
+ [Essential concepts for Aurora MySQL tuning](AuroraMySQL.Managing.Tuning.concepts.md)
+ [Tuning Aurora MySQL with wait events](AuroraMySQL.Managing.Tuning.wait-events.md)
+ [Tuning Aurora MySQL with thread states](AuroraMySQL.Managing.Tuning.thread-states.md)
+ [Tuning Aurora MySQL with Amazon DevOps Guru proactive insights](MySQL.Tuning.proactive-insights.md)