# Import an Edge\-Optimized API into API Gateway<a name="import-edge-optimized-api"></a>

You can import an API's OpenAPI definition file to create a new edge\-optimized API by specifying the `EDGE` endpoint type as an additional input, besides the OpenAPI file, to the import operation\. You can do so using the API Gateway console, AWS CLI, an AWS SDK, or the API Gateway REST API\.

**Topics**
+ [Import an Edge\-Optimized API Using the API Gateway Console](#import-edge-optimized-api-with-console)
+ [Import an Edge\-Optimized API Using the AWS CLI](#import-edge-optimized-api-with-awscli)
+ [Import an Edge\-Optimized API Using the API Gateway REST API](#import-edge-optimized-api-with-restapi)

## Import an Edge\-Optimized API Using the API Gateway Console<a name="import-edge-optimized-api-with-console"></a>

To import an API of an edge\-optimized API endpoint type using the API Gateway console, do the following:

1.  Sign in to the API Gateway console and choose **\+ Create API**\. 

1.  Select the **Import from OpenAPI** option under **Create new API**\.

1.  Copy an API's OpenAPI definition and paste it into the code editor, or choose **Select OpenAPI File** to load an OpenAPI file from a local drive\.

1.  Under **Settings**, for **Endpoint Type**, choose `Edge optimized`\.\.

1.  Choose **Import** to start importing the OpenAPI definitions\.

## Import an Edge\-Optimized API Using the AWS CLI<a name="import-edge-optimized-api-with-awscli"></a>

To import an API from an OpenAPI definition file to create a new edge\-optimized API using the AWS CLI, use the `import-rest-api` command as follows:

```
aws apigateway import-rest-api \
    --fail-on-warnings \
    --body 'file://path/to/API_OpenAPI_template.json'
```

or with an explicit specification of the `endpointConfigurationTypes` query string parameter to `EDGE`: 

```
aws apigateway import-rest-api \
    --endpointConfigurationTypes 'EDGE' \
    --fail-on-warnings \
    --body 'file://path/to/API_OpenAPI_template.json'
```

## Import an Edge\-Optimized API Using the API Gateway REST API<a name="import-edge-optimized-api-with-restapi"></a>

To use the API Gateway REST API to create a regional API by importing an OpenAPI definition file, call the following `restapi:import` link\-relation:

```
POST /restapis?mode=import&failonwarnings=true
Host: apigateway.us-west-2.amazonaws.com
Content-Type:application/json
Content-Length: ...

{
    //OpenAPI definition
}
```

or with an explicit specification of the `endpointConfigurationTypes` query string parameter to `EDGE`:

```
POST /restapis?mode=import&failonwarnings=true&endpointConfigurationTypes=EDGE
Host: apigateway.us-west-2.amazonaws.com
Content-Type:application/json
Content-Length: ...

{
    //OpenAPI definition
}
```