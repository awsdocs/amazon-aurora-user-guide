# Create a DB instance in an Aurora DB cluster using an AWS SDK<a name="example_aurora_CreateDBInstance_section"></a>

The following code examples show how to create a DB instance in an Aurora DB cluster\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/Aurora#code-examples)\. 
  

```
    /// <summary>
    /// Create an Amazon Relational Database Service (Amazon RDS) DB instance
    /// with a particular set of properties. Use the action DescribeDBInstancesAsync
    /// to determine when the DB instance is ready to use.
    /// </summary>
    /// <param name="dbInstanceIdentifier">DB instance identifier.</param>
    /// <param name="dbClusterIdentifier">DB cluster identifier.</param>
    /// <param name="dbEngine">The engine for the DB instance.</param>
    /// <param name="dbEngineVersion">Version for the DB instance.</param>
    /// <param name="instanceClass">Class for the DB instance.</param>
    /// <returns>DB instance object.</returns>
    public async Task<DBInstance> CreateDBInstanceInClusterAsync(
        string dbClusterIdentifier,
        string dbInstanceIdentifier,
        string dbEngine,
        string dbEngineVersion,
        string instanceClass)
    {
        // When creating the instance within a cluster, do not specify the name or size.
        var response = await _amazonRDS.CreateDBInstanceAsync(
            new CreateDBInstanceRequest()
            {
                DBClusterIdentifier = dbClusterIdentifier,
                DBInstanceIdentifier = dbInstanceIdentifier,
                Engine = dbEngine,
                EngineVersion = dbEngineVersion,
                DBInstanceClass = instanceClass
            });

        return response.DBInstance;
    }
```
+  For API details, see [CreateDBInstance](https://docs.aws.amazon.com/goto/DotNetSDKV3/rds-2014-10-31/CreateDBInstance) in *AWS SDK for \.NET API Reference*\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/aurora#code-examples)\. 
  

```
        Aws::Client::ClientConfiguration clientConfig;
        // Optional: Set to the AWS Region (overrides config file).
        // clientConfig.region = "us-east-1";

    Aws::RDS::RDSClient client(clientConfig);

        Aws::RDS::Model::CreateDBInstanceRequest request;
        request.SetDBInstanceIdentifier(DB_INSTANCE_IDENTIFIER);
        request.SetDBClusterIdentifier(DB_CLUSTER_IDENTIFIER);
        request.SetEngine(engineName);
        request.SetDBInstanceClass(dbInstanceClass);

        Aws::RDS::Model::CreateDBInstanceOutcome outcome =
                client.CreateDBInstance(request);

        if (outcome.IsSuccess()) {
            std::cout << "The DB instance creation has started."
                      << std::endl;
        }
        else {
            std::cerr << "Error with RDS::CreateDBInstance. "
                      << outcome.GetError().GetMessage()
                      << std::endl;
            cleanUpResources(CLUSTER_PARAMETER_GROUP_NAME, DB_CLUSTER_IDENTIFIER, "",
                             client);
            return false;
        }
```
+  For API details, see [CreateDBInstance](https://docs.aws.amazon.com/goto/SdkForCpp/rds-2014-10-31/CreateDBInstance) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/rds#readme)\. 
  

```
    public static String createDBInstanceCluster(RdsClient rdsClient,
                                                 String dbInstanceIdentifier,
                                                 String dbInstanceClusterIdentifier,
                                                 String instanceClass){

        try {
            CreateDbInstanceRequest instanceRequest = CreateDbInstanceRequest.builder()
                .dbInstanceIdentifier(dbInstanceIdentifier)
                .dbClusterIdentifier(dbInstanceClusterIdentifier)
                .engine("aurora-mysql")
                .dbInstanceClass(instanceClass)
                .build() ;

            CreateDbInstanceResponse response = rdsClient.createDBInstance(instanceRequest);
            System.out.print("The status is " + response.dbInstance().dbInstanceStatus());
            return response.dbInstance().dbInstanceArn();

        } catch (RdsException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        return "";
    }
```
+  For API details, see [CreateDBInstance](https://docs.aws.amazon.com/goto/SdkForJavaV2/rds-2014-10-31/CreateDBInstance) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/rds#code-examples)\. 
  

```
suspend fun createDBInstanceCluster(dbInstanceIdentifierVal: String?, dbInstanceClusterIdentifierVal: String?, instanceClassVal: String?): String? {
    val instanceRequest = CreateDbInstanceRequest {
        dbInstanceIdentifier = dbInstanceIdentifierVal
        dbClusterIdentifier = dbInstanceClusterIdentifierVal
        engine = "aurora-mysql"
        dbInstanceClass = instanceClassVal
    }

    RdsClient { region = "us-west-2" }.use { rdsClient ->
        val response = rdsClient.createDbInstance(instanceRequest)
        print("The status is ${response.dbInstance?.dbInstanceStatus}")
        return response.dbInstance?.dbInstanceArn
    }
}
```
+  For API details, see [CreateDBInstance](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/aurora#code-examples)\. 
  

```
class AuroraWrapper:
    """Encapsulates Aurora DB cluster actions."""
    def __init__(self, rds_client):
        """
        :param rds_client: A Boto3 Amazon Relational Database Service (Amazon RDS) client.
        """
        self.rds_client = rds_client

    @classmethod
    def from_client(cls):
        """
        Instantiates this class from a Boto3 client.
        """
        rds_client = boto3.client('rds')
        return cls(rds_client)

    def create_instance_in_cluster(self, instance_id, cluster_id, db_engine, instance_class):
        """
        Creates a database instance in an existing DB cluster. The first database that is
        created defaults to a read-write DB instance.

        :param instance_id: The ID to give the newly created DB instance.
        :param cluster_id: The ID of the DB cluster where the DB instance is created.
        :param db_engine: The database engine of a database to create in the DB instance.
                          This must be compatible with the configured parameter group
                          of the DB cluster.
        :param instance_class: The DB instance class for the newly created DB instance.
        :return: Data about the newly created DB instance.
        """
        try:
            response = self.rds_client.create_db_instance(
                DBInstanceIdentifier=instance_id, DBClusterIdentifier=cluster_id,
                Engine=db_engine, DBInstanceClass=instance_class)
            db_inst = response['DBInstance']
        except ClientError as err:
            logger.error(
                "Couldn't create DB instance %s. Here's why: %s: %s", instance_id,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return db_inst
```
+  For API details, see [CreateDBInstance](https://docs.aws.amazon.com/goto/boto3/rds-2014-10-31/CreateDBInstance) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](CHAP_Tutorials.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.