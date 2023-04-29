# Babelfish version updates<a name="babelfish-information"></a>

Babelfish is an option available with Aurora PostgreSQL version 13\.4 and higher releases\. Updates to Babelfish become available with certain new releases of the Aurora PostgreSQL database engine\. For more information, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\. 

**Note**  
Babelfish DB clusters running on any version of Aurora PostgreSQL 13 can't be upgraded to Aurora PostgreSQL 14\.3, 14\.4, and 14\.5\. Also, Babelfish doesn't support a direct upgrade from 13\.x to 15\.x\. You must first upgrade your 13\.x DB cluster to 14\.6 and higher version and then upgrade to 15\.x version\.

For a list of supported functionality across different Babelfish releases, see [Supported functionality in Babelfish by version](babelfish-compatibility.supported-functionality-table.md)\. 

For a list of currently unsupported functionality, see [Unsupported functionality in Babelfish](babelfish-compatibility.tsql.limitations-unsupported.md)\.

You can use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command to get a list of Aurora PostgreSQL versions in your AWS Region that support Babelfish, as shown in the following example\. 

For Linux, macOS, or Unix:

```
$ aws rds describe-db-engine-versions --region us-east-1 \
    --engine aurora-postgresql \
    --query '*[]|[?SupportsBabelfish==`true`].[EngineVersion]' \
    --output text
13.4
13.5
13.6
13.7
13.8
14.3
14.4
14.5
14.6
14.7
15.2
```

For more information, see [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) in the *AWS CLI Command Reference*\.

In the following topics, you can learn how to identify the version of Babelfish running on your Aurora PostgreSQL DB cluster, and how to upgrade to a new version\.

**Contents**
+ [Identifying your version of Babelfish](babelfish-information-identify-version.md)
+ [Upgrading your Babelfish cluster to a new version](babelfish-information-upgrading.md)
  + [Upgrading Babelfish to a new minor version](babelfish-information-upgrading.md#babelfish-information-upgrading-minor)
  + [Upgrading Babelfish to a new major version](babelfish-information-upgrading.md#babelfish-information-upgrading-major)
    + [Before upgrading Babelfish to a new major version](babelfish-information-upgrading.md#babelfish-information-upgrading-preliminary)
    + [Performing major version upgrade](babelfish-information-upgrading.md#babelfish-performing-major-version-upgrade)
    + [After upgrading to a new major version](babelfish-information-upgrading.md#babelfish-information-upgrading-post-upgrade)
    + [Example: Upgrading the Babelfish DB cluster to a major release](babelfish-information-upgrading.md#babelfish-information-upgrading-example)
+ [Using Babelfish product version parameter](babelfish-guc-version.md)
  + [Configuring Babelfish product version parameter](babelfish-guc-version.md#babelfish-guc-version-setvalues)
  + [Affected queries and parameter](babelfish-guc-version.md#babelfish-guc-version-affects)
  + [Interface with babelfishpg\_tsql\.version parameter](babelfish-guc-version.md#babelfish-guc-version-tsql)