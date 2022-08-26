# Amazon Aurora PostgreSQL releases and engine versions<a name="AuroraPostgreSQL.Updates.20180305"></a>

Amazon Aurora PostgreSQL\-Compatible Edition releases are updated regularly\. Updates are applied to Aurora PostgreSQL DB clusters during system maintenance windows\. When updates are applied depends on the type of update, the AWS Region, and maintenance window setting for the DB cluster\. Many of the listed releases include both a PostgreSQL version number and an Amazon Aurora version number\. However, starting with the release of PostgreSQL versions 13\.3, 12\.8, 11\.13, 10\.18, and for all other later versions, Aurora version numbers aren't used\. To identify the version numbers of your Aurora PostgreSQL database, see [Identifying versions of Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.Versions.md)\. 

For information about extensions and modules, see [Extension versions for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Extensions.md)\.

**Note**  
For information about Amazon Aurora version policies and release timelines, see [How long Amazon Aurora major versions remain available](Aurora.VersionPolicy.md#Aurora.VersionPolicy.MajorVersionLifetime)\. 

For information about support for Amazon Aurora see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\. 

To determine which Aurora PostgreSQL DB engine versions are available in an AWS Region, use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\. For example:

```
aws rds describe-db-engine-versions --engine aurora-postgresql --query '*[].[EngineVersion]' --output text --region aws-region
```

For a list of AWS Regions, see [Aurora PostgreSQL Region availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability.PostgreSQL)\.

For details about the PostgreSQL versions that are available on Aurora PostgreSQL, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\.