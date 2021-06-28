# Getting the status of a database activity stream<a name="DBActivityStreams.Status"></a>

You can get the status of an activity stream using the console or AWS CLI\.

## Console<a name="DBActivityStreams.Status-collapsible-section-S1"></a>

**To get the status of a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster link\.

1. Choose the **Configuration** tab, and check **Database activity stream** for status\.

## AWS CLI<a name="DBActivityStreams.Status-collapsible-section-S2"></a>

You can get the activity stream configuration for a DB cluster as the response to a [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI request\. In the following example, see the values for `ActivityStreamKinesisStreamName`, `ActivityStreamStatus`, `ActivityStreamKmsKeyId`, and `ActivityStreamMode`\.

The request is as follows\.

```
aws rds --region MY_REGION describe-db-clusters --db-cluster-identifier my-cluster
```

The response includes the following items for a database activity stream\.

The following example shows a JSON response\. These fields are the same for Aurora PostgreSQL and Aurora MySQL, except that `ActivityStreamMode` is always `async` for Aurora MySQL, while for Aurora PostgreSQL it might be `sync` or `async`\.

```
{
    "DBClusters": [
        {
      "DBClusterIdentifier": "my-cluster",
            ...
            "ActivityStreamKinesisStreamName": "aws-rds-das-cluster-A6TSYXITZCZXJHIRVFUBZ5LTWY",
            "ActivityStreamStatus": "starting",
            "ActivityStreamKmsKeyId": "12345678-abcd-efgh-ijkl-bd041f170262",
            "ActivityStreamMode": "async",
            "DbClusterResourceId": "cluster-ABCD123456"
            ...
        }
    ]
}
```

## RDS API<a name="DBActivityStreams.Status-collapsible-section-S3"></a>

You can get the activity stream configuration for a DB cluster as the response to a [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) operation\.