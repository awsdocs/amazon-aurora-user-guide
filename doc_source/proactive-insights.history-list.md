# The InnoDB history list length increased significantly<a name="proactive-insights.history-list"></a>

Starting on *date*, your history list for row changes increased significantly, up to *length* on *db\-instance*\. This increase affects query and database shutdown performance\.

**Topics**
+ [Supported engine versions](#proactive-insights.history-list.context.supported)
+ [Context](#proactive-insights.history-list.context)
+ [Likely causes for this issue](#proactive-insights.history-list.causes)
+ [Actions](#proactive-insights.history-list.actions)
+ [Relevant metrics](#proactive-insights.history-list.metrics)

## Supported engine versions<a name="proactive-insights.history-list.context.supported"></a>

This insight information is supported for all versions of Aurora MySQL\.

## Context<a name="proactive-insights.history-list.context"></a>

The InnoDB transaction system maintains multiversion concurrency control \(MVCC\)\. When a row is modified, the pre\-modification version of the data being modified is stored as an undo record in an undo log\. Every undo record has a reference to its previous redo record, forming a linked list\.

The InnoDB history list is a global list of the undo logs for committed transactions\. MySQL uses the history list to purge records and log pages when transactions no longer require the history\. The history list length is the total number of undo logs that contain modifications in the history list\. Each log contains one or more modifications\. If the InnoDB history list length grows too large, indicating a large number of old row versions, queries and database shutdowns become slower\.

## Likely causes for this issue<a name="proactive-insights.history-list.causes"></a>

Typical causes of a long history list include the following:
+ Long\-running transactions, either read or write
+ A heavy write load

## Actions<a name="proactive-insights.history-list.actions"></a>

We recommend different actions depending on the causes of your insight\.

**Topics**
+ [Don't begin any operation involving a database shutdown until the InnoDB history list decreases](#proactive-insights.history-list.actions.no-shutdown)
+ [Identify and end long\-running transactions](#proactive-insights.history-list.actions.long-txn)
+ [Identify the top hosts and top users by using Performance Insights\.](#proactive-insights.history-list.actions.top-PI)

### Don't begin any operation involving a database shutdown until the InnoDB history list decreases<a name="proactive-insights.history-list.actions.no-shutdown"></a>

Because a long InnoDB history list slows database shutdowns, reduce the list size before initiating operations involving a database shutdown\. These operations include major version database upgrades\.

### Identify and end long\-running transactions<a name="proactive-insights.history-list.actions.long-txn"></a>

You can find long\-running transactions by querying `information_schema.innodb_trx`\.

**To identify and end long\-running transactions**

1. In your SQL client, run the following query:

   ```
   SELECT a.trx_id, 
         a.trx_state, 
         a.trx_started, 
         TIMESTAMPDIFF(SECOND,a.trx_started, now()) as "Seconds Transaction Has Been Open", 
         a.trx_rows_modified, 
         b.USER, 
         b.host, 
         b.db, 
         b.command, 
         b.time, 
         b.state 
   FROM  information_schema.innodb_trx a, 
         information_schema.processlist b 
   WHERE a.trx_mysql_thread_id=b.id
     AND TIMESTAMPDIFF(SECOND,a.trx_started, now()) > 10 
   ORDER BY trx_started
   ```

1. End each long\-running transaction with a `COMMIT` or `ROLLBACK` command\.

### Identify the top hosts and top users by using Performance Insights\.<a name="proactive-insights.history-list.actions.top-PI"></a>

Optimize transactions so that large numbers of modified rows are immediately committed\.

## Relevant metrics<a name="proactive-insights.history-list.metrics"></a>

The following metrics are related to this insight:
+ trx\_rseg\_history\_len

  For more information, see [InnoDB INFORMATION\_SCHEMA Metrics Table](https://dev.mysql.com/doc/refman/5.7/en/innodb-information-schema-metrics-table.html) in the *MySQL 5\.7 Reference Manual*\.