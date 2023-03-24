# Describe options for Aurora DB instances using an AWS SDK<a name="example_aurora_DescribeOrderableDBInstanceOptions_section"></a>

The following code examples show how to describe options for Aurora DB instances\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/Aurora#code-examples)\. 
  

```
    /// <summary>
    /// Get a list of orderable DB instance options for a specific
    /// engine and engine version. 
    /// </summary>
    /// <param name="engine">Name of the engine.</param>
    /// <param name="engineVersion">Version of the engine.</param>
    /// <returns>List of OrderableDBInstanceOptions.</returns>
    public async Task<List<OrderableDBInstanceOption>> DescribeOrderableDBInstanceOptionsPagedAsync(string engine, string engineVersion)
    {
        // Use a paginator to get a list of DB instance options.
        var results = new List<OrderableDBInstanceOption>();
        var paginateInstanceOptions = _amazonRDS.Paginators.DescribeOrderableDBInstanceOptions(
            new DescribeOrderableDBInstanceOptionsRequest()
            {
                Engine = engine,
                EngineVersion = engineVersion,
            });
        // Get the entire list using the paginator.
        await foreach (var instanceOptions in paginateInstanceOptions.OrderableDBInstanceOptions)
        {
            results.Add(instanceOptions);
        }
        return results;
    }
```
+  For API details, see [DescribeOrderableDBInstanceOptions](https://docs.aws.amazon.com/goto/DotNetSDKV3/rds-2014-10-31/DescribeOrderableDBInstanceOptions) in *AWS SDK for \.NET API Reference*\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/aurora#code-examples)\. 
  

```
        Aws::Client::ClientConfiguration clientConfig;
        // Optional: Set to the AWS Region (overrides config file).
        // clientConfig.region = "us-east-1";

    Aws::RDS::RDSClient client(clientConfig);


//! Routine which gets available DB instance classes, displays the list
//! to the user, and returns the user selection.
/*!
 \sa chooseDBInstanceClass()
 \param engineName: The DB engine name.
 \param engineVersion: The DB engine version.
 \param dbInstanceClass: String for DB instance class chosen by the user.
 \param client: 'RDSClient' instance.
 \return bool: Successful completion.
 */
bool AwsDoc::Aurora::chooseDBInstanceClass(const Aws::String &engine,
                                           const Aws::String &engineVersion,
                                           Aws::String &dbInstanceClass,
                                           const Aws::RDS::RDSClient &client) {
    std::vector<Aws::String> instanceClasses;
    Aws::String marker; // The marker is used for pagination.
    do {
        Aws::RDS::Model::DescribeOrderableDBInstanceOptionsRequest request;
        request.SetEngine(engine);
        request.SetEngineVersion(engineVersion);
        if (!marker.empty()) {
            request.SetMarker(marker);
        }

        Aws::RDS::Model::DescribeOrderableDBInstanceOptionsOutcome outcome =
                client.DescribeOrderableDBInstanceOptions(request);

        if (outcome.IsSuccess()) {
            const Aws::Vector<Aws::RDS::Model::OrderableDBInstanceOption> &options =
                    outcome.GetResult().GetOrderableDBInstanceOptions();
            for (const Aws::RDS::Model::OrderableDBInstanceOption &option: options) {
                const Aws::String &instanceClass = option.GetDBInstanceClass();
                instanceClasses.push_back(instanceClass);
            }
            marker = outcome.GetResult().GetMarker();
        }
        else {
            std::cerr << "Error with Aurora::DescribeOrderableDBInstanceOptions. "
                      << outcome.GetError().GetMessage()
                      << std::endl;
            return false;
        }
    } while (!marker.empty());

    std::cout << "The available DB instance classes for your database engine are:"
              << std::endl;
    for (int i = 0; i < instanceClasses.size(); ++i) {
        std::cout << "   " << i + 1 << ": " << instanceClasses[i] << std::endl;
    }

    int choice = askQuestionForIntRange(
            "Which DB instance class do you want to use? ",
            1, static_cast<int>(instanceClasses.size()));
    dbInstanceClass = instanceClasses[choice - 1];
    return true;
}
```
+  For API details, see [DescribeOrderableDBInstanceOptions](https://docs.aws.amazon.com/goto/SdkForCpp/rds-2014-10-31/DescribeOrderableDBInstanceOptions) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/rds#readme)\. 
  

```
    public static void describeDBEngines( RdsClient rdsClient) {
        try {
            DescribeDbEngineVersionsRequest engineVersionsRequest = DescribeDbEngineVersionsRequest.builder()
                .engine("aurora-mysql")
                .defaultOnly(true)
                .maxRecords(20)
                .build();

            DescribeDbEngineVersionsResponse response = rdsClient.describeDBEngineVersions(engineVersionsRequest);
            List<DBEngineVersion> engines = response.dbEngineVersions();

            // Get all DBEngineVersion objects.
            for (DBEngineVersion engineOb: engines) {
                System.out.println("The name of the DB parameter group family for the database engine is "+engineOb.dbParameterGroupFamily());
                System.out.println("The name of the database engine "+engineOb.engine());
                System.out.println("The version number of the database engine "+engineOb.engineVersion());
            }

        } catch (RdsException e) {
            System.out.println(e.getLocalizedMessage());
            System.exit(1);
        }
    }
```
+  For API details, see [DescribeOrderableDBInstanceOptions](https://docs.aws.amazon.com/goto/SdkForJavaV2/rds-2014-10-31/DescribeOrderableDBInstanceOptions) in *AWS SDK for Java 2\.x API Reference*\. 

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

    def get_orderable_instances(self, db_engine, db_engine_version):
        """
        Gets DB instance options that can be used to create DB instances that are
        compatible with a set of specifications.

        :param db_engine: The database engine that must be supported by the DB instance.
        :param db_engine_version: The engine version that must be supported by the DB instance.
        :return: The list of DB instance options that can be used to create a compatible DB instance.
        """
        try:
            inst_opts = []
            paginator = self.rds_client.get_paginator('describe_orderable_db_instance_options')
            for page in paginator.paginate(Engine=db_engine, EngineVersion=db_engine_version):
                inst_opts += page['OrderableDBInstanceOptions']
        except ClientError as err:
            logger.error(
                "Couldn't get orderable DB instances. Here's why: %s: %s",
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return inst_opts
```
+  For API details, see [DescribeOrderableDBInstanceOptions](https://docs.aws.amazon.com/goto/boto3/rds-2014-10-31/DescribeOrderableDBInstanceOptions) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](CHAP_Tutorials.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.