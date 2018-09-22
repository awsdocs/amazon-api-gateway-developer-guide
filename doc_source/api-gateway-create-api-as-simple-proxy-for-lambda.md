# Build an API Gateway API with Lambda Proxy Integration<a name="api-gateway-create-api-as-simple-proxy-for-lambda"></a>

In this section, we show how to create and test an API with Lambda integration using the API Gateway console\. We demonstrate how a Lambda backend parses the raw request and implements app logic that depends on the incoming request data\. For more information on API Gateway proxy integration, see [Set up a Proxy Integration with a Proxy Resource](api-gateway-set-up-simple-proxy.md)\.

 First, we create the following Node\.js function, named `GetStartedLambdaProxyIntegration`, using the AWS Lambda console, as the backend\. We then create an API with the Lambda proxy integration by using the `GetStartedLambdaProxyIntegration` function through a proxy resource by using the API Gateway console\. Finally, we demonstrate how to test the API\. 

**Topics**
+ [Create Lambda Functions for an API with Lambda Proxy Integration](#api-gateway-proxy-integration-create-lambda-backend)
+ [Create a Backend for an API with Lambda Proxy Integration](#api-gateway-create-api-as-simple-proxy-for-lambda-create-function)
+ [Create an API with Lambda Proxy Integration](#api-gateway-create-api-as-simple-proxy-for-lambda-build)
+ [Test an API with Lambda Proxy Integration](#api-gateway-create-api-as-simple-proxy-for-lambda-test)

## Create Lambda Functions for an API with Lambda Proxy Integration<a name="api-gateway-proxy-integration-create-lambda-backend"></a>

 We create a Lambda function that returns a greeting to the caller as a JSON object of the following format:

```
{
    "greeting": "Good {time}, {name} of {city}.[ Happy {day}]"
}
```

In this example, `{time}` can be `morning`, `afternoon`, or `day`; `{name}` can be `you` or a user\-specified user name; `{city}` can be `World` or a user\-supplied city name; and `{day}` can be null, empty, or one of the week days\. If `{day}` is null or empty, the `Happy {day}` portion is not displayed\. The Lambda function is very flexible and the client can specify the input in any combination of request headers, path variables, query string parameters, and body\. 

 To show what API Gateway passes through to the backend, we include the `event` object to the Lambda function in its output as well\. Finally, we create a `response` object to illustrate the basic output format required of the Lambda proxy integration\. 

 A Lambda function can be written in Node\.js, Python, Java, and C\#\. In this tutorial, we show snippets in Node\.js and Java\. You can extend the Node\.js implementation to the Python function or extend the Java implementation to the C\# function\. There are instructions for doing so in the following topics\. 

**Topics**
+ [Node\.js Function for an API with Lambda Proxy Integration](#api-gateway-proxy-integration-lambda-function-nodejs)
+ [Python Function for an API with Lambda Proxy Integration](#api-gateway-proxy-integration-lambda-function-python)
+ [C\# Function for an API with Lambda Proxy Integration](#api-gateway-proxy-integration-lambda-function-csharp)
+ [Java Function for an API with Lambda Proxy Integration](#api-gateway-proxy-integration-lambda-function-java)

### Node\.js Function for an API with Lambda Proxy Integration<a name="api-gateway-proxy-integration-lambda-function-nodejs"></a>

The following Lambda function in Node\.js is a "Hello, World\!" application\. The function shows how to parse the input `event` parameter that contains a request made by a client to an API Gateway proxy resource\. This resource is integrated with the function using the Lambda proxy integration\. The function also demonstrates how to format the output of the Lambda function for API Gateway to return the results as an HTTP response\. For more information about the input and output formats that this type of Lambda function must follow, see [Input Format of a Lambda Function for Proxy Integration ](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format) and [Output Format of a Lambda Function for Proxy Integration](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-output-format)\. 

```
'use strict';
console.log('Loading hello world function');
 
exports.handler = function(event, context, callback) {
    let name = "you";
    let city = 'World';
    let time = 'day';
    let day = '';
    let responseCode = 200;
    console.log("request: " + JSON.stringify(event));
    
    // This is a simple illustration of app-specific logic to return the response. 
    // Although only 'event.queryStringParameters' are used here, other request data, 
    // such as 'event.headers', 'event.pathParameters', 'event.body', 'event.stageVariables', 
    // and 'event.requestContext' can be used to determine what response to return. 
    //
    if (event.queryStringParameters !== null && event.queryStringParameters !== undefined) {
        if (event.queryStringParameters.name !== undefined && 
            event.queryStringParameters.name !== null && 
            event.queryStringParameters.name !== "") {
            console.log("Received name: " + event.queryStringParameters.name);
            name = event.queryStringParameters.name;
        }
    }
    
    if (event.pathParameters !== null && event.pathParameters !== undefined) {
        if (event.pathParameters.proxy !== undefined && 
            event.pathParameters.proxy !== null && 
            event.pathParameters.proxy !== "") {
            console.log("Received proxy: " + event.pathParameters.proxy);
            city = event.pathParameters.proxy;
        }
    }
    
    if (event.headers !== null && event.headers !== undefined) {
        if (event.headers['day'] !== undefined && event.headers['day'] !== null && event.headers['day'] !== "") {
            console.log("Received day: " + event.headers.day);
            day = event.headers.day;
        }
    }
    
    if (event.body !== null && event.body !== undefined) {
        let body = JSON.parse(event.body)
        if (body.time) 
            time = body.time;
    }
 
    let greeting = 'Good ' + time + ', ' + name + ' of ' + city + '. ';
    if (day) greeting += 'Happy ' + day + '!';

    var responseBody = {
        message: greeting,
        input: event
    };
    
    // The output from a Lambda proxy integration must be 
    // of the following JSON object. The 'headers' property 
    // is for custom response headers in addition to standard 
    // ones. The 'body' property  must be a JSON string. For 
    // base64-encoded payload, you must also set the 'isBase64Encoded'
    // property to 'true'.
    var response = {
        statusCode: responseCode,
        headers: {
            "x-custom-header" : "my custom header value"
        },
        body: JSON.stringify(responseBody)
    };
    console.log("response: " + JSON.stringify(response))
    callback(null, response);
};
```

 For the API Gateway proxy integrations, the input parameter of `event` contains an API request marshalled as a JSON object by API Gateway\. This input can include the request's HTTP method \(`httpMethod`\), path \(`path` and `pathParameters`\), query parameters \(`queryStringParameters`\), headers \(`headers`\), and applicable payload \(`body`\)\. The input can also include the context \(`requestContext`\) and stage variables \(`stageVariables`\)\. 

This example Lambda function parses the `event` parameter to retrieve the query string parameter of `name`, the `proxy` path parameter, the `day` header value, and the `time` property of the payload\. 

The function then returns a greeting to the named user in the `message` property of the `responseBody` object\. To show the details of the incoming request as marshalled by API Gateway, the function also returns the incoming `event` object as the `input` property of the response body\. 

 Finally, upon exiting, the function returns a JSON object, containing the required `statusCode` and any applicable `headers` and `body`, for API Gateway to return it as an HTTP response to the client\. 

### Python Function for an API with Lambda Proxy Integration<a name="api-gateway-proxy-integration-lambda-function-python"></a>

 Follow the discussion in [Authoring Lambda Functions in Python](https://docs.aws.amazon.com/lambda/latest/dg/python-programming-model.html) to create the Python Lambda function handler, while extending the programming flow shown in the preceding Node\.js Lambda function\. 

### C\# Function for an API with Lambda Proxy Integration<a name="api-gateway-proxy-integration-lambda-function-csharp"></a>

 Follow the discussion in [Authoring Lambda Functions in C\#](https://docs.aws.amazon.com/lambda/latest/dg/dotnet-programming-model.html) to create the C\# Lambda function handler, while extending the programming flow shown in the following Java Lambda function\. 

### Java Function for an API with Lambda Proxy Integration<a name="api-gateway-proxy-integration-lambda-function-java"></a>

The following Lambda function in Java is a "Hello, World\!" application, similar to its Node\.js [counterpart](#api-gateway-proxy-integration-lambda-function-nodejs)\. The function shows how to parse the input event that is passed through as an [InputStream](https://docs.aws.amazon.com/lambda/latest/dg/java-programming-model-req-resp.html) object and that contains a request made by a client to an API Gateway proxy resource\. This resource is integrated with the function using the Lambda proxy integration\. It also shows how to parse the `context` object to get the `LambdaLogger`\. The example also demonstrates how to format the output of the Lambda function for API Gateway in Java to return the results in an [OutputStream](https://docs.aws.amazon.com/lambda/latest/dg/java-programming-model-req-resp.html) object as an HTTP response\. For more information about the Lambda proxy integration input and output formats, see [Input Format of a Lambda Function for Proxy Integration ](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format) and [Output Format of a Lambda Function for Proxy Integration](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-output-format)\. 

```
package examples;

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.BufferedReader;
import java.io.Writer;

import com.amazonaws.services.lambda.runtime.RequestStreamHandler;
import com.amazonaws.services.lambda.runtime.Context; 
import com.amazonaws.services.lambda.runtime.LambdaLogger;


import org.json.simple.JSONObject;
import org.json.simple.JSONArray;
import org.json.simple.parser.ParseException;
import org.json.simple.parser.JSONParser;



public class ProxyWithStream implements RequestStreamHandler {
    JSONParser parser = new JSONParser();


    public void handleRequest(InputStream inputStream, OutputStream outputStream, Context context) throws IOException {

        LambdaLogger logger = context.getLogger();
        logger.log("Loading Java Lambda handler of ProxyWithStream");


        BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
        JSONObject responseJson = new JSONObject();
        String name = "you";
        String city = "World";
        String time = "day";
        String day = null;
        String responseCode = "200";

        try {
            JSONObject event = (JSONObject)parser.parse(reader);
            if (event.get("queryStringParameters") != null) {
                JSONObject qps = (JSONObject)event.get("queryStringParameters");
                if ( qps.get("name") != null) {
                    name = (String)qps.get("name");
                }
            }

            if (event.get("pathParameters") != null) {
                JSONObject pps = (JSONObject)event.get("pathParameters");
                if ( pps.get("proxy") != null) {
                    city = (String)pps.get("proxy");
                }
            }

            if (event.get("headers") != null) {
                JSONObject hps = (JSONObject)event.get("headers");
                if ( hps.get("day") != null) {
                    day = (String)hps.get("day");
                }
            }
            
            if (event.get("body") != null) {
                JSONObject body = (JSONObject)parser.parse((String)event.get("body"));
                if ( body.get("time") != null) {
                    time = (String)body.get("time");
                }
            }

            String greeting = "Good " + time + ", " + name + " of " + city + ". ";
            if (day!=null && day != "") greeting += "Happy " + day + "!";


            JSONObject responseBody = new JSONObject();
            responseBody.put("input", event.toJSONString());
            responseBody.put("message", greeting);

            JSONObject headerJson = new JSONObject();
            headerJson.put("x-custom-header", "my custom header value");

            responseJson.put("isBase64Encoded", false);
            responseJson.put("statusCode", responseCode);
            responseJson.put("headers", headerJson);
            responseJson.put("body", responseBody.toString());  

        } catch(ParseException pex) {
            responseJson.put("statusCode", "400");
            responseJson.put("exception", pex);
        }

        logger.log(responseJson.toJSONString());
        OutputStreamWriter writer = new OutputStreamWriter(outputStream, "UTF-8");
        writer.write(responseJson.toJSONString());  
        writer.close();
    }
}
```

For proxy integrations in API Gateway, the input stream contains an API request serialized as a JSON string by API Gateway\. The input data can include the request's HTTP method \(`httpMethod`\), path \(`path` and `pathParameters`\), query parameters \(`queryStringParameters`\), headers \(`headers`\), applicable payload \(`body`\), the context \(`requestContext`\), and stage variables \(`stageVariables`\)\. 

 This example Lambda function parses the `inputStream` parameter to retrieve the query string parameter of `name`, the `proxy` path parameter, the `day` header value and the `time` property of the payload\. For logging, it retrieves the `LambdaLogger` object from the incoming `context` object\. 

The function then returns a greeting to the named user in the `message` property of the `responseBody` object\. To show the details of the incoming request as marshalled by API Gateway, the function also returns the input data \(`event`\) in the response body\. 

 Finally, upon exiting, the function returns a JSON string, containing the required `statusCode` and any applicable `headers` and `body`, for API Gateway to return it as an HTTP response to the client\. 

 To create this function in the Lambda console, you must create a deployment package before uploading the package into Lambda\. For more information, see [creating a deployment package in the *AWS Lambda Developer Guide*](https://docs.aws.amazon.com/lambda/latest/dg/lambda-java-how-to-create-deployment-package.html)\. 

## Create a Backend for an API with Lambda Proxy Integration<a name="api-gateway-create-api-as-simple-proxy-for-lambda-create-function"></a>

The following procedure describes how to create the Lambda function in API Gateway using the Lambda console\.

**Create a Lambda function for an API with a proxy resource in the Lambda console**

1. Sign in to the Lambda console at [https://console\.aws\.amazon\.com/lambda](https://console.aws.amazon.com/lambda)\.

1. From the upper\-right corner, choose an available region for the Lambda function\.

1. From the main navigation pane, choose **Functions**\. You may need to choose the navigation menu on the top\-left corner if the navigation pane is not displayed\.

1.  Choose **Create function**\. And then choose **Author from scratch** or **Blueprints**\. For this example, we create a function from scratch\.

1.  Under **Author from scratch**, do the following:

   1.  In the **Name** input field, type a function name\.

   1.  From the **Runtime** drop\-down list, choose a supported runtime\. In this example, we use **Node\.js 4\.3**\.

   1.  From the **Role** drop\-down list, choose **Choose an existing role**, **Create new role from template\(s\)** or **Create a custom role**\. Then, follow the ensuing instructions for the choice\.

   1. Choose **Create function** to continue\.

      For this example, we will skip the **Designer** section and move to the **Function code** section next\.

1.  For a Node or Python runtime, you can use the inline code editor to create or edit the lambda function, in addition to uploading a zipped code file from a local drive or from Amazon S3\. For a Java or C\# runtime, you must upload the zipped code file from a local drive or from Amazon S3\. In any case, use the code example of the specified runtime as specified in [ Create Lambda Functions for an API with Lambda Proxy Integration ](#api-gateway-proxy-integration-create-lambda-backend) here\. 

1. Choose **Save** to finish creating the Lambda function\.

1.  Optionally, but highly recommended, choose **Test** and configure the test event to take the required [Lambda proxy integration request input](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format)\.

**Note**  
Note the region where you created the Lambda function\. You need it when creating the API for the function\.

## Create an API with Lambda Proxy Integration<a name="api-gateway-create-api-as-simple-proxy-for-lambda-build"></a>

Now create an API with a proxy resource for a Lambda function by using the API Gateway console\.

**Build an API with a proxy resource for a Lambda function**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. To create an API, choose **Create new API** \(for creating the first API\) or **Create API** \(for creating any subsequent API\)\. Next, do the following:

   1. Choose **New API**\.

   1. Type a name in **API Name**\.

   1. Optionally, add a brief description in **Description**\.

   1. Choose **Create API**\.

   For this tutorial, use `LambdaSimpleProxy` as the API name\.

1. To create a child resource, choose a parent resource item under the **Resources** tree and then choose **Create Resource** from the **Actions** drop\-down menu\. Then, do the following in the **New Child Resource** pane\.

   1. Select the **Configure as proxy resource** option to create a proxy resource\. Otherwise, leave it de\-selected\.

   1. Type a name in the **Resource Name\*** input text field\.

   1. Type a new name or use the default name in the **Resource Path\*** input text field\.

   1. Choose **Create Resource**\.

   1. Select **Enable API Gateway CORS**, if required\.

   For this tutorial, use the root resource \(`/`\) as the parent resource\. Select **Configure as proxy resource**\. For **Resource Name**, use the default, `proxy`\. For **Resource Path**, use `/{proxy+}`\. De\-select **Enable API Gateway CORS**\.

1. To set up the `ANY` method for integration with the Lambda back end, do the following:

   1. Choose the resource just created and then choose **Create Method** from the **Actions** drop\-down menu\.

   1. Choose `ANY` from the HTTP method drop\-down list and then choose the check mark icon to save the choice\.

   1. Choose **Lambda Function Proxy** for **Integration type**\. 

   1. Choose a region from **Lambda Region**\.

   1. Type the name of your Lambda function in **Lambda Function**\.

   1. Choose **Save**\.

   1. Choose **OK** when prompted with **Add Permission to Lambda Function**\.

   For this tutorial, use the previously created [GetStartedLambdaProxyIntegration](#api-gateway-proxy-integration-lambda-function-nodejs) for the **Lambda Function**\.

For the proxy resource API that Lambda just created, API Gateway forwards the raw request from the client to the backend for the Lambda function to process\. The request includes the request method, its path, query string and headers parameters, any payload, plus context and stage variables\. The next procedure describes how to test this\. 

## Test an API with Lambda Proxy Integration<a name="api-gateway-create-api-as-simple-proxy-for-lambda-test"></a>

The following procedure describes how to test the proxy integration\.

**Call the [GetStartedLambdaProxyIntegration](#api-gateway-proxy-integration-lambda-function-nodejs) Lambda function through the proxy resource**
+ To use a browser to call a GET method on a specific resource of the API, do the following\.

  1. If you have not done so, choose **Deploy API** from the **Actions** drop\-down menu for the API you created\. Follow the instructions to deploy the API to a specific stage\. Note the **Invoke URL** that displays on the resulting **Stage Editor** page\. This is the base URL of the API\.

  1. To submit a GET request on a specific resource, append the resource path, including possible query string expressions to the **Invoke URL** value obtained in the previous step, copy the complete URL into the address bar of a browser, and choose Enter\.

  For this tutorial, deploy the API to a `test` stage and note of the API's base URL; for example, `https://wt6mne2s9k.execute-api.us-west-2.amazonaws.com/test`\.

  There are several ways you can test a deployed API\. For GET requests using only URL path variables or a query string parameter, you can type the API resource URL in a browser\. For other methods, you must use more advanced REST API testing utilities, such as [POSTMAN](https://www.getpostman.com/) or [cURL](https://curl.haxx.se/)\.

**To test the deployed API using cURL**

  1. Open a terminal window on your local computer connected to the internet\.

  1. To test `POST /Seattle?time=evening`:

     Copy the following cURL command and paste it into the terminal window\.

     ```
     curl -v -X POST \
       'https://r275xc9bmd.execute-api.us-west-2.amazonaws.com/test/Seattle?time=evening' \
       -H 'content-type: application/json' \
       -H 'day: Thursday' \
       -H 'x-amz-docs-region: us-west-2' \
       -d '{
     	"callerName": "John"
     }'
     ```

     You should get a successful response with the following payload:

     ```
     {
       "message": "Good day, John of Seattle. Happy Friday!",
       "input": {
         "resource": "/{proxy+}",
         "path": "/Seattle",
         "httpMethod": "POST",
         "headers": {
           "day": "Friday"
         },
         "queryStringParameters": {
           "time": "morning"
         },
         "pathParameters": {
           "proxy": "Seattle"
         },
         "stageVariables": null,
         "requestContext": {
           "path": "/{proxy+}",
           "accountId": "123456789012",
           "resourceId": "nl9h80",
           "stage": "test-invoke-stage",
           "requestId": "test-invoke-request",
           "identity": {
             "cognitoIdentityPoolId": null,
             "accountId": "123456789012",
             "cognitoIdentityId": null,
             "caller": "AIDXXX...XXVJZG",
             "apiKey": "test-invoke-api-key",
             "sourceIp": "test-invoke-source-ip",
             "accessKey": "ASIXXX...XXDQ5A",
             "cognitoAuthenticationType": null,
             "cognitoAuthenticationProvider": null,
             "userArn": "arn:aws:iam::123456789012:user/kdeding",
             "userAgent": "Apache-HttpClient/4.5.x (Java/1.8.0_131)",
             "user": "AIDXXX...XXVJZG"
           },
           "resourcePath": "/{proxy+}",
           "httpMethod": "POST",
           "apiId": "r275xc9bmd"
         },
         "body": "{ \"callerName\": \"John\" }",
         "isBase64Encoded": false
       }
     }
     ```

     If you change `POST` to `PUT` in the preceding method request, you get the same response\.

  1. To test `GET /Boston?time=morning`:

     Copy the following cURL command and paste it into the terminal window\.

     ```
     curl -X GET \
       'https://r275xc9bmd.execute-api.us-west-2.amazonaws.com/test/Seattle?time=evening' \
       -H 'content-type: application/json' \
       -H 'day: Thursday' \
       -H 'x-amz-docs-region: us-west-2'
     ```

     You get a `200 OK Request` response similar to the result from the preceding `POST` request, with the exception that the `GET` request does not have any payload\. So the `body` parameter will be `null`\. 
**Note**  
The `requestContext` is a map of key\-value pairs and corresponds to the [$context](api-gateway-mapping-template-reference.md#context-variable-reference) variable\. Its outcome is API\-dependent\. API Gateway may add new keys to the map\. For more information, see [Input Format of a Lambda Function for Proxy Integration ](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format)\.