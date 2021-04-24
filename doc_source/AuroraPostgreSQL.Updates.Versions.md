# Identifying your versions of Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Updates.Versions"></a>

Amazon Aurora includes certain features that are general to Aurora and available to all Aurora DB clusters\. Aurora includes other features that are specific to a particular database engine that Aurora supports\. These features are available only to those Aurora DB clusters that use that database engine, such as Aurora PostgreSQL\.

An Aurora database has two version numbers, the Aurora version number and the database engine version number\. See also [Amazon Aurora PostgreSQL releases and engine versions](AuroraPostgreSQL.Updates.20180305.md) for the association between a specific Aurora version number and a specific PostgreSQL engine version number\.

## Aurora version number<a name="AuroraPostgreSQL.Updates.Versions.AuroraNumber"></a>

To get the Aurora version number of an Aurora PostgreSQL DB instance, use the following query\.

```
SELECT AURORA_VERSION();
```

Aurora version numbers use the following format: *major\-version*\.*minor\-version*\.*patch\-version*

## PostgreSQL engine version number<a name="AuroraPostgreSQL.Updates.Versions.EngineNumber"></a>

To get the database engine version number for an Aurora PostgreSQL DB instance, query for the `SERVER_VERSION` runtime parameter as shown following\. 

```
SHOW SERVER_VERSION;
```

The version numbering sequence for the PostgreSQL database engine is as follows: 

*major\.minor*  
For PostgreSQL versions 10 and later, the engine version number is in the form *major\.minor*\. The major version number is the integer part of the version number\. The minor version number is the fractional part of the version number\.   
A major version upgrade increases the integer part of the version number, such as upgrading from 10\.*minor* to 11\.*minor*\.

*major\.major\.minor*  
For PostgreSQL versions earlier than 10, the engine version number is in the form *major\.major\.minor*\. The major engine version number is both the integer and the first fractional part of the version number\. For example, 9\.6 is a major version\. The minor version number is the third part of the version number\. For example, for version 9\.6\.12, the 12 is the minor version number\.  
A major version upgrade increases the major part of the version number\. For example, an upgrade from *9\.6*\.12 to *10*\.7 is a major version upgrade, where *9\.6* and *10* are the major version numbers\.