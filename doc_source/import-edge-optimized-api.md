# Import an edge\-optimized API into API Gateway<a name="import-edge-optimized-api"></a>

You can import an API's OpenAPI definition file to create a new edge\-optimized API by specifying the `EDGE` endpoint type as an additional input, besides the OpenAPI file, to the import operation\. You can do so using the API Gateway console, AWS CLI, or an AWS SDK\.

For a tutorial on using the Import API feature from the API Gateway console, see [Tutorial: Create a REST API by importing an example](api-gateway-create-api-from-example.md)\.

**Topics**
+ [Import an edge\-optimized API using the API Gateway console](#import-edge-optimized-api-with-console)
+ [Import an edge\-optimized API using the AWS CLI](#import-edge-optimized-api-with-awscli)

## Import an edge\-optimized API using the API Gateway console<a name="import-edge-optimized-api-with-console"></a>

To import an edge\-optimized API using the API Gateway console, do the following:

1.  Sign in to the API Gateway console and choose **\+ Create API**\. 

1.  Select the **Import from OpenAPI** option under **Create new API**\.

1.  Copy an API's OpenAPI definition and paste it into the code editor, or choose **Select OpenAPI File** to load an OpenAPI file from a local drive\.

1.  Under **Settings**, for **Endpoint Type**, choose `Edge optimized`\.

1.  Choose **Import** to start importing the OpenAPI definitions\.

## Import an edge\-optimized API using the AWS CLI<a name="import-edge-optimized-api-with-awscli"></a>

To import an API from an OpenAPI definition file to create a new edge\-optimized API using the AWS CLI, use the `import-rest-api` command as follows:

```
aws apigateway import-rest-api \
    --fail-on-warnings \
    --body 'file://path/to/API_OpenAPI_template.json'
```

or with an explicit specification of the `endpointConfigurationTypes` query string parameter to `EDGE`: 

```
aws apigateway import-rest-api \
    --endpointConfigurationTypes=EDGE \
    --fail-on-warnings \
    --body 'file://path/to/API_OpenAPI_template.json'
```