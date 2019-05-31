# Using Database Activity Streams with Aurora PostgreSQL<a name="DBActivityStreams"></a>

Monitoring your database activity can help you provide safeguards for your database and meet compliance and regulatory requirements\. One way of monitoring database activity with Amazon Aurora with PostgreSQL compatibility is to use database activity streams\. *Database activity streams* provide a near real\-time data stream of the database activity in your relational database\. When you integrate database activity streams with third\-party monitoring tools, you can monitor and audit database activity\. 

Beyond external security threats, managed databases need to provide protection against insider risks from database administrators \(DBAs\)\. Database activity streams protect your databases from internal threats by controlling DBA access to the database activity streams\. Thus, the collection, transmission, storage, and subsequent processing of the database activity stream is beyond the access of the DBAs that manage the database\. 

A database activity stream from Aurora PostgreSQL is pushed to an Amazon Kinesis data stream that is created on behalf of your database\. From Kinesis, the database activity stream can then be consumed by Amazon CloudWatch or by applications for compliance management\. These compliance applications include as Imperva's SecureSphere Database Audit and Protection, McAfee's Data Center Security Suite, or IBM's Infosphere Guardium\. These applications can use the database activity stream information to generate alerts and provide auditing of all activity on your Amazon Aurora databases\.

Database activity streams have the following limits and requirements:
+ Currently, these streams are supported only with Aurora with PostgreSQL compatibility version 2\.3, which is compatible with PostgreSQL version 10\.7\.
+ They're not supported in the following AWS Regions:
  + China \(Beijing\) Region, `cn-north-1`
  + China \(Ningxia\) Region, `cn-northwest-1`
  + AWS GovCloud \(US\-East\), `us-gov-east-1` 
  + AWS GovCloud \(US\-West\), `us-gov-west-1` 
+ They require use of AWS Key Management Service \(AWS KMS\)\.

**Topics**
+ [Starting a Database Activity Stream](#DBActivityStreams.Enabling)
+ [Getting the Status of a Database Activity Stream](#DBActivityStreams.Status)
+ [Stopping a Database Activity Stream](#DBActivityStreams.Disabling)
+ [Monitoring Database Activity Streams](#DBActivityStreams.Monitoring)
+ [Managing Access to Database Activity Streams](#DBActivityStreams.ManagingAccess)

## Starting a Database Activity Stream<a name="DBActivityStreams.Enabling"></a>

You start a database activity stream at the DB cluster level to monitor activity for all DB instances of the cluster\. Any DB instances added to the cluster are also automatically monitored\. 

When you start a database activity stream, each database activity event, such as a change or access, generates an activity stream event\. Access events are generated from SQL commands such as `CONNECT` and `SELECT`\. Change events are generated from SQL commands such as `CREATE` and `INSERT`\. To make each activity stream event durable, you encrypt and store it\. You can choose to have the database session handle database activity events either synchronously or asynchronously:
+ *Synchronous mode* – In synchronous mode, when a database session generates an activity stream event, the session blocks until the event is made durable\. If the event can't be made durable for some reason, the database session returns to normal activities\. However, an RDS event is sent indicating that activity stream records might be lost for some time\. A second RDS event is sent after the system is back to a healthy state\.

  The synchronous mode favors the accuracy of the database activity stream over database performance\. 
+ *Asynchronous mode* – In asynchronous mode, when a database session generates an activity stream event, the session returns to normal activities immediately\. In the background, the activity stream event is made a durable record\. If an error occurs in the background task, an RDS event is sent\. This event indicates the beginning and end of any time windows where activity stream event records might have been lost\.

  Asynchronous mode favors database performance over the accuracy of the database activity stream\. 

### Console<a name="DBActivityStreams.Enabling-collapsible-section-E1"></a>

**To start a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to use\.

1. For **Actions**, choose **Start activity stream**\. The **Database Activity Stream** window appears\. 

1. Enter the following settings in the **Database Activity Stream** window:
   + For **Master key**, choose a key from the list of AWS KMS keys\. 

     The master key is used to encrypt the key that in turn encrypts the database activity logged\. You must choose a master key other than the default key\. For more information about encryption keys and AWS KMS, see [What is AWS Key Management Service?](https://docs.aws.amazon.com//kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide\.*
   + For **Database activity stream mode**, choose **Asynchronous** or **Synchronous**\.
   + Choose **Apply immediately**\.

     If you choose **Schedule for the next maintenance window**, the database doesn't restart right away\. Instead, it remains in the PENDING REBOOT state\. In this case, the database activity stream will not start until either the next maintenance window or a manual restart\.

   When you're done entering settings, choose **Continue**\.

   The cluster's DB instance status shows that the database activity stream is being configured\. 

### AWS CLI<a name="DBActivityStreams.Enabling-collapsible-section-E2"></a>

To start database activity streams for a DB cluster, configure the DB cluster using the [start\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/start-activity-stream.html) AWS CLI command\. Identify the AWS Region for the DB cluster with the `--region` parameter\. The `--apply-immediately` parameter is optional\.

For Linux, OS X, or Unix:

```
aws rds --region us-west-2 \
    start-activity-stream \
    --mode sync \
    --kms-key-id MY_KMS_KEY_ARN \
    --resource-arn MY_CLUSTER_ARN \
    --apply-immediately \
    --profile MY_PROFILE_CREDENTIALS
```

For Windows:

```
aws rds --region us-west-2 ^ 
    start-activity-stream ^ 
    --mode sync ^ 
    --kms-key-id MY_KMS_KEY_ARN ^ 
    --resource-arn MY_CLUSTER_ARN ^ 
    --apply-immediately ^ 
    --profile MY_PROFILE_CREDENTIALS
```

## Getting the Status of a Database Activity Stream<a name="DBActivityStreams.Status"></a>

You can get the status of a database activity stream using the console or AWS CLI\.

### Console<a name="DBActivityStreams.Status-collapsible-section-S1"></a>

**To get the status of a DB cluster's database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster\.

1. Choose the **Configuration** tab and check **Database activity stream** for status\.

### AWS CLI<a name="DBActivityStreams.Status-collapsible-section-S2"></a>

You can get a DB cluster's database activity stream configuration as the response to a [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI request\. In the following example, see the values for `ActivityStreamKinesisStreamName`, `ActivityStreamStatus`, `ActivityStreamKmsKeyId`, and `ActivityStreamMode`\.

The request is as follows:

```
aws rds --region us-west-2 describe-db-clusters --db-cluster-identifier my-cluster --profile MY_PROFILE_CREDENTIALS
```

The response includes the following items for a database activity stream:

```
{
"DBClusters": [
    {
    "DBClusterIdentifier": "my-cluster",
    . . .
    "ActivityStreamKinesisStreamName": "aws-rds-das-cluster-A6TSYXITZCZXJHIRVFUBZ5LTWY",
    "ActivityStreamStatus": "starting",
    "ActivityStreamKmsKeyId": "12345678-abcd-efgh-ijkl-bd041f170262",
    "ActivityStreamMode": "sync",
    "DbClusterResourceId": "cluster-ABCD123456"
    . . .
    }
  ]
}
```

## Stopping a Database Activity Stream<a name="DBActivityStreams.Disabling"></a>

You can stop a database activity stream using the console or AWS CLI\.

### Console<a name="DBActivityStreams.Disabling-collapsible-section-D1"></a>

**To turn off a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to stop the database activity stream for\.

1. For **Actions**, choose **Stop activity stream**\. The **Database Activity Stream** window appears\.

   1. Choose **Apply immediately**\.

      If you choose **Schedule for the next maintenance window**, the database doesn't restart right away\. Instead, it remains in the PENDING REBOOT state\. In this case, the data activity stream is not disabled until either the next maintenance window or a manual restart\.

   1. Choose **Continue**\.

### AWS CLI<a name="DBActivityStreams.Disabling-collapsible-section-D2"></a>

To stop database activity streams for a DB cluster, configure the DB cluster using the AWS CLI command [stop\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-activity-stream.html)\. Identify the AWS Region for the DB cluster using the `--region` parameter\. The `--apply-immediately` parameter is optional\.

For Linux, OS X, or Unix:

```
aws rds --region us-west-2 \
        stop-activity-stream \
        --resource-arn MY_CLUSTER_ARN \
        --apply-immediately \
        --profile MY_PROFILE_CREDENTIALS
```

For Windows:

```
aws rds --region us-west-2 ^
        stop-activity-stream ^
        --resource-arn MY_CLUSTER_ARN ^
        --apply-immediately ^
        --profile MY_PROFILE_CREDENTIALS
```

## Monitoring Database Activity Streams<a name="DBActivityStreams.Monitoring"></a>

A database activity stream monitors and reports all activities on the database\. The stream of data is collected and transmitted to a secure server by using Amazon Kinesis\. From Kinesis, the data stream can be monitored or later consumed by other services and applications for further analysis\. 

The following categories of activity are monitored and put in the database activity stream audit log:
+ **SQL commands** – All SQL commands are audited, and also prepared statements, PostgreSQL functions, and functions in Procedural Language for SQL \(PL/SQL\)\.
+ **Other database information** – Activity monitored includes the full SQL statement, parameters, bind variables, the row count of affected rows from DML commands, accessed objects, and the unique database name\. 
+ **Connection information** – Activity monitored includes session and network information, the server process ID, and exit codes\.

If a database activity stream has a failure while monitoring a DB instance, you are notified by using RDS events\. If a failure occurs, you can decide to shut down the DB instance or let it continue\.

**Topics**
+ [Accessing a Database Activity Stream from Kinesis](#DBActivityStreams.KinesisAccess)
+ [Audit Log Contents and Examples](#DBActivityStreams.AuditLog)
+ [Processing a Database Activity Stream using the AWS SDK](#DBActivityStreams.CodeExample)

### Accessing a Database Activity Stream from Kinesis<a name="DBActivityStreams.KinesisAccess"></a>

When you enable a database activity stream for a DB cluster, a Kinesis stream is created for you\. From Kinesis, you can monitor your database activity in real time\. To further analyze database activity, you can connect your Kinesis stream to consumer applications such as Amazon CloudWatch\. You can also connect it to compliance management applications from Imperva, McAfee, or IBM\.

**To access a database activity stream from Kinesis**

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose your database activity stream from the list of Kinesis streams\.

   A database activity stream's name includes the prefix `aws-rds-das-` followed by the DB cluster's resource ID\. The following is an example\. 

   ```
   aws-rds-das-cluster-NHVOV4PCLWHGF52NP
   ```

   To use the Amazon RDS console to find your DB cluster's resource ID, choose your DB cluster from the list of databases, and then choose the **Configuration** tab\.

   To use the AWS CLI to find the full Kinesis stream name for a database activity stream, use a [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI request and note the value of `DBActivityStreamKinesisStreamName` in the response\.

1. Choose **Monitoring** to begin observing the database activity\.

For more information about using Amazon Kinesis, see [What Is Amazon Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)\.

### Audit Log Contents and Examples<a name="DBActivityStreams.AuditLog"></a>

The database activity events that are monitored are represented in the Kinesis activity stream as JSON strings\. The structure consists of a JSON object containing a `DatabaseActivityMonitoringRecord`, which in turn contains a `databaseActivityEventList` array of activity events\. 

**Topics**
+ [Audit Log Examples](#DBActivityStreams.AuditLog.Examples)
+ [Activity Event Record](#DBActivityStreams.AuditLog.ActivityEvent)
+ [databaseActivityEventList JSON Array](#DBActivityStreams.AuditLog.databaseActivityEventList)

#### Audit Log Examples<a name="DBActivityStreams.AuditLog.Examples"></a>

Following are sample decrypted JSON audit logs of activity event records\.

**Example Activity Event Record of a CONNECT SQL Statement**  
Following is an activity event record of a login with the use of a `CONNECT` SQL statement \(`command`\) by a psql client \(`clientApplication`\)\.   

```
{
   "type":"DatabaseActivityMonitoringRecord",
   "clusterId":"cluster-4HNY5V4RRNPKKYB7ICFKE5JBQQ",
   "instanceId":"db-FZJTMYKCXQBUUZ6VLU7NW3ITCM",
   "databaseActivityEventList":[
      {
        "logTime": "2019-05-23 01:31:28.610198+00",
        "statementId": 1,
        "substatementId": 1,
        "objectType": null,
        "command": "CONNECT",
        "objectName": null,
        "databaseName": "postgres",
        "dbUserName": "rdsadmin",
        "remoteHost": "172.31.3.195",
        "remotePort": "49804",
        "sessionId": "5ce5f7f0.474b",
        "rowCount": null,
        "commandText": null,
        "paramList": [],
        "pid": 18251,
        "clientApplication": "psql",
        "exitCode": null,
        "class": "MISC",
        "serverVersion": "2.3.1",
        "serverType": "PostgreSQL",
        "serviceName": "Amazon Aurora PostgreSQL-Compatible edition",
        "serverHost": "172.31.3.192",
        "netProtocol": "TCP",
        "dbProtocol": "Postgres 3.0",
        "type": "record"
      }
   ]
}
```

**Example Activity Event Record of a CREATE TABLE Statement**  
Following is an example of a `CREATE TABLE` event\.  

```
{
   "type":"DatabaseActivityMonitoringRecord",
   "clusterId":"cluster-4HNY5V4RRNPKKYB7ICFKE5JBQQ",
   "instanceId":"db-FZJTMYKCXQBUUZ6VLU7NW3ITCM",
   "databaseActivityEventList":[
      {
        "logTime": "2019-05-24 00:36:54.494235+00",
        "statementId": 2,
        "substatementId": 1,
        "objectType": null,
        "command": "CREATE TABLE",
        "objectName": null,
        "databaseName": "postgres",
        "dbUserName": "rdsadmin",
        "remoteHost": "172.31.3.195",
        "remotePort": "34534",
        "sessionId": "5ce73c6f.7e64",
        "rowCount": null,
        "commandText": "create table my_table (id serial primary key, name varchar(32));",
        "paramList": [],
        "pid": 32356,
        "clientApplication": "psql",
        "exitCode": null,
        "class": "DDL",
        "serverVersion": "2.3.1",
        "serverType": "PostgreSQL",
        "serviceName": "Amazon Aurora PostgreSQL-Compatible edition",
        "serverHost": "172.31.3.192",
        "netProtocol": "TCP",
        "dbProtocol": "Postgres 3.0",
        "type": "record"
      }
   ]
}
```

**Example Activity Event Record of a SELECT Statement**  
Following is an example of a `SELECT` event\.  

```
{
   "type":"DatabaseActivityMonitoringRecord",
   "clusterId":"cluster-4HNY5V4RRNPKKYB7ICFKE5JBQQ",
   "instanceId":"db-FZJTMYKCXQBUUZ6VLU7NW3ITCM",
   "databaseActivityEventList":[
      {
        "logTime": "2019-05-24 00:39:49.940668+00",
        "statementId": 6,
        "substatementId": 1,
        "objectType": "TABLE",
        "command": "SELECT",
        "objectName": "public.my_table",
        "databaseName": "postgres",
        "dbUserName": "rdsadmin",
        "remoteHost": "172.31.3.195",
        "remotePort": "34534",
        "sessionId": "5ce73c6f.7e64",
        "rowCount": 10,
        "commandText": "select * from my_table;",
        "paramList": [],
        "pid": 32356,
        "clientApplication": "psql",
        "exitCode": null,
        "class": "READ",
        "serverVersion": "2.3.1",
        "serverType": "PostgreSQL",
        "serviceName": "Amazon Aurora PostgreSQL-Compatible edition",
        "serverHost": "172.31.3.192",
        "netProtocol": "TCP",
        "dbProtocol": "Postgres 3.0",
        "type": "record"
      }
   ]
}
```

#### Activity Event Record<a name="DBActivityStreams.AuditLog.ActivityEvent"></a>

The audit log activity event record is a JSON object that contains the following information\.


****  

| JSON Field | Data Type | Description | 
| --- | --- | --- | 
|  `type`  | string |  The type of JSON record\. The value is `DatabaseActivityMonitoringRecord`\.  | 
| clusterId | string | The DB cluster ID | 
| instanceId | string | The DB instance ID\. | 
|  `databaseActivityEventList`   | string |  A JSON object encrypted as a base64 byte array\. Take the following steps to decrypt this content: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)  | 

#### databaseActivityEventList JSON Array<a name="DBActivityStreams.AuditLog.databaseActivityEventList"></a>

The audit log payload is an encrypted `databaseActivityEventList` JSON array\. The following table lists alphabetically the fields for each activity event in the decrypted `DatabaseActivityEventList` array of an audit log\.


****  

| Field | Data Type | Description | 
| --- | --- | --- | 
| class | string |  The class of activity event\. Valid values are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)  | 
| clientApplication | string | The application the client used to connect as reported by the client\. The client doesn't have to provide this information, so the value can be null\. | 
| command | string | The name of the SQL command without any command details\. | 
| commandText | string |  The actual SQL statement passed in by the user\. This field is used for all types of records except for connect or disconnect records, in which case the value is null\.  Sensitive Data The full SQL text is visible including any sensitive data\. However, database user passwords are redacted if they can be determined from the context, such as in the following SQL statement\.   <pre>ALTER ROLE role-name WITH password</pre>  | 
| databaseName | string | The database to which the user connected\. | 
| dbProtocol | string | The database protocol\. | 
| dbUserName | string | The database user with which the client authenticated\. | 
| exitCode | int | A value used for a session exit record\. On a clean exit, this contains the exit code\. An exit code can't always be obtained in some failure scenarios\. Examples are if PostgreSQL does an exit\(\) or if an operator performs a command such as kill \-9\. | 
| logTime | string | A timestamp as recorded in the auditing code path\. | 
| netProtocol | string | The network communication protocol\. | 
| objectName | string | The name of the database object if the SQL statement is operating on one\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. | 
| objectType | string | The database object type such as table, index, view, and so on\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. Valid values include the following:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html) | 
| paramList | string | An array of comma\-separated parameters passed to the SQL statement\. If the SQL statement has no parameters, this value is an empty array\. | 
| pid | int | The process ID of the backend process that is allocated for serving the client connection\. | 
| remoteHost | string | Either the client IP address or hostname, depending on the database's log\_hostname parameter setting\. | 
| remotePort | string | The client port number\. | 
| rowCount | int | The number of table rows affected or retrieved by the SQL statement\. This field is used only for SQL statements that are data manipulation language \(DML\) statements\. If the SQL statement is not a DML statement, this value is null\. | 
| serverHost | string | The database server host IP address\. | 
| serverType | string | The database server type, for example PostgreSQL\. | 
| serverVersion | string | The database server version\. | 
| serviceName | string | The name of the service, for example Amazon Aurora PostgreSQL\-Compatible edition\.  | 
| sessionId | int | A pseudo\-unique session identifier\. | 
| statementId | int | An ID for the client's SQL statement\. The counter is at the session level and increments with each SQL statement entered by the client\.  | 
| substatementId | int | An ID for a SQL substatement\. This value counts the contained substatements for each SQL statement identified by the statementId field\. | 
| type | string | The event type\. Valid values are record or heartbeat\. | 

### Processing a Database Activity Stream using the AWS SDK<a name="DBActivityStreams.CodeExample"></a>

You can programmatically process a database activity stream by using the AWS SDK\. The following are fully functioning Java and Python examples of how you might process the Kinesis data stream\.

------
#### [ Java ]

```
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.net.InetAddress;
import java.nio.ByteBuffer;
import java.nio.charset.StandardCharsets;
import java.security.NoSuchAlgorithmException;
import java.security.NoSuchProviderException;
import java.security.Security;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.zip.GZIPInputStream;

import javax.crypto.Cipher;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.SecretKeySpec;

import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.encryptionsdk.AwsCrypto;
import com.amazonaws.encryptionsdk.CryptoInputStream;
import com.amazonaws.encryptionsdk.jce.JceMasterKey;
import com.amazonaws.services.kinesis.clientlibrary.exceptions.InvalidStateException;
import com.amazonaws.services.kinesis.clientlibrary.exceptions.ShutdownException;
import com.amazonaws.services.kinesis.clientlibrary.exceptions.ThrottlingException;
import com.amazonaws.services.kinesis.clientlibrary.interfaces.IRecordProcessor;
import com.amazonaws.services.kinesis.clientlibrary.interfaces.IRecordProcessorCheckpointer;
import com.amazonaws.services.kinesis.clientlibrary.interfaces.IRecordProcessorFactory;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.InitialPositionInStream;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.KinesisClientLibConfiguration;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.ShutdownReason;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.Worker;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.Worker.Builder;
import com.amazonaws.services.kinesis.model.Record;
import com.amazonaws.services.kms.AWSKMS;
import com.amazonaws.services.kms.AWSKMSClientBuilder;
import com.amazonaws.services.kms.model.DecryptRequest;
import com.amazonaws.services.kms.model.DecryptResult;
import com.amazonaws.util.Base64;
import com.amazonaws.util.IOUtils;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.annotations.SerializedName;
import org.bouncycastle.jce.provider.BouncyCastleProvider;

public class DemoConsumer {

    private static final String STREAM_NAME = "aws-rds-das-[cluster-external-resource-id]";
    private static final String APPLICATION_NAME = "AnyApplication"; //unique application name for dynamo table generation that holds kinesis shard tracking
    private static final String AWS_ACCESS_KEY = "[AWS_ACCESS_KEY_TO_ACCESS_KINESIS]";
    private static final String AWS_SECRET_KEY = "[AWS_SECRET_KEY_TO_ACCESS_KINESIS]";
    private static final String DBC_RESOURCE_ID = "[cluster-external-resource-id]";
    private static final String REGION_NAME = "[region-name]"; //us-east-1, us-east-2...
    private static final BasicAWSCredentials CREDENTIALS = new BasicAWSCredentials(AWS_ACCESS_KEY, AWS_SECRET_KEY);
    private static final AWSStaticCredentialsProvider CREDENTIALS_PROVIDER = new AWSStaticCredentialsProvider(CREDENTIALS);

    private static final AwsCrypto CRYPTO = new AwsCrypto();
    private static final AWSKMS KMS = AWSKMSClientBuilder.standard()
            .withRegion(REGION_NAME)
            .withCredentials(CREDENTIALS_PROVIDER).build();

    class Activity {
        String type;
        String version;
        String databaseActivityEvents;
        String key;
    }

    class ActivityEvent {
        @SerializedName("class") String _class;
        String clientApplication;
        String command;
        String commandText;
        String databaseName;
        String dbProtocol;
        String dbUserName;
        String exitCode;
        String logTime;
        String netProtocol;
        String objectName;
        String objectType;
        List<String> paramList;
        String pid;
        String remoteHost;
        String remotePort;
        String rowCount;
        String serverHost;
        String serverType;
        String serverVersion;
        String serviceName;
        String sessionId;
        String statementId;
        String substatementId;
        String type;
    }

    class ActivityRecords {
        String type;
        String clusterId;
        String instanceId;
        List<ActivityEvent> databaseActivityEventList;
    }

    static class RecordProcessorFactory implements IRecordProcessorFactory {
        @Override
        public IRecordProcessor createProcessor() {
            return new RecordProcessor();
        }
    }

    static class RecordProcessor implements IRecordProcessor {

        private static final long BACKOFF_TIME_IN_MILLIS = 3000L;
        private static final int PROCESSING_RETRIES_MAX = 10;
        private static final long CHECKPOINT_INTERVAL_MILLIS = 60000L;
        private static final Gson GSON = new GsonBuilder().serializeNulls().create();

        private static final Cipher CIPHER;
        static {
            Security.insertProviderAt(new BouncyCastleProvider(), 1);
            try {
                CIPHER = Cipher.getInstance("AES/GCM/NoPadding", "BC");
            } catch (NoSuchAlgorithmException | NoSuchPaddingException | NoSuchProviderException e) {
                throw new ExceptionInInitializerError(e);
            }
        }

        private long nextCheckpointTimeInMillis;

        @Override
        public void initialize(String shardId) {
        }

        @Override
        public void processRecords(final List<Record> records, final IRecordProcessorCheckpointer checkpointer) {
            for (final Record record : records) {
                processSingleBlob(record.getData());
            }

            if (System.currentTimeMillis() > nextCheckpointTimeInMillis) {
                checkpoint(checkpointer);
                nextCheckpointTimeInMillis = System.currentTimeMillis() + CHECKPOINT_INTERVAL_MILLIS;
            }
        }

        @Override
        public void shutdown(IRecordProcessorCheckpointer checkpointer, ShutdownReason reason) {
            if (reason == ShutdownReason.TERMINATE) {
                checkpoint(checkpointer);
            }
        }

        private void processSingleBlob(final ByteBuffer bytes) {
            try {
                // JSON $Activity
                final Activity activity = GSON.fromJson(new String(bytes.array(), StandardCharsets.UTF_8), Activity.class);

                // Base64.Decode
                final byte[] decoded = Base64.decode(activity.databaseActivityEvents);
                final byte[] decodedDataKey = Base64.decode(activity.key);

                Map<String, String> context = new HashMap<>();
                context.put("aws:rds:dbc-id", DBC_RESOURCE_ID);

                // Decrypt
                final DecryptRequest decryptRequest = new DecryptRequest()
                        .withCiphertextBlob(ByteBuffer.wrap(decodedDataKey)).withEncryptionContext(context);
                final DecryptResult decryptResult = KMS.decrypt(decryptRequest);
                final byte[] decrypted = decrypt(decoded, getByteArray(decryptResult.getPlaintext()));

                // GZip Decompress
                final byte[] decompressed = decompress(decrypted);
                // JSON $ActivityRecords
                final ActivityRecords activityRecords = GSON.fromJson(new String(decompressed, StandardCharsets.UTF_8), ActivityRecords.class);

                // Iterate throught $ActivityEvents
                for (final ActivityEvent event : activityRecords.databaseActivityEventList) {
                    System.out.println(GSON.toJson(event));
                }
            } catch (Exception e) {
                // Handle error.
                e.printStackTrace();
            }
        }

        private static byte[] decompress(final byte[] src) throws IOException {
            ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(src);
            GZIPInputStream gzipInputStream = new GZIPInputStream(byteArrayInputStream);
            return IOUtils.toByteArray(gzipInputStream);
        }

        private void checkpoint(IRecordProcessorCheckpointer checkpointer) {
            for (int i = 0; i < PROCESSING_RETRIES_MAX; i++) {
                try {
                    checkpointer.checkpoint();
                    break;
                } catch (ShutdownException se) {
                    // Ignore checkpoint if the processor instance has been shutdown (fail over).
                    System.out.println("Caught shutdown exception, skipping checkpoint." + se);
                    break;
                } catch (ThrottlingException e) {
                    // Backoff and re-attempt checkpoint upon transient failures
                    if (i >= (PROCESSING_RETRIES_MAX - 1)) {
                        System.out.println("Checkpoint failed after " + (i + 1) + "attempts." + e);
                        break;
                    } else {
                        System.out.println("Transient issue when checkpointing - attempt " + (i + 1) + " of " + PROCESSING_RETRIES_MAX + e);
                    }
                } catch (InvalidStateException e) {
                    // This indicates an issue with the DynamoDB table (check for table, provisioned IOPS).
                    System.out.println("Cannot save checkpoint to the DynamoDB table used by the Amazon Kinesis Client Library." + e);
                    break;
                }
                try {
                    Thread.sleep(BACKOFF_TIME_IN_MILLIS);
                } catch (InterruptedException e) {
                    System.out.println("Interrupted sleep" + e);
                }
            }
        }
    }

    private static byte[] decrypt(final byte[] decoded, final byte[] decodedDataKey) throws IOException {
        // Create a JCE master key provider using the random key and an AES-GCM encryption algorithm
        final JceMasterKey masterKey = JceMasterKey.getInstance(new SecretKeySpec(decodedDataKey, "AES"),
                "BC", "DataKey", "AES/GCM/NoPadding");
        try (final CryptoInputStream<JceMasterKey> decryptingStream = CRYPTO.createDecryptingStream(masterKey, new ByteArrayInputStream(decoded));
             final ByteArrayOutputStream out = new ByteArrayOutputStream()) {
            IOUtils.copy(decryptingStream, out);
            return out.toByteArray();
        }
    }

    public static void main(String[] args) throws Exception {
        final String workerId = InetAddress.getLocalHost().getCanonicalHostName() + ":" + UUID.randomUUID();
        final KinesisClientLibConfiguration kinesisClientLibConfiguration =
                new KinesisClientLibConfiguration(APPLICATION_NAME, STREAM_NAME, CREDENTIALS_PROVIDER, workerId);
        kinesisClientLibConfiguration.withInitialPositionInStream(InitialPositionInStream.LATEST);
        kinesisClientLibConfiguration.withRegionName(REGION_NAME);
        final Worker worker = new Builder()
                .recordProcessorFactory(new RecordProcessorFactory())
                .config(kinesisClientLibConfiguration)
                .build();

        System.out.printf("Running %s to process stream %s as worker %s...\n", APPLICATION_NAME, STREAM_NAME, workerId);

        try {
            worker.run();
        } catch (Throwable t) {
            System.err.println("Caught throwable while processing data.");
            t.printStackTrace();
            System.exit(1);
        }
        System.exit(0);
    }

    private static byte[] getByteArray(final ByteBuffer b) {
        byte[] byteArray = new byte[b.remaining()];
        b.get(byteArray);
        return byteArray;
    }
}
```

------
#### [ Python ]

```
import zlib
import boto3
import base64
import json
import aws_encryption_sdk
from Crypto.Cipher import AES
from aws_encryption_sdk import DefaultCryptoMaterialsManager
from aws_encryption_sdk.internal.crypto import WrappingKey
from aws_encryption_sdk.key_providers.raw import RawMasterKeyProvider
from aws_encryption_sdk.identifiers import WrappingAlgorithm, EncryptionKeyType


aws_access_key_id="YOUR_ACCESS_KEY"
aws_secret_access_key="YOUR_SECRET_KEY"
key_id = "your_key_id"
stream_name = "YOUR_KINESIS_STREAM_NAME"
region_name = 'YOUR_REGION_NAME'
cluster_id = "YOUR_CLUSTER_ID"

class MyRawMasterKeyProvider(RawMasterKeyProvider):
    provider_id = "BC"

    def __new__(cls, *args, **kwargs):
        obj = super(RawMasterKeyProvider, cls).__new__(cls)
        return obj

    def __init__(self, wrapping_key):
        RawMasterKeyProvider.__init__(self)
        self.wrapping_key = wrapping_key

    def _get_raw_key(self, key_id):
        return self.wrapping_key


def decrypt(decoded, plaintext):
    wrapping_key = WrappingKey(wrapping_algorithm=WrappingAlgorithm.AES_256_GCM_IV12_TAG16_NO_PADDING,
                               wrapping_key=plaintext, wrapping_key_type=EncryptionKeyType.SYMMETRIC)
    my_key_provider = MyRawMasterKeyProvider(wrapping_key)
    my_key_provider.add_master_key("DataKey")
    with aws_encryption_sdk.stream(
            mode='d',
            source=decoded,
            materials_manager=DefaultCryptoMaterialsManager(master_key_provider=my_key_provider)
    ) as decryptor:
        for chunk in decryptor:
            d = zlib.decompressobj(16 + zlib.MAX_WBITS)
            decompressed_database_stream = d.decompress(chunk)
            print decompressed_database_stream


if __name__ == '__main__':
    session = boto3.session.Session(
        aws_access_key_id=aws_access_key_id,
        aws_secret_access_key=aws_secret_access_key
    )

    kms = session.client('kms', region_name=region_name)

    client = session.client('kinesis', region_name=region_name)

    response = client.describe_stream(StreamName=stream_name)
    shard_it = {}
    for idx, shard in enumerate(response['StreamDescription']['Shards']):
        shared_iterator = client.get_shard_iterator(
            StreamName=stream_name,
            ShardId=response['StreamDescription']['Shards'][idx]['ShardId'],
            ShardIteratorType='LATEST',
        )["ShardIterator"]
        shard_it[idx] = shared_iterator

    while True:
        rows = []
        for shared_iterator in shard_it:
            response = client.get_records(ShardIterator=shard_it[shared_iterator], Limit=10000)
            for record in response['Records']:
                record_data = record['Data']
                record_data = json.loads(record_data)
                key = record_data['key']
                decoded = base64.b64decode(record_data['DatabaseActivityEvents'])
                decoded_data_key = base64.b64decode(record_data['key'])
                decrypt_result = kms.decrypt(CiphertextBlob=decoded_data_key,
                                             EncryptionContext={"aws:rds:dbc-id":
                                                                    cluster_id})
                plaintext = decrypt_result[u'Plaintext']
                cipher = AES.new(plaintext, AES.MODE_GCM)
                decrypt(decoded, decrypt_result[u'Plaintext'])
            shard_it[shared_iterator] = response["NextShardIterator"]
```

------

## Managing Access to Database Activity Streams<a name="DBActivityStreams.ManagingAccess"></a>

Any user with appropriate AWS Identity and Access Management \(IAM\) role privileges for the database activity streams can create, start, stop, and modify the database activity stream settings for a DB cluster\. These actions are included in the audit log of the stream\. For best compliance practices, we recommend that you don't provide these privileges to DBAs\.

You set access to database activity streams using IAM policies\. For more information about Aurora authentication, see [Authentication and Access Control](UsingWithRDS.IAM.md)\. For more information about creating IAM policies, see [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. 

**Example Policy to Allow Configuring Database Activity Streams**  
To give users fine\-grained access to modify database activity streams, use the service\-specific operation context key `rds:ConfigureDBActivityStreams` in an IAM policy\. The following IAM policy example allows a user or role to configure database activity streams\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
        {
            "Sid":"ConfigureActivityStreams",
            "Effect":"Allow",
            "Action": [
                "rds:StartActivityStream",
                "rds:StopActivityStream"
            ],
            "Resource":"*",
          }
     ]
}
```

**Example Policy to Allow Starting Database Activity Streams**  
The following IAM policy example allows a user or role to start database activity streams\.  

```
{ 
    "Version":"2012-10-17", 
    "Statement":[ 
        { 
            "Sid":"AllowStartActivityStreams", 
            "Effect":"Allow", 
            "Action":"rds:StartActivityStream", 
            "Resource":"*" 
        } 
    ] 
}
```

**Example Policy to Allow Stopping Database Activity Streams**  
The following IAM policy example allows a user or role to stop database activity streams\.  

```
{ 
    "Version":"2012-10-17",
    "Statement":[ 
        { 
            "Sid":"AllowStopActivityStreams",
            "Effect":"Allow",
            "Action":"rds:StopActivityStream",
            "Resource":"*" 
        } 
     ] 
}
```

**Example Policy to Deny Starting Database Activity Streams**  
The following IAM policy example denies a user or role from starting database activity streams\.  

```
{ 
    "Version":"2012-10-17", 
    "Statement":[ 
        { 
            "Sid":"DenyStartActivityStreams", 
            "Effect":"Deny", 
            "Action":"rds:StartActivityStream", 
            "Resource":"*" 
        } 
     ]
}
```

**Example Policy to Deny Stopping Database Activity Streams**  
The following IAM policy example denies a user or role from stopping database activity streams\.  

```
{ 
    "Version":"2012-10-17", 
    "Statement":[ 
        { 
            "Sid":"DenyStopActivityStreams",
            "Effect":"Deny",
            "Action":"rds:StopActivityStream",
            "Resource":"*" 
        }
    ]
}
```