# Import a Regional API into API Gateway<a name="import-export-api-endpoints"></a>

When importing an API, you can choose the regional endpoint configuration for the API\. You can use the API Gateway console, the AWS CLI, or an AWS SDK\.

When you export an API, the API endpoint configuration is not included in the exported API definitions\.

For a tutorial on using the Import API feature from the API Gateway console, see [TUTORIAL: Create a REST API by Importing an Example](api-gateway-create-api-from-example.md)\.

**Topics**
+ [Import a Regional API Using the API Gateway Console](#import-regional-api-with-console)
+ [Import a Regional API Using the AWS CLI](#import-regional-api-with-awscli)

## Import a Regional API Using the API Gateway Console<a name="import-regional-api-with-console"></a>

To import an API of a regional endpoint using the API Gateway console, do the following:

1.  Sign in to the API Gateway console and choose **Create API**\. 

1.  Select the **Import from OpenAPI** option under **Create new API**\.

1.  Copy an API's OpenAPI definition and paste it into the code editor, or choose **Select OpenAPI File** to load an OpenAPI file from a local drive\.

1.  Under **Settings**, for **Endpoint Type**, choose `Regional`\.

1.  Choose **Import** to start importing the OpenAPI definitions\.

## Import a Regional API Using the AWS CLI<a name="import-regional-api-with-awscli"></a>

To import an API from an OpenAPI definition file using the AWS CLI, use the `import-rest-api` command:

```
aws apigateway import-rest-api \
    --endpointConfigurationTypes 'REGIONAL' \
    --fail-on-warnings \
    --body 'file://path/to/API_OpenAPI_template.json'
```