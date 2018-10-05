# Import a Regional API into API Gateway<a name="import-export-api-endpoints"></a>

When importing an API, you can choose the regional endpoint configuration for the API\. You can use the API Gateway console, AWS CLI, an AWS SDK, or the API Gateway REST API\.

When you export an API, the API endpoint configuration is not included in the exported API definitions\.

## Import a Regional API Using the API Gateway Console<a name="import-regional-api-with-console"></a>

To import an API of a regional endpoint using the API Gateway console, do the following:

1.  Sign in to the API Gateway console and choose **\+ Create API**\. 

1.  Select the **Import from Swagger** option under **Create new API**\.

1.  Copy an API Swagger definition and paste it into the code editor, or choose **Select Swagger File** to load a Swagger file from a local drive\.

1.  Under **Settings**, for **Endpoint Type**, choose `Regional`\.\.

1.  Choose **Import** to start importing the Swagger definitions\.

## Import a Regional API Using the AWS CLI<a name="import-regional-api-with-awscli"></a>

To import an API from a Swagger definition file using the AWS CLI, use the `import-rest-api` command:

```
aws apigateway import-rest-api \
    --endpointConfigurationTypes 'REGIONAL' \
    --fail-on-warnings \
    --body 'file://path/to/API_Swagger_template.json'
```

## Import a Regional API Using the API Gateway REST API<a name="import-regional-api-with-restapi"></a>

To use the API Gateway REST API to create a regional API by importing a Swagger definition file, call the following `restapi:import` link\-relation:

```
POST /restapis?mode=import&failonwarnings=true&endpointConfigurationTypes=REGIONAL
Host: apigateway.us-west-2.amazonaws.com
Content-Type:application/json
Content-Length: ...

{
    //API Swagger definition
}
```