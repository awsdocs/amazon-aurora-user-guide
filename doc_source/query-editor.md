# Using the Query Editor for Aurora Serverless<a name="query-editor"></a>


|  | 
| --- |
| The query editor is in beta for Aurora Serverless and is subject to change\. | 

With the query editor for Aurora Serverless, you can run SQL queries in the RDS console\. You can run any valid SQL statement on the Aurora Serverless DB cluster, including data manipulation and data definition statements\.

The query editor requires an Aurora Serverless DB cluster with the Data API enabled\. For information about creating an Aurora Serverless DB cluster with the Data API enabled, see [Using the Data API for Aurora Serverless](data-api.md)\.

## Authorizing Access to the Query Editor<a name="query-editor.access"></a>

A user must be authorized to run queries in the query editor\. You can authorize a user to run queries in the query editor by adding the `AmazonRDSDataFullAccess` predefined AWS Identity and Access Management \(IAM\) policy to the user\.

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
                "rds-data:ExecuteSql"
            ],
            "Resource": "*"
        }
    ]
}
```

For information about creating an IAM policy, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *AWS Identity and Access Management User Guide*\.

For information about adding an IAM policy to a user, see [Adding and Removing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *AWS Identity and Access Management User Guide*\.

## Running Queries in the Query Editor<a name="query-editor.running"></a>

You can run SQL statements on an Aurora Serverless DB cluster in the query editor\.

**To run a query in the query editor**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the Aurora Serverless DB clusters that you want to query\.

1. In the navigation pane, choose **Clusters**\.

1. Choose the Aurora Serverless DB cluster that you want to run SQL queries against\.

1. For **Actions**, choose **Query**\.

1. If you haven't connected to the database before, the **Connect to database** page opens\.  
![\[Query editor Connect to database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/query-editor-connect.png)

   1. For **Database cluster**, choose the Aurora DB cluster that you want to run SQL queries on\.

   1. For **Database username**, choose the user name of the database user to connect with\.

   1. For **Password**, enter the password for the database user that you chose\.

   1. In the last box, enter the name of the schema that you want to use for the Aurora DB cluster\.

   1. Choose **Connect to database**\.
**Note**  
If your connection is successful, your connection and authentication information are stored in AWS Secrets Manager\. You don't need to enter the connection information again\.

1. In the query editor, enter the SQL query you want to run on the database\.  
![\[Query editor\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/query-editor.png)

1. Choose **Run** or press Ctrl\+Enter, and the query editor displays the results of your query\.

   After running the query, save it to **Saved queries** by choosing **Save As** or **Save**\.

   Export the query results to spreadsheet format by choosing **Export**\.

After you run queries and save queries, you can rerun them\. To do so, choose the **History** tab or the **Saved queries** tab, choose the query, and then choose **Run**\.