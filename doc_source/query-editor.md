# Using the Query Editor for Aurora Serverless<a name="query-editor"></a>

With the query editor for Aurora Serverless, you can run SQL queries in the RDS console\. You can run any valid SQL statement on the Aurora Serverless DB cluster, including data manipulation and data definition statements\.

The query editor requires an Aurora Serverless DB cluster with the Data API enabled\. For information about creating an Aurora Serverless DB cluster with the Data API enabled, see [Using the Data API for Aurora Serverless](data-api.md)\.

## Availability of the Query Editor<a name="query-editor.regions"></a>

The query editor is only available for the following Aurora Serverless DB clusters:
+ Aurora with MySQL version 5\.6 compatibility
+ Aurora with MySQL version 5\.7 compatibility
+ Aurora with PostgreSQL version 10\.7 compatibility

The query editor is currently available for Aurora Serverless in the following AWS Regions:
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Asia Pacific \(Mumbai\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Paris\)
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(N\. California\)
+ US West \(Oregon\)

## Authorizing Access to the Query Editor<a name="query-editor.access"></a>

A user must be authorized to run queries in the query editor\. You can authorize a user to run queries in the query editor by adding the `AmazonRDSDataFullAccess` policy, a predefined AWS Identity and Access Management \(IAM\) policy, to that user\.

You can also create an IAM policy that grants access to the query editor\. After you create the policy, add it to each user that requires access to the query editor\.

The following policy provides the minimum required permissions for a user to access the query editor\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "QueryEditor0",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutResourcePolicy",
                "secretsmanager:PutSecretValue",
                "secretsmanager:DeleteSecret",
                "secretsmanager:DescribeSecret",
                "secretsmanager:TagResource"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:rds-db-credentials/*"
        },
        {
            "Sid": "QueryEditor1",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetRandomPassword",
                "tag:GetResources",
                "secretsmanager:CreateSecret",
                "secretsmanager:ListSecrets",
                "dbqms:CreateFavoriteQuery",
                "dbqms:DescribeFavoriteQueries",
                "dbqms:UpdateFavoriteQuery",
                "dbqms:DeleteFavoriteQueries",
                "dbqms:GetQueryString",
                "dbqms:CreateQueryHistory",
                "dbqms:UpdateQueryHistory",
                "dbqms:DeleteQueryHistory",
                "dbqms:DescribeQueryHistory",
                "rds-data:BatchExecuteStatement",
                "rds-data:BeginTransaction",
                "rds-data:CommitTransaction",
                "rds-data:ExecuteStatement",
                "rds-data:RollbackTransaction"
            ],
            "Resource": "*"
        }
    ]
}
```

For information about creating an IAM policy, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *AWS Identity and Access Management User Guide*\.

For information about adding an IAM policy to a user, see [Adding and Removing IAM Identity Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *AWS Identity and Access Management User Guide*\.

## Running Queries in the Query Editor<a name="query-editor.running"></a>

You can run SQL statements on an Aurora Serverless DB cluster in the query editor\.

**To run a query in the query editor**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the Aurora Serverless DB clusters that you want to query\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Aurora Serverless DB cluster that you want to run SQL queries against\.

1. For **Actions**, choose **Query**\. If you haven't connected to the database before, the **Connect to database** page opens\.  
![\[Query editor Connect to database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/query-editor-connect.png)

1. Enter the following information:

   1. For **Database instance or cluster**, choose the Aurora Serverless DB cluster that you want to run SQL queries on\.

   1. For **Database username**, choose the user name of the database user to connect with, or choose **Add new database credentials**\. If you choose **Add new database credentials**, enter the user name for the new database credentials in **Enter database username**\.

   1. For **Enter database password**, enter the password for the database user that you chose\.

   1. In the last box, enter the name of the database or schema that you want to use for the Aurora DB cluster\.

   1. Choose **Connect to database**\.
**Note**  
If your connection is successful, your connection and authentication information are stored in AWS Secrets Manager\. You don't need to enter the connection information again\.

1. In the query editor, enter the SQL query that you want to run on the database\.  
![\[Query editor\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/query-editor.png)

   Each SQL statement can commit automatically, or you can run SQL statements in a script as part of a transaction\. To control this behavior, choose the gear icon above the query window\.   
![\[Gear icon in Query editor\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/query-editor-gear.png)

   The **Query Editor Settings** window appears\.  
![\[Query Editor Settings\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/query-editor-settings.png)

   If you choose **Auto\-commit**, every SQL statement commits automatically\. If you choose **Transaction**, you can run a group of statements in a script\. Statements are automatically committed at the end of the script unless explicitly committed or rolled back before then\. Also, you can choose to stop a running script if an error occurs by enabling **Stop on error**\.
**Note**  
In a group of statements, data definition language \(DDL\) statements can cause previous data manipulation language \(DML\) statements to commit\. You can also include `COMMIT` and `ROLLBACK` statements in a group of statements in a script\.

   After you make your choices in the **Query Editor Settings** window, choose **Save**\.

1. Choose **Run** or press Ctrl\+Enter, and the query editor displays the results of your query\.

   After running the query, save it to **Saved queries** by choosing **Save**\.

   Export the query results to spreadsheet format by choosing **Export to csv**\.

You can find, edit, and rerun previous queries\. To do so, choose the **Recent** tab or the **Saved queries** tab, choose the query text, and then choose **Run**\.

To change the database, choose **Change database**\.