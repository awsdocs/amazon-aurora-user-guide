# Viewing SQL statistics in the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.AnalyzingSQLLevel"></a>

In the Performance Insights dashboard, SQL statistics are available in the **Top SQL** tab of the **Database load** chart\.

**To view SQL statistics**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Performance Insights**\.

1. At the top of the page, choose the database whose SQL statistics you want to see\.

1. Scroll to the bottom of the page and choose the **Top SQL** tab\.

1. Choose an individual statement \(Aurora MySQL only\) or digest query\.  
![\[Viewing metrics for running queries\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/./images/perf_insights_per_sql_digest.png)

1. Choose which statistics to display by choosing the gear icon in the upper\-right corner of the chart\. For descriptions of the SQL statistics for the Amazon RDSAurora engines, see [SQL statistics for Performance Insights](sql-statistics.md)\.

   The following example shows the preferences for Aurora PostgreSQL\.  
![\[Preferences for metrics for running queries for Aurora PostgreSQL DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/./images/perf_insights_per_sql_pref_apg.png)

   The following example shows the preferences for Aurora MySQL DB instances\.  
![\[Preferences for metrics for running queries for Aurora MySQL DB instances.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/./images/perf_insights_per_sql_pref_ams.png)

1. Choose Save to save your preferences\.

   The **Top SQL** table refreshes\.