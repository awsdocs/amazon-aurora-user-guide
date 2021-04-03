# Database Query Metadata Service \(DBQMS\) API reference<a name="dbqms-api"></a>

The Database Query Metadata Service \(`dbqms`\) is an internal\-only service\. It provides your recent and saved queries for the query editor on the AWS Management Console for multiple AWS services, including Amazon RDS\.

The following DBQMS actions are supported:

**Topics**
+ [CreateFavoriteQuery](#CreateFavoriteQuery)
+ [CreateQueryHistory](#CreateQueryHistory)
+ [CreateTab](#CreateTab)
+ [DeleteFavoriteQueries](#DeleteFavoriteQueries)
+ [DeleteQueryHistory](#DeleteQueryHistory)
+ [DeleteTab](#DeleteTab)
+ [DescribeFavoriteQueries](#DescribeFavoriteQueries)
+ [DescribeQueryHistory](#DescribeQueryHistory)
+ [DescribeTabs](#DescribeTabs)
+ [GetQueryString](#GetQueryString)
+ [UpdateFavoriteQuery](#UpdateFavoriteQuery)
+ [UpdateQueryHistory](#UpdateQueryHistory)
+ [UpdateTab](#UpdateTab)

## CreateFavoriteQuery<a name="CreateFavoriteQuery"></a>

Save a new favorite query\. Each IAM user can create up to 1000 saved queries\. This limit is subject to change in the future\.

## CreateQueryHistory<a name="CreateQueryHistory"></a>

Save a new query history entry\.

## CreateTab<a name="CreateTab"></a>

Save a new query tab\. Each IAM user can create up to 10 query tabs\.

## DeleteFavoriteQueries<a name="DeleteFavoriteQueries"></a>

Delete one or more saved queries\.

## DeleteQueryHistory<a name="DeleteQueryHistory"></a>

Delete query history entries\.

## DeleteTab<a name="DeleteTab"></a>

Delete query tab entries\.

## DescribeFavoriteQueries<a name="DescribeFavoriteQueries"></a>

List saved queries created by an IAM user in a given account\.

## DescribeQueryHistory<a name="DescribeQueryHistory"></a>

List query history entries\.

## DescribeTabs<a name="DescribeTabs"></a>

List query tabs created by an IAM user in a given account\.

## GetQueryString<a name="GetQueryString"></a>

Retrieve full query text from a query ID\.

## UpdateFavoriteQuery<a name="UpdateFavoriteQuery"></a>

Update the query string, description, name, or expiration date\.

## UpdateQueryHistory<a name="UpdateQueryHistory"></a>

Update the status of query history\.

## UpdateTab<a name="UpdateTab"></a>

Update the query tab name and query string\.