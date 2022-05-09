# Metrics reference for Amazon Aurora<a name="metrics-reference"></a>

In this reference, you can find descriptions of Amazon Aurora metrics for Amazon CloudWatch, Performance Insights, and Enhanced Monitoring\.

**Topics**
+ [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md)
+ [Amazon CloudWatch dimensions for Aurora](dimensions.md)
+ [Availability of Aurora metrics in the Amazon RDS console](Aurora.Monitoring.Metrics.RDSAvailability.md)
+ [Amazon CloudWatch metrics for Performance Insights](USER_PerfInsights.Cloudwatch.md)
+ [Performance Insights counter metrics](USER_PerfInsights_Counters.md)
+ [SQL statistics for Performance Insights](#sql-statistics)
+ [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)

## SQL statistics for Performance Insights<a name="sql-statistics"></a>

*SQL statistics* are performance\-related metrics about SQL queries that are collected by Performance Insights\. Performance Insights gathers statistics for each second that a query is running and for each SQL call\. All engines support statistics for digest queries\. All engines support statement\-level statistics except for Aurora PostgreSQL\.

**Topics**
+ [SQL statistics for Aurora MySQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.md)
+ [SQL statistics for Aurora PostgreSQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.md)