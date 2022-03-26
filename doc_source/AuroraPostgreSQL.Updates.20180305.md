# Amazon Aurora PostgreSQL releases and engine versions<a name="AuroraPostgreSQL.Updates.20180305"></a>

Amazon Aurora PostgreSQL\-Compatible Edition releases update regularly\. Updates are applied to Aurora PostgreSQL DB clusters during system maintenance windows\. The timing when updates are applied depends on the AWS Region and maintenance window setting for the DB cluster, and also the type of update\. Many of the listed releases include both a PostgreSQL version number and an Amazon Aurora version number\. However, starting with the release of PostgreSQL versions 13\.3, 12\.8, 11\.13, 10\.18, and for all other later versions, Aurora version numbers aren't used\. To determine the version numbers of your Aurora PostgreSQL database, see [Identifying versions of Amazon Aurora PostgreSQL](AuroraPostgreSQL.Updates.Versions.md)\.

For information about extensions and modules, see [Extension versions for Amazon Aurora PostgreSQL](AuroraPostgreSQL.Extensions.md)\.

Amazon Aurora for PostgreSQL 1\.X \(compatible with PostgreSQL 9\.6\.XX\) reaches end of life on January 31, 2022\. For more information, see [ Announcement: Amazon Aurora PostgreSQL 9\.6 End\-of\-Life date is January 31, 2022](http://forums.aws.amazon.com/ann.jspa?annID=8512)\. We recommend that you proactively upgrade your databases that are running Aurora PostgreSQL 9\.6 to Amazon Aurora PostgreSQL 10 or higher at your convenience before January 31, 2022\. To learn how, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 

For more information about Amazon Aurora available releases, policies, and timelines, see [How long Amazon Aurora major versions remain available](Aurora.VersionPolicy.md#Aurora.VersionPolicy.MajorVersionLifetime)\. For more information about support and other policies for Amazon Aurora see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\. 

To determine which Aurora PostgreSQL DB engine versions are available in an AWS Region, use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\. For example:

```
aws rds describe-db-engine-versions --engine aurora-postgresql --query '*[].[EngineVersion]' --output text --region aws-region
```

For a list of AWS Regions, see [Aurora PostgreSQL Region availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability.PostgreSQL)\.

For details about the PostgreSQL versions that are supported on Aurora PostgreSQL, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\.