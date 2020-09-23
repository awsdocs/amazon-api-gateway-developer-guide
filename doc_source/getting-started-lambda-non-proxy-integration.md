# Tutorial: Build an API Gateway REST API with Lambda non\-proxy integration<a name="getting-started-lambda-non-proxy-integration"></a>

In this walkthrough, we use the API Gateway console to build an API that enables a client to call Lambda functions through the Lambda non\-proxy integration \(also known as custom integration\)\. For more information about AWS Lambda and Lambda functions, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\. 

To facilitate learning, we chose a simple Lambda function with minimal API setup to walk you through the steps of building an API Gateway API with the Lambda custom integration\. When necessary, we describe some of the logic\. For a more detailed example of the Lambda custom integration, see [Tutorial: Create a `Calc` REST API with two AWS service integrations and one Lambda non\-proxy integration](integrating-api-with-aws-services-lambda.md)\. 

Before creating the API, set up the Lambda backend by creating a Lambda function in AWS Lambda, described next\.

**Topics**
+ [Create a Lambda function for Lambda non\-proxy integration](#getting-started-new-lambda)
+ [Create an API with Lambda non\-proxy integration](#getting-started-new-api)
+ [Test invoking the API method](#getting-started-new-get)
+ [Deploy the API](#getting-started-deploy-api)
+ [Test the API in a deployment stage](#getting-started-test)
+ [Clean up](#getting-started-clean-up)

## Create a Lambda function for Lambda non\-proxy integration<a name="getting-started-new-lambda"></a>

**Note**  
Creating Lambda functions may result in charges to your AWS account\.

 In this step, you create a "Hello, World\!"\-like Lambda function for the Lambda custom integration\. Throughout this walkthrough, the function is called `GetStartedLambdaIntegration`\.

 The Node\.js implementation of this `GetStartedLambdaIntegration` Lambda function is as follows: 

```
'use strict';
var days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];            
var times = ['morning', 'afternoon', 'evening', 'night', 'day'];

console.log('Loading function');

exports.handler = function(event, context, callback) {
  // Parse the input for the name, city, time and day property values
  let name = event.name === undefined ? 'you' : event.name;
  let city = event.city === undefined ? 'World' : event.city;
  let time = times.indexOf(event.time)<0 ? 'day' : event.time;
  let day = days.indexOf(event.day)<0 ? null : event.day;

  // Generate a greeting
  let greeting = 'Good ' + time + ', ' + name + ' of ' + city + '. ';
  if (day) greeting += 'Happy ' + day + '!';
  
  // Log the greeting to CloudWatch
  console.log('Hello: ', greeting);
  
  // Return a greeting to the caller
  callback(null, {
      "greeting": greeting
  }); 
};
```

For the Lambda custom integration, API Gateway passes the input to the Lambda function from the client as the integration request body\. The `event` object of the Lambda function handler is the input\. 

Our Lambda function is simple\. It parses the input `event` object for the `name`, `city`, `time`, and `day` properties\. It then returns a greeting, as a JSON object of `{"message":greeting}`, to the caller\. The message is in the `"Good [morning|afternoon|day], [name|you] in [city|World]. Happy day!"` pattern\. It is assumed that the input to the Lambda function is of the following JSON object: 

```
{
  "city": "...",
  "time": "...",
  "day": "...",
  "name" : "..."
}
```

For more information, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)\. 

In addition, the function logs its execution to Amazon CloudWatch by calling `console.log(...)`\. This is helpful for tracing calls when debugging the function\. To allow the `GetStartedLambdaIntegration` function to log the call, set an IAM role with appropriate policies for the Lambda function to create the CloudWatch streams and add log entries to the streams\. The Lambda console guides you through to create the required IAM roles and policies\.

If you set up the API without using the API Gateway console, such as when [importing an API from an OpenAPI file](https://github.com/awslabs/api-gateway-secure-pet-store/blob/master/src/main/resources/swagger.yaml#L39), you must explicitly create, if necessary, and set up an invocation role and policy for API Gateway to invoke the Lambda functions\. For more information on how to set up Lambda invocation and execution roles for an API Gateway API, see [Control access to an API with IAM permissions](permissions.md)\. 

 Compared to `GetStartedLambdaProxyIntegration`, the Lambda function for the Lambda proxy integration, the `GetStartedLambdaIntegration` Lambda function for the Lambda custom integration only takes input from the API Gateway API integration request body\. The function can return an output of any JSON object, a string, a number, a Boolean, or even a binary blob\. The Lambda function for the Lambda proxy integration, in contrast, can take the input from any request data, but must return an output of a particular JSON object\. The `GetStartedLambdaIntegration` function for the Lambda custom integration can have the API request parameters as input, provided that API Gateway maps the required API request parameters to the integration request body before forwarding the client request to the backend\. For this to happen, the API developer must create a mapping template and configure it on the API method when creating the API\. 

Now, create the `GetStartedLambdaIntegration` Lambda function\. 

**To create the `GetStartedLambdaIntegration` Lambda function for Lambda custom integration**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Do one of the following:
   + If the welcome page appears, choose **Get Started Now** and then choose **Create function**\.
   + If the **Lambda > Functions** list page appears, choose **Create function**\.

1. Choose **Author from scratch**\. 

1. In the **Author from scratch** pane, do the following:

   1. For **Name**, enter **GetStartedLambdaIntegration** as the Lambda function name\.

   1. For **Runtime**, choose a supported Node\.js runtime\.

   1. For **Execution role**, choose **Create a new role from AWS policy templates**\.

   1. For **Role name**, enter a name for your role \(for example, **GetStartedLambdaIntegrationRole**\)\.

   1. For **Policy templates**, choose **Simple microservice permissions**\.

   1. Choose **Create function**\.

1. In the **Configure function** pane, under **Function code** do the following:

   1. Copy the Lambda function code listed in the beginning of this section and paste it in the inline code editor\.

   1. Leave the default choices for all other fields in this section\.

   1. Choose **Save**\.

1. To test the newly created function, choose **Configure test events** from **Select a test event\.\.\.**\.

   1. For **Create new event**, replace any default code statements with the following, enter **HelloWorldTest** for the event name, and choose **Create**\.

      ```
      {
        "name": "Jonny",
        "city": "Seattle",
        "time": "morning",
        "day": "Wednesday"
      }
      ```

   1.  Choose **Test** to invoke the function\. The **Execution result: succeeded** section is shown\. Expand **Detail** and you see the following output\.

      ```
      {
          "greeting": "Good morning, Jonny of Seattle. Happy Wednesday!"
      }
      ```

      The output is also written to CloudWatch Logs\. 

 As a side exercise, you can use the IAM console to view the IAM role \(`GetStartedLambdaIntegrationRole`\) that was created as part of the Lambda function creation\. Attached to this IAM role are two inline policies\. One stipulates the most basic permissions for Lambda execution\. It permits calling the CloudWatch `CreateLogGroup` for any CloudWatch resources of your account in the region where the Lambda function is created\. This policy also allows creating the CloudWatch streams and logging events for the `HelloWorldForLambdaIntegration` Lambda function\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "arn:aws:logs:region:account-id:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:region:account-id:log-group:/aws/lambda/GetStartedLambdaIntegration:*"
            ]
        }
    ]
}
```

The other policy document applies to invoking another AWS service that is not used in this example\. You can skip it for now\. 

 Associated with the IAM role is a trusted entity, which is `lambda.amazonaws.com`\. Here is the trust relationship: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

 The combination of this trust relationship and the inline policy makes it possible for the Lambda function to invoke a `console.log()` function to log events to CloudWatch Logs\. 

If you did not use the AWS Management Console to create the Lambda function, you need to follow these examples to create the required IAM role and policies and then manually attach the role to your function\.

## Create an API with Lambda non\-proxy integration<a name="getting-started-new-api"></a>

 With the Lambda function \(`GetStartedLambdaIntegration`\) created and tested, you are ready to expose the function through an API Gateway API\. For illustration purposes, we expose the Lambda function with a generic HTTP method\. We use the request body, a URL path variable, a query string, and a header to receive required input data from the client\. We turn on the API Gateway request validator for the API to ensure that all of the required data is properly defined and specified\. We configure a mapping template for API Gateway to transform the client\-supplied request data into the valid format as required by the backend Lambda function\.

**To create an API with Lambda custom integration with a Lambda function**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

   1. Choose **New API**\.

   1. Enter a name in **API Name**\.

   1. Optionally, add a brief description in **Description**\.

   1. Choose **Create API**\.

1. Choose the root resource \(`/`\) under **Resources**\. From the **Actions** menu, choose **Create Resource**\. 

   1. Type `city` for **Resource Name**\.

   1. Replace **Resource Path** with `{city}`\. This is an example of the templated path variable used to take input from the client\. Later, we show how to map this path variable into the Lambda function input using a mapping template\. 

   1.  Select the **Enable API Gateway Cors** option\.

   1.  Choose **Create Resource**\.

1.  With the newly created `/{city}` resource highlighted, choose **Create Method** from **Actions**\. 

   1.  Choose `ANY` from the HTTP method drop\-down menu\. The `ANY` HTTP verb is a placeholder for a valid HTTP method that a client submits at run time\. This example shows that `ANY` method can be used for Lambda custom integration as well as for Lambda proxy integration\. 

   1.  To save the setting, choose the check mark\. 

1.  In **Method Execution**, for the `/{city} ANY` method, do the following: 

   1. Choose **Lambda Function** for `Integration type`\.

   1. Leave the **Use Lambda Proxy integration** box unchecked\.

   1. Choose the region where you created the Lambda function; for example, `us-west-2`\.

   1. Type the name of your Lambda function in **Lambda Function**; for example, `GetStartedLambdaIntegration`\.

   1. Leave the **Use Default timeout** box checked\.

   1. Choose **Save**\. 

   1. Choose **OK** in the **Add Permission to Lambda Function** popup to have API Gateway set up the required access permissions for the API to invoke the integrated Lambda function\. 

1. In this step you'll configure the following:
   + A query string parameter \(`time`\)
   + A header parameter \(`day`\)
   + A payload property \(`callerName`\)

   At run time, the client can use these request parameters and the request body to provide time of the day, the day of the week, and the name of the caller\. You already configured the /\{city\} path variable\.

   1. In the **Method Execution** pane, choose **Method Request**\.

   1. Expand the **URL Query String Parameters** section\. Choose **Add query string**\. Type `time` for **Name**\. Select the **Required** option and choose the check\-mark icon to save the setting\. Leave **Caching** cleared to avoid an unnecessary charge for this exercise\.

   1. Expand the **HTTP Request Headers** section\. Choose **Add header**\. Type `day` for **Name**\. Select the **Required** option and choose the check\-mark icon to save the setting\. Leave **Caching** cleared to avoid an unnecessary charge for this exercise\. 

   1. To define the method request payload, do the following: 

      1. To define a model, choose **Models** under the API from the API Gateway primary navigation pane, and then choose **Create**\. 

      1.  Type `GetStartedLambdaIntegrationUserInput` for **Model name**\.

      1. Type `application/json` for **Content type**\.

      1. Leave the **Model description** blank\.

      1.  Copy the following schema definition into the **Model schema** editor:

         ```
         {
           "$schema": "http://json-schema.org/draft-04/schema#",
           "title": "GetStartedLambdaIntegrationInputModel",
           "type": "object",
           "properties": {
             "callerName": { "type": "string" }
           }
         }
         ```

      1. Choose **Create model** to finish defining the input model\.

      1. Choose **Resources**, choose the `/{city} ANY` method, choose **Method Request**, and expand **Request body**\. Choose **Add model**\. Type `application/json` for **Content type**\. Choose `GetStartedLambdaIntegrationInput` for **Model name**\. Choose the check\-mark icon to save the setting\. 

1. Choose the `/{city} ANY` method and choose **Integration Request** to set up a body\-mapping template\. In this step you'll map the previously configured method request parameter of `nameQuery` or `nameHeader` to the JSON payload, as required by the backend Lambda function:

   1. Expand the **Mapping Templates** section\. Choose **Add mapping template**\. Type `application/json` for **Content\-Type**\. Choose the check\-mark icon to save the setting\.

   1. In the popup that appears, choose **Yes, secure this integration**\.

   1. Check the recommended `When there are no templates defined` for **Request body passthrough**\.

   1. Choose `GetStartedLambaIntegrationUserInput` from **Generate template** to generate an initial mapping template\. This option is available because you defined a model schema, without which you would need to write the mapping template from scratch\.

   1. Replace the generated mapping script in the mapping template editor with the following:

      ```
      #set($inputRoot = $input.path('$'))
      {
        "city": "$input.params('city')",
        "time": "$input.params('time')",
        "day":  "$input.params('day')",
        "name": "$inputRoot.callerName"
      }
      ```

   1. Choose **Save**\.

## Test invoking the API method<a name="getting-started-new-get"></a>

 The API Gateway console provides a testing facility for you to test invoking the API before it is deployed\. You use the Test feature of the console to test the API by submitting the following request: 

```
POST /Seattle?time=morning
day:Wednesday

{
    "callerName": "John"
}
```

 In this test request, you'll set `ANY` to `POST`, set `{city}` to `Seattle`, assign `Wednesday` as the `day` header value, and assign `"John"` as the `callerName` value\. 

**To test\-invoke the `ANY /{city}` method**

1.  In **Method Execution**, choose **Test**\. 

1. Choose `POST` from the **Method** drop\-down list\.

1. In **Path**, type **Seattle**\.

1. In **Query Strings**, type **time=morning**\.

1. In **Headers**, type **day:Wednesday**\.

1. In **Request Body**, type **\{ "callerName":"John" \}**\.

1. Choose **Test**\.

1. Verify that the returned response payload is as follows:

   ```
   {
     "greeting": "Good morning, John of Seattle. Happy Wednesday!"
   }
   ```

1. You can also view the logs to examine how API Gateway processes the request and response\.

   ```
   Execution log for request test-request
   Thu Aug 31 01:07:25 UTC 2017 : Starting execution for request: test-invoke-request
   Thu Aug 31 01:07:25 UTC 2017 : HTTP Method: POST, Resource Path: /Seattle
   Thu Aug 31 01:07:25 UTC 2017 : Method request path: {city=Seattle}
   Thu Aug 31 01:07:25 UTC 2017 : Method request query string: {time=morning}
   Thu Aug 31 01:07:25 UTC 2017 : Method request headers: {day=Wednesday}
   Thu Aug 31 01:07:25 UTC 2017 : Method request body before transformations: { "callerName": "John" }
   Thu Aug 31 01:07:25 UTC 2017 : Request validation succeeded for content type application/json
   Thu Aug 31 01:07:25 UTC 2017 : Endpoint request URI: https://lambda.us-west-2.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-west-2:123456789012:function:GetStartedLambdaIntegration/invocations
   Thu Aug 31 01:07:25 UTC 2017 : Endpoint request headers: {x-amzn-lambda-integration-tag=test-request, Authorization=****************************************************************************************************************************************************************************************************************************************************************************************************************************************338c72, X-Amz-Date=20170831T010725Z, x-amzn-apigateway-api-id=beags1mnid, X-Amz-Source-Arn=arn:aws:execute-api:us-west-2:123456789012:beags1mnid/null/POST/{city}, Accept=application/json, User-Agent=AmazonAPIGateway_beags1mnid, X-Amz-Security-Token=FQoDYXdzELL//////////wEaDMHGzEdEOT/VvGhabiK3AzgKrJw+3zLqJZG4PhOq12K6W21+QotY2rrZyOzqhLoiuRg3CAYNQ2eqgL5D54+63ey9bIdtwHGoyBdq8ecWxJK/YUnT2Rau0L9HCG5p7FC05h3IvwlFfvcidQNXeYvsKJTLXI05/yEnY3ttIAnpNYLOezD9Es8rBfyruHfJfOqextKlsC8DymCcqlGkig8qLKcZ0hWJWVwiPJiFgL7laabXs++ZhCa4hdZo4iqlG729DE4gaV1mJVdoAagIUwLMo+y4NxFDu0r7I0/EO5nYcCrppGVVBYiGk7H4T6sXuhTkbNNqVmXtV3ch5bOlh7 [TRUNCATED]
   Thu Aug 31 01:07:25 UTC 2017 : Endpoint request body after transformations: {
     "city": "Seattle",
     "time": "morning",
     "day": "Wednesday",
     "name" : "John"
   }
   Thu Aug 31 01:07:25 UTC 2017 : Sending request to https://lambda.us-west-2.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-west-2:123456789012:function:GetStartedLambdaIntegration/invocations
   Thu Aug 31 01:07:25 UTC 2017 : Received response. Integration latency: 328 ms
   Thu Aug 31 01:07:25 UTC 2017 : Endpoint response body before transformations: {"greeting":"Good morning, John of Seattle. Happy Wednesday!"}
   Thu Aug 31 01:07:25 UTC 2017 : Endpoint response headers: {x-amzn-Remapped-Content-Length=0, x-amzn-RequestId=c0475a28-8de8-11e7-8d3f-4183da788f0f, Connection=keep-alive, Content-Length=62, Date=Thu, 31 Aug 2017 01:07:25 GMT, X-Amzn-Trace-Id=root=1-59a7614d-373151b01b0713127e646635;sampled=0, Content-Type=application/json}
   Thu Aug 31 01:07:25 UTC 2017 : Method response body after transformations: {"greeting":"Good morning, John of Seattle. Happy Wednesday!"}
   Thu Aug 31 01:07:25 UTC 2017 : Method response headers: {X-Amzn-Trace-Id=sampled=0;root=1-59a7614d-373151b01b0713127e646635, Content-Type=application/json}
   Thu Aug 31 01:07:25 UTC 2017 : Successfully completed execution
   Thu Aug 31 01:07:25 UTC 2017 : Method completed with status: 200
   ```

   The logs show the incoming request before the mapping and the integration request after the mapping\. When a test fails, the logs are useful for evaluating whether the original input is correct or the mapping template works correctly\. 

## Deploy the API<a name="getting-started-deploy-api"></a>

 The test invocation is a simulation and has limitations\. For example, it bypasses any authorization mechanism enacted on the API\. To test the API execution in real time, you must deploy the API first\. To deploy an API, you create a stage to create a snapshot of the API at that time\. The stage name also defines the base path after the API's default host name\. The API's root resource is appended after the stage name\. When you modify the API, you must redeploy it to a new or existing stage before the changes take effect\. 

**To deploy the API to a stage**

1.  Choose the API from the **APIs** pane or choose a resource or method from the **Resources** pane\. Choose **Deploy API** from the **Actions** drop\-down menu\.

1. For **Deployment stage**, choose **New Stage**\.

1. For **Stage name**, type a name; for example, **test**\.
**Note**  
The input must be UTF\-8 encoded \(i\.e\., unlocalized\) text\.

1. For **Stage description**, type a description or leave it blank\. 

1. For **Deployment description**, type a description or leave it blank\. 

1.  Choose **Deploy**\. After the API is successfully deployed, you see the API's base URL \(the default host name plus the stage name\) displayed as **Invoke URL** at the top of the **Stage Editor**\. The general pattern of this base URL is `https://api-id.region.amazonaws.com/stageName`\. For example, the base URL of the API \(`beags1mnid`\) created in the `us-west-2` region and deployed to the `test` stage is `https://beags1mnid.execute-api.us-west-2.amazonaws.com/test`\.

## Test the API in a deployment stage<a name="getting-started-test"></a>

There are several ways you can test a deployed API\. For GET requests using only URL path variables or query string parameters, you can type the API resource URL in a browser\. For other methods, you must use more advanced REST API testing utilities, such as [POSTMAN](https://www.postman.com/) or [cURL](https://curl.haxx.se/)\.

**To test the API using cURL**

1. Open a terminal window on your local computer connected to the internet\.

1. To test `POST /Seattle?time=evening`:

   Copy the following cURL command and paste it into the terminal window\.

   ```
   curl -v -X POST \
     'https://beags1mnid.execute-api.us-west-2.amazonaws.com/test/Seattle?time=evening' \
     -H 'content-type: application/json' \
     -H 'day: Thursday' \
     -H 'x-amz-docs-region: us-west-2' \
     -d '{
   	"callerName": "John"
   }'
   ```

   You should get a successful response with the following payload:

   ```
   {"greeting":"Good evening, John of Seattle. Happy Thursday!"}
   ```

   If you change `POST` to `PUT` in this method request, you get the same response\.

1. To test `GET /Boston?time=morning`:

   Copy the following cURL command and paste it into the terminal window\.

   ```
   curl -X GET \
     'https://beags1mnid.execute-api.us-west-2.amazonaws.com/test/Boston?time=morning' \
     -H 'content-type: application/json' \
     -H 'day: Thursday' \
     -H 'x-amz-docs-region: us-west-2' \
     -d '{
   	"callerName": "John"
   }'
   ```

   You get a `400 Bad Request` response with the following error message:

   ```
   {"message": "Invalid request body"}
   ```

   This is because the `GET` request that you submitted cannot take a payload and fails the request validation\.

## Clean up<a name="getting-started-clean-up"></a>

If you no longer need the Lambda functions you created for this walkthrough, you can delete them now\. You can also delete the accompanying IAM resources\.

**Warning**  
If you plan to complete the other walkthroughs in this series, do not delete the Lambda execution role or the Lambda invocation role\. If you delete a Lambda function that your APIs rely on, those APIs will no longer work\. Deleting a Lambda function cannot be undone\. If you want to use the Lambda function again, you must re\-create the function\.  
If you delete an IAM resource that a Lambda function relies on, that Lambda function will no longer work, and any APIs that rely on that function will no longer work\. Deleting an IAM resource cannot be undone\. If you want to use the IAM resource again, you must re\-create the resource\. 

**To delete the Lambda functions**

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. From the list of functions, choose **GetHelloWorld**, choose **Actions**, and then choose **Delete function**\. When prompted, choose **Delete** again\.

1. From the list of functions, choose **GetHelloWithName**, choose **Actions**, and then choose **Delete function**\. When prompted, choose **Delete** again\.

**To delete the associated IAM resources**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From **Details**, choose **Roles**\.

1. From the list of roles, choose **APIGatewayLambdaExecRole**, choose **Role Actions**, and then choose **Delete Role**\. When prompted, choose **Yes, Delete**\.

1. From **Details**, choose **Policies**\.

1. From the list of policies, choose **APIGatewayLambdaExecPolicy**, choose **Policy Actions**, and then choose **Delete**\. When prompted, choose **Delete**\.

You have now reached the end of this walkthrough\.