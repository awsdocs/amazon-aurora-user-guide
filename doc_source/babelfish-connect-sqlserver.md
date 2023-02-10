# Using a SQL Server client to connect to your DB cluster<a name="babelfish-connect-sqlserver"></a>

You can use a SQL Server client to connect with Babelfish on the TDS port\. As of Babelfish 2\.1\.0 and higher releases, you can use the SSMS Object Explorer or the SSMS Query Editor to connect to your Babelfish cluster\. 

**Limitations**
+ In Babelfish 2\.1\.0 and older versions, using `PARSE` to check SQL syntax doesn't work as it should\. Rather than checking the syntax without running the query, the `PARSE` command runs the query but doesn't display any results\. Using the SMSS <Ctrl><F5> key combination to check syntax has the same anomalous behavior, that is, Babelfish unexpectedly runs the query without providing any output\. 
+ Babelfish doesn't support MARS \(Multiple Active Result Sets\)\. Be sure that any client applications that you use to connect to Babelfish aren't set to use MARS\. 
+ For Babelfish 1\.3\.0 and older versions, only the Query Editor is supported for SSMS\. To use SSMS with Babelfish, be sure to open the Query Editor connection dialog in SSMS, and not the Object Explorer\. If the Object Explorer dialog does open, cancel the dialog and re\-open the Query Editor\. In the following image, you can find the menu options to choose when connecting to Babelfish 1\.3\.0 or older versions\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/babelfish_connect_ssms.png)

For more information about interoperability and behavioral differences between SQL Server and Babelfish, see [Differences between Babelfish for Aurora PostgreSQL and SQL Server](babelfish-compatibility.md)\.

## Using sqlcmd to connect to the DB cluster<a name="babelfish-connect-sqlcmd"></a>

You can connect to and interact with an Aurora PostgreSQL DB cluster that supports Babelfish by using the SQL Server `sqlcmd` command line client\. Use the following command to connect\.

```
sqlcmd -S endpoint,port -U login-id -P password -d your-DB-name
```

The options are as follows:
+ `-S` is the endpoint and \(optional\) TDS port of the DB cluster\.
+ `-U` is the login name of the user\.
+ `-P` is the password associated with the user\.
+ `-d` is the name of your Babelfish database\.

After connecting, you can use many of the same commands that you use with SQL Server\. For some examples, see [Getting information from the Babelfish system catalog](babelfish-query-database.md)\.

## Using SSMS to connect to the DB cluster<a name="babelfish-connect-SSMS"></a>

You can connect to an Aurora PostgreSQL DB cluster running Babelfish by using Microsoft SQL Server Management Studio \(SSMS\)\. SSMS includes a variety of tools, including the SQL Server Import amd Export Wizard discussed in [Migrating a SQL Server database to Babelfish for Aurora PostgreSQL](babelfish-migration.md)\. For more information about SSMS, see [ Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16) in the Microsoft documentation\.

**To connect to your Babelfish database with SSMS**

1. Start SSMS\.

1. Open the **Connect to Server** dialog box\. To continue with the connection, do one of the following:
   + Choose **New Query**\.
   + If the Query Editor is open, choose **Query**, **Connection**, **Connect**\.

1. Provide the following information for your database:

   1. For **Server type**, choose **Database Engine**\.

   1. For **Server name**, enter the DNS name\. For example, your server name should look similar to the following\.

      ```
      cluster-name.cluster-555555555555.aws-region.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\.

   1. For **Login**, enter the user name that you chose when you created your database\.

   1. For **Password**, enter the password that you chose when you created your database\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-SSMS-connect-database1.png)

1. \(Optional\) Choose **Options**, and then choose the **Connection Properties** tab\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-SSMS-connect-database2.png)

1. \(Optional\) For **Connect to database**, specify the name of the migrated SQL Server database to connect to, and choose **Connect**\.

   If a message appears indicating that SSMS can't apply connection strings, choose **OK**\.

   If you are having trouble connecting to Babelfish, see [Connection failure](babelfish-troubleshooting.md#babelfish-troubleshooting-connectivity)\.

   For more information about SQL Server connection issues, see [Troubleshooting connections to your SQL Server DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToMicrosoftSQLServerInstance.html#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting) in the *Amazon RDS User Guide*\.