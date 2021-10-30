# Using a SQL Server client to connect to your DB cluster<a name="babelfish-connect-sqlserver"></a>

You can use a SQL Server client to connect with Babelfish on the TDS port\.

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

After connecting, you can use many of the same commands that you use with SQL Server\. For some examples, see [Querying a database for object information](babelfish-query-database.md)\.

To review a list of exceptions, see [Differences between Aurora PostgreSQL with Babelfish and SQL Server](babelfish-compatibility.md)\.

## Using SSMS to connect to the DB cluster<a name="babelfish-connect-SSMS"></a>

In the following procedure, you connect to your Aurora PostgreSQL database by using Microsoft SQL Server Management Studio \(SSMS\)\. You can use the SSMS query editor to connect to a Babelfish database\.

You can't currently connect using the SSMS Object Explorer\.

**To connect to your Babelfish database with SSMS**

1. Start SSMS\.

1. Open the **Connect to Server** dialog box by doing one of the following:
   + Choose **New Query**\.
   + If the Query Editor is open, choose **Query**, **Connection**, **Connect**\.

1. Provide the following information for your database:

   1. For **Server type**, choose **Database Engine**\.

   1. For **Server name**, enter the DNS name\. For example, your server name should look similar to the following\.

      ```
      cluster-name.cluster-123abc456def.us-east-1-rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\.

   1. For **Login**, enter the user name that you chose when you created your database\.

   1. For **Password**, enter the password that you chose when you created your database\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-SSMS-connect-database1.png)

1. \(Optional\) Choose **Options**, and then choose the **Connection Properties** tab\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Babelfish-SSMS-connect-database2.png)

1. \(Optional\) For **Connect to database**, specify the name of the migrated SQL Server database to connect to, and choose **Connect**\.

   If a message appears indicating that SSMS can't apply connection strings, choose **OK**\.

   If you are having trouble connecting, see [Troubleshooting connections to your SQL Server DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToMicrosoftSQLServerInstance.html#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting) in the *Amazon RDS User Guide*\.