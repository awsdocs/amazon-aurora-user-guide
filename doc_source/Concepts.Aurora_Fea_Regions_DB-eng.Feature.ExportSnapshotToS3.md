# Exporting snapshot data to Amazon S3<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3"></a>

You can export Aurora DB cluster snapshot data to an Amazon S3 bucket\. You can export manual snapshots and automated system snapshots\. After the data is exported, you can analyze the exported data directly through tools like Amazon Athena or Amazon Redshift Spectrum\. For more information, see [Exporting DB cluster snapshot data to Amazon S3](aurora-export-snapshot.md)\.

Exporting snapshots to S3 is available in all AWS Regions except the following:
+ Asia Pacific \(Jakarta\)
+ Middle East \(UAE\)
+ Europe \(Spain\)
+ Europe \(Zurich\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

**Topics**
+ [Exporting snapshot data to S3 with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.ams)
+ [Exporting snapshot data to S3 with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.apg)

## Exporting snapshot data to S3 with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.ams"></a>

All currently available Aurora MySQL engine versions support exporting DB cluster snapshot data to Amazon S3\. For more information about versions, see [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html)\.

## Exporting snapshot data to S3 with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.apg"></a>

All currently available Aurora PostgreSQL engine versions support exporting DB cluster snapshot data to Amazon S3\. For more information about versions, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)\.