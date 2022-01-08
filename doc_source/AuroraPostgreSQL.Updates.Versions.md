# Identifying versions of Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.Versions"></a>

Amazon Aurora includes certain features that are general to Aurora and available to all Aurora DB clusters\. Aurora includes other features that are specific to a particular database engine that Aurora supports\. These features are available only to those Aurora DB clusters that use that database engine, such as Aurora PostgreSQL\.

An Aurora database release typically has two version numbers, the database engine version number and the Aurora version number\. If an Aurora PostgreSQL release has an Aurora version number, it's included after the engine version number in the [Amazon Aurora PostgreSQL releases and engine versions](AuroraPostgreSQL.Updates.20180305.md) listing\. 

## Aurora version number<a name="AuroraPostgreSQL.Updates.Versions.AuroraNumber"></a>

Aurora version numbers use the *major*\.*minor*\.*patch* naming scheme\. An Aurora patch version includes important bug fixes added to a minor version after its release\. To learn more about Amazon Aurora major, minor, and patch releases, see [Amazon Aurora major versions](Aurora.VersionPolicy.md#Aurora.VersionPolicy.MajorVersions), [Amazon Aurora minor versions](Aurora.VersionPolicy.md#Aurora.VersionPolicy.MinorVersions), and [Amazon Aurora patch versions](Aurora.VersionPolicy.md#Aurora.VersionPolicy.PatchVersions)\. 

You can find out the Aurora version number of your Aurora PostgreSQL DB instance with the following SQL query:

```
pgres=> SELECT aurora_version();
```

Starting with the release of PostgreSQL versions 13\.3, 12\.8, 11\.13, 10\.18, and for all other later versions, Aurora version numbers align more closely to the PostgreSQL engine version\. For example, querying an Aurora PostgreSQL 13\.3 DB cluster returns the following:

```
aurora_version
----------------
 13.3.1
(1 row)
```

Prior releases, such as Aurora PostgreSQL 10\.14 DB cluster, return version numbers similar to the following:

```
aurora_version
----------------
 2.7.3
(1 row)
```

## PostgreSQL engine version numbers<a name="AuroraPostgreSQL.Updates.Versions.EngineNumber"></a>

Starting with PostgreSQL 10, PostgreSQL database engine versions use a *major*\.*minor* numbering scheme for all releases\. Some examples include PostgreSQL 10\.18, PostgreSQL 12\.7, and PostgreSQL 13\.3\. 

Releases prior to PostgreSQL 10 use a *major*\.*major*\.*minor* numbering scheme in which the first two digits make up the major version number and a third digit denotes a minor version\. For example, PostgreSQL 9\.6 is a major version, with minor versions 9\.6\.19 or 9\.6\.21 indicated by the third digit\. 

**Note**  
The PostgreSQL engine version 9\.6 is no longer supported\. To upgrade, see [Upgrading the PostgreSQL DB engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 

You can find out the PostgreSQL database engine version number with the following SQL query:

```
pgres=> SELECT version();
```

For an Aurora PostgreSQL 13\.3 DB cluster, the results are as follows:

```
version
-------------------------------------------------------------------------------------------------
 PostgreSQL 13.3 on x86_64-pc-linux-gnu, compiled by x86_64-pc-linux-gnu-gcc (GCC) 7.4.0, 64-bit
(1 row)
```