# Tutorial: Build a Hello World REST API with Lambda proxy integration<a name="api-gateway-create-api-as-simple-proxy-for-lambda"></a>

[Lambda proxy integration](set-up-lambda-proxy-integrations.md) is a lightweight, flexible API Gateway API integration type that allows you to integrate an API method – or an entire API – with a Lambda function\. The Lambda function can be written in [any language that Lambda supports](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)\. Because it's a proxy integration, you can change the Lambda function implementation at any time without needing to redeploy your API\.

In this tutorial, you do the following:
+ Create a "Hello, World\!" Lambda function to be the backend for the API\.
+ Create and test a "Hello, World\!" API with Lambda proxy integration\.

**Topics**
+ [Create a "Hello, World\!" Lambda function](#api-gateway-proxy-integration-create-lambda-backend)
+ [Create a "Hello, World\!" API](#api-gateway-create-api-as-simple-proxy-for-lambda-build)
+ [Deploy and test the API](#api-gateway-create-api-as-simple-proxy-for-lambda-test)

## Create a "Hello, World\!" Lambda function<a name="api-gateway-proxy-integration-create-lambda-backend"></a>

This function returns a greeting to the caller as a JSON object in the following format:

```
{
    "greeting": "Good {time}, {name} of {city}.[ Happy {day}!]"
}
```

**Create a "Hello, World\!" Lambda function in the Lambda console**

1. Sign in to the Lambda console at [https://console\.aws\.amazon\.com/lambda](https://console.aws.amazon.com/lambda)\.

1. On the AWS navigation bar, choose a [region](https://docs.aws.amazon.com/general/latest/gr/rande.htmlrande.html#apigateway_region) \(for example, US East \(N\. Virginia\)\)\.
**Note**  
Note the region where you create the Lambda function\. You'll need it when you create the API\.

1. Choose **Functions** in the navigation pane\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Under **Basic information**, do the following:

   1. In **Function name**, enter **GetStartedLambdaProxyIntegration**\.

   1. From the **Runtime** dropdown list, choose a supported Node\.js runtime\.

   1. Under **Permissions**, expand **Choose or create an execution role**\. From the **Execution role** dropdown list, choose **Create new role from AWS policy templates**\.

   1. In **Role name**, enter **GetStartedLambdaBasicExecutionRole**\.

   1. Leave the **Policy templates** field blank\.

   1. Choose **Create function**\.

1. Under **Function code**, in the inline code editor, copy/paste the following code:

   ```
   'use strict';
   console.log('Loading hello world function');
    
   exports.handler = async (event) => {
       let name = "you";
       let city = 'World';
       let time = 'day';
       let day = '';
       let responseCode = 200;
       console.log("request: " + JSON.stringify(event));
       
       if (event.queryStringParameters && event.queryStringParameters.name) {
           console.log("Received name: " + event.queryStringParameters.name);
           name = event.queryStringParameters.name;
       }
       
       if (event.queryStringParameters && event.queryStringParameters.city) {
           console.log("Received city: " + event.queryStringParameters.city);
           city = event.queryStringParameters.city;
       }
       
       if (event.headers && event.headers['day']) {
           console.log("Received day: " + event.headers.day);
           day = event.headers.day;
       }
       
       if (event.body) {
           let body = JSON.parse(event.body)
           if (body.time) 
               time = body.time;
       }
    
       let greeting = `Good ${time}, ${name} of ${city}.`;
       if (day) greeting += ` Happy ${day}!`;
   
       let responseBody = {
           message: greeting,
           input: event
       };
       
       // The output from a Lambda proxy integration must be 
       // in the following JSON object. The 'headers' property 
       // is for custom response headers in addition to standard 
       // ones. The 'body' property  must be a JSON string. For 
       // base64-encoded payload, you must also set the 'isBase64Encoded'
       // property to 'true'.
       let response = {
           statusCode: responseCode,
           headers: {
               "x-custom-header" : "my custom header value"
           },
           body: JSON.stringify(responseBody)
       };
       console.log("response: " + JSON.stringify(response))
       return response;
   };
   ```

1. Choose **Save**\.

## Create a "Hello, World\!" API<a name="api-gateway-create-api-as-simple-proxy-for-lambda-build"></a>

Now create an API for your "Hello, World\!" Lambda function by using the API Gateway console\.

**Build a "Hello, World\!" API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

1. Create an empty API as follows:

   1. Under **Create new API**, choose **New API**\.

   1. Under **Settings**:
      + For **API name**, enter **LambdaSimpleProxy**\.
      + If desired, enter a description in the **Description** field; otherwise, leave it empty\.
      + Leave **Endpoint Type** set to **Regional**\.

   1. Choose **Create API**\.

1. Create the `helloworld` resource as follows:

   1. Choose the root resource \(**/**\) in the **Resources** tree\.

   1. Choose **Create Resource** from the **Actions** dropdown menu\.

   1. Leave **Configure as proxy resource** unchecked\.

   1. For **Resource Name**, enter **helloworld**\.

   1. Leave **Resource Path** set to **/helloworld**\.

   1. Leave **Enable API Gateway CORS** unchecked\.

   1. Choose **Create Resource**\.

1. In a proxy integration, the entire request is sent to the backend Lambda function as\-is, via a catch\-all `ANY` method that represents any HTTP method\. The actual HTTP method is specified by the client at run time\. The `ANY` method allows you to use a single API method setup for all of the supported HTTP methods: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`\.

   To set up the `ANY` method, do the following:

   1. In the **Resources** list, choose **/helloworld**\.

   1. In the **Actions** menu, choose **Create method**\.

   1. Choose **ANY** from the dropdown menu, and choose the checkmark icon

   1. Leave the **Integration type** set to **Lambda Function**\.

   1. Choose **Use Lambda Proxy integration**\.

   1. From the **Lambda Region** dropdown menu, choose the region where you created the `GetStartedLambdaProxyIntegration` Lambda function\.

   1. In the **Lambda Function** field, type any character and choose `GetStartedLambdaProxyIntegration` from the dropdown menu\.

   1. Leave **Use Default Timeout** checked\.

   1. Choose **Save**\.

   1. Choose **OK** when prompted with **Add Permission to Lambda Function**\.

## Deploy and test the API<a name="api-gateway-create-api-as-simple-proxy-for-lambda-test"></a>

**Deploy the API in the API Gateway console**

1. Choose **Deploy API** from the **Actions** dropdown menu\.

1. For **Deployment stage**, choose **\[new stage\]**\.

1. For **Stage name**, enter **test**\.

1. If desired, enter a **Stage description**\.

1. If desired, enter a **Deployment description**\.

1. Choose **Deploy**\.

1. Note the API's **Invoke URL**\.

### Use browser and cURL to test an API with Lambda proxy integration<a name="api-gateway-create-api-as-simple-proxy-for-lambda-test-curl"></a>

You can use a browser or [cURL](https://curl.haxx.se/) to test your API\.

To test `GET` requests using only query string parameters, you can type the URL for the API's `helloworld` resource into a browser address bar\. For example: https://*r275xc9bmd*\.execute\-api\.*us\-east\-1*\.amazonaws\.com/test/helloworld?name=John&city=Seattle

For other methods, you must use more advanced REST API testing utilities, such as [POSTMAN](https://www.postman.com/) or [cURL](https://curl.haxx.se/)\. This tutorial uses cURL\. The cURL command examples below assume that cURL is installed on your computer\.

**To test the deployed API using cURL:**

1. Open a terminal window\.

1. Copy the following cURL command and paste it into the terminal window, replacing `r275xc9bmd` with your API's API ID and `us-east-1` with the region where your API is deployed\.

   ```
   curl -v -X POST \
     'https://r275xc9bmd.execute-api.us-east-1.amazonaws.com/test/helloworld?name=John&city=Seattle' \
     -H 'content-type: application/json' \
     -H 'day: Thursday' \
     -d '{ "time": "evening" }'
   ```
**Note**  
If you're running the command on Windows, use this syntax instead:  

   ```
   curl -v -X POST "https://r275xc9bmd.execute-api.us-east-1.amazonaws.com/test/helloworld?name=John&city=Seattle" -H "content-type: application/json" -H "day: Thursday" -d "{ \"time\": \"evening\" }"
   ```

You should get a successful response with a payload similar to the following:

```
{
  "message":"Good evening, John of Seattle. Happy Thursday!", 
  "input":{
    "resource":"/helloworld",
    "path":"/helloworld",
    "httpMethod":"POST",
    "headers":{"Accept":"*/*",
    "content-type":"application/json",
    "day":"Thursday",
    "Host":"r275xc9bmd.execute-api.us-east-1.amazonaws.com",
    "User-Agent":"curl/7.64.0",
    "X-Amzn-Trace-Id":"Root=1-1a2b3c4d-a1b2c3d4e5f6a1b2c3d4e5f6",
    "X-Forwarded-For":"72.21.198.64",
    "X-Forwarded-Port":"443",
    "X-Forwarded-Proto":"https"},
    "multiValueHeaders":{"Accept":["*/*"],
    "content-type":["application/json"],
    "day":["Thursday"],
    "Host":["r275xc9bmd.execute-api.us-east-1.amazonaws.com"],
    "User-Agent":["curl/0.0.0"],
    "X-Amzn-Trace-Id":["Root=1-1a2b3c4d-a1b2c3d4e5f6a1b2c3d4e5f6"],
    "X-Forwarded-For":["11.22.333.44"],
    "X-Forwarded-Port":["443"],
    "X-Forwarded-Proto":["https"]},
    "queryStringParameters":{"city":"Seattle",
    "name":"John"
  },
  "multiValueQueryStringParameters":{
    "city":["Seattle"],
    "name":["John"]
  },
  "pathParameters":null,
  "stageVariables":null,
  "requestContext":{
    "resourceId":"3htbry",
    "resourcePath":"/helloworld",
    "htt* Connection #0 to host r275xc9bmd.execute-api.us-east-1.amazonaws.com left intact pMethod":"POST",
    "extendedRequestId":"a1b2c3d4e5f6g7h=",
    "requestTime":"20/Mar/2019:20:38:30 +0000",
    "path":"/test/helloworld",
    "accountId":"123456789012",
    "protocol":"HTTP/1.1",
    "stage":"test",
    "domainPrefix":"r275xc9bmd",
    "requestTimeEpoch":1553114310423,
    "requestId":"test-invoke-request",
    "identity":{"cognitoIdentityPoolId":null,
      "accountId":null,
      "cognitoIdentityId":null,
      "caller":null,
      "sourceIp":"test-invoke-source-ip",
      "accessKey":null,
      "cognitoAuthenticationType":null,
      "cognitoAuthenticationProvider":null,
      "userArn":null,
      "userAgent":"curl/0.0.0","user":null
    },
    "domainName":"r275xc9bmd.execute-api.us-east-1.amazonaws.com",
    "apiId":"r275xc9bmd"
  },
  "body":"{ \"time\": \"evening\" }",
  "isBase64Encoded":false
  }
}
```

If you change `POST` to `PUT` in the preceding method request, you should get the same response\.

To test the `GET` method, copy the following `cURL` command and paste it into the terminal window, replacing `r275xc9bmd` with your API's API ID and `us-east-1` with the region where your API is deployed\.

```
curl -X GET \
  'https://r275xc9bmd.execute-api.us-east-1.amazonaws.com/test/helloworld?name=John&city=Seattle' \
  -H 'content-type: application/json' \
  -H 'day: Thursday'
```

You should get a response similar to the result from the preceding `POST` request, except that the `GET` request does not have any payload\. So the `body` parameter will be `null`\. 