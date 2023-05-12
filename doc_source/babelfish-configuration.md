# DB cluster parameter group settings for Babelfish<a name="babelfish-configuration"></a>

When you create an Aurora PostgreSQL DB cluster and choose **Turn on Babelfish**, a DB cluster parameter group is created for you automatically if you choose **Create new**\. This DB cluster parameter group is based on the Aurora PostgreSQL DB cluster parameter group for the Aurora PostgreSQL version chosen for the install, for example, Aurora PostgreSQL version 14\. It's named using the following general pattern: 

```
custom-aurora-postgresql14-babelfish-compat-3
```

You can change the following settings during the cluster creation process but some of these can't be changed once they're stored in the custom parameter group, so choose carefully:
+ Single database or Multiple databases
+ Default collation locale
+ Collation name
+ Babelfish TDS port
+ DB parameter group

To use an existing Aurora PostgreSQL DB cluster version 13 or higher parameter group, edit the group and set the `babelfish_status` parameter to `on`\. Specify any Babelfish options before creating your Aurora PostgreSQL cluster\. To learn more, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

The following parameters control Babelfish preferences\. Unless otherwise stated in the Description, parameters are modifiable\. The default value is included in the description\. To see the allowable values for any parameter, do as follows: 

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Parameter groups** from the navigation menu\.

1. Choose the `default.aurora-postgresql14` DB cluster parameter group from the list\.

1. Enter the name of a parameter in the search field\. For example, enter `babelfishpg_tsql.default_locale` in the search field to display this parameter and its default value and allowable settings\. 


| Parameter | Description | 
| --- | --- | 
| babelfishpg\_tds\.tds\_default\_numeric\_scale | Sets the default scale of numeric type to be sent in the TDS column metadata if the engine doesn't specify one\. \(Default: 8\) \(Allowable: 0–38\)  | 
| babelfishpg\_tds\.tds\_default\_numeric\_precision | An integer that sets the default precision of numeric type to be sent in the TDS column metadata if the engine doesn't specify one\. \(Default: 38\) \(Allowable: 1–38\) | 
| babelfishpg\_tds\.tds\_default\_packet\_size | An integer that sets the default packet size for connecting SQL Server clients\. \(Default: 4096\) \(Allowable: 512–32767\) | 
| babelfishpg\_tds\.tds\_default\_protocol\_version | An integer that sets a default TDS protocol version for connecting clients\. \(Default: DEFAULT\) \(Allowable: TDSv7\.0, TDSv7\.1, TDSv7\.1\.1, TDSv7\.2, TDSv7\.3A, TDSv7\.3B, TDSv7\.4, DEFAULT\) | 
| babelfishpg\_tds\.default\_server\_name | A string that identifies the default name of the Babelfish server\. \(Default: Microsoft SQL Server\) \(Allowable: null\) | 
| babelfishpg\_tds\.enable\_tds\_debug\_log\_level | An integer that sets the logging level in TDS; 0 turns off logging\. \(Default: 1\) \(Allowable: 0, 1, 2, 3\) | 
| babelfishpg\_tds\.listen\_addresses | A string that sets the host name or IP address or addresses to listen for TDS on\. This parameter can't be modified after the Babelfish DB cluster is created\. \(Default: \* \) \(Allowable: null\)  | 
| babelfishpg\_tds\.port | An integer that specifies the TCP port used for requests in SQL Server syntax\. \(Default: 1433\) \(Allowable: 1–65535\) | 
| babelfishpg\_tds\.tds\_ssl\_encrypt | A boolean that turns encryption on \(0\) or off \(1\) for data traversing the TDS listener port\. For detailed information about using SSL for client connections, see [Babelfish SSL settings and client connections](#babelfish-ssl)\. \(Default: 0\) \(Allowable: 0, 1\) | 
| babelfishpg\_tds\.tds\_ssl\_max\_protocol\_version | A string that specifies the highest SSL/TLS protocol version to use for the TDS session\. \(Default: 'TLSv1\.2'\) \(Allowable: 'TLSv1', 'TLSv1\.1', 'TLSv1\.2'\) | 
| babelfishpg\_tds\.tds\_ssl\_min\_protocol\_version | A string that specifies the minimum SSL/TLS protocol version to use for the TDS session\. \(Default: 'TLSv1'\) \(Allowable: 'TLSv1', 'TLSv1\.1', 'TLSv1\.2'\) | 
| babelfishpg\_tds\.unix\_socket\_directories | A string that identifies the TDS server Unix socket directory\. This parameter can't be modified after the Babelfish DB cluster is created\. \(Default: /tmp\) \(Allowable: null\) | 
| babelfishpg\_tds\.unix\_socket\_group | A string that identifies the TDS server Unix socket group\. This parameter can't be modified after the Babelfish DB cluster is created\. \(Default: rdsdb\) \(Allowable: null\) | 
| babelfishpg\_tsql\.default\_locale |  A string that specifies the default locale used for Babelfish collations\. The default locale is the locale only and doesn't include any qualifiers\. Set this parameter when you provision a Babelfish DB cluster\. After the DB cluster is provisioned, changes to this parameter are ignored\. \(Default: en\_US\) \(Allowable: See [tables](babelfish-collations.md)\)   | 
| babelfishpg\_tsql\.migration\_mode |  A non\-modifiable list that specifies support for single\- or multiple user databases\. Set this parameter when you provision a Babelfish DB cluster\. After the DB cluster is provisioned, you can't modify this parameter's value\. \(Default: single\-db\) \(Allowable: single\-db, multi\-db,null\)  | 
| babelfishpg\_tsql\.server\_collation\_name |  A string that specifies the name of the collation used for server\-level actions\. Set this parameter when you provision a Babelfish DB cluster\. After the DB cluster is provisioned, don't modify the value of this parameter\. \(Default: bbf\_unicode\_general\_ci\_as\) \(Allowable: See [tables](babelfish-collations.md)\)  | 
| babelfishpg\_tsql\.version |  A string that sets the output of @@VERSION variable\. Don't modify this value for Aurora PostgreSQL DB clusters\. \(Default: null\) \(Allowable: default\)  | 
| rds\.babelfish\_status | A string that sets the state of Babelfish functionality\. When this parameter is set to `datatypesonly`, Babelfish is turned off but SQL Server data types are still available\. \(Default: off\) \(Allowable: on, off, datatypesonly\) | 
| unix\_socket\_permissions | An integer that sets the TDS server Unix socket permissions\. This parameter can't be modified after the Babelfish DB cluster is created\. \(Default: 0700\) \(Allowable: 0–511\) | 

## Babelfish SSL settings and client connections<a name="babelfish-ssl"></a>

When a client connects to the TDS port \(default `1433`\), Babelfish compares the Secure Sockets Layer \(SSL\) setting sent during the client handshake to the Babelfish SSL parameter setting \(`tds_ssl_encrypt`\)\. Babelfish then determines if a connection is allowed\. If a connection is allowed, encryption behavior is either enforced or not, depending on your parameter settings and the support for encryption offered by the client\.

The table following shows how Babelfish behaves for each combination\.


| Client SSL setting | Babelfish SSL setting | Connection allowed? | Value returned to client | 
| --- | --- | --- | --- | 
| ENCRYPT\_OFF | tds\_ssl\_encrypt=0 | Allowed, the login packet is encrypted | ENCRYPT\_OFF | 
| ENCRYPT\_OFF | tds\_ssl\_encrypt=1 | Allowed, the entire connection is encrypted | ENCRYPT\_REQ | 
| ENCRYPT\_ON | tds\_ssl\_encrypt=0 | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_ON | tds\_ssl\_encrypt=1 | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_NOT\_SUP | tds\_ssl\_encrypt=0 | Yes | ENCRYPT\_NOT\_SUP | 
| ENCRYPT\_NOT\_SUP | tds\_ssl\_encrypt=1 | No, connection closed  | ENCRYPT\_REQ | 
| ENCRYPT\_REQ | tds\_ssl\_encrypt=0 | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_REQ | tds\_ssl\_encrypt=1 | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_CLIENT\_CERT | tds\_ssl\_encrypt=0 | No, connection closed | Unsupported | 
| ENCRYPT\_CLIENT\_CERT | tds\_ssl\_encrypt=1 | No, connection closed | Unsupported | 