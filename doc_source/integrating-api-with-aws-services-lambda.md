# Create an API Gateway API for AWS Lambda Functions<a name="integrating-api-with-aws-services-lambda"></a>

**Note**  
 To integrate your API Gateway API with Lambda, you must choose a region where both the API Gateway and Lambda services are available\. For region availability, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#apigateway_region)\. 

 In the [Getting Started](getting-started-with-lambda-integration.md) section, you learned how to use the API Gateway console to build an API to expose a Lambda function\. There, the console let you choose `Lambda Function` for **Integration type**, among other options of `HTTP`, `Mock` and `AWS Service`\. The `Lambda Function` option is a special case of the `AWS Service` integration type and simplifies the integration set\-up for you with default settings\. For example, with the former, the console automatically adds the required resource\-based permissions for invoking the Lambda function\. With the latter, you have more control, but more responsibilities to set up the integration, including creating and specifying an IAM role containing appropriate permissions\. For the both options, the underlying [integration\.type](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#type) is `AWS` in the API Gateway REST API and its Swagger definition file\. 

In this section, we walk you through the steps to integrate an API with a Lambda function using the `AWS Service` and `Lambda Function` integration types\. To support asynchronous invocation of the Lambda function, you must explicitly add the `X-Amz-Invocation-Type:Event` header to the integration request\. For the synchronous invocation, you can add the `X-Amz-Invocation-Type:RequestResponse` header to the integration request or leave it unspecified\. The following example shows the integration request of an asynchronous Lambda function invocation: 

```
POST /2015-03-31/functions/FunctionArn/invocations?Qualifier=Qualifier HTTP/1.1 
X-Amz-Invocation-Type: Event 
...
Authorization: ...
Content-Type: application/json
Content-Length: PayloadSize

Payload
```

 In this example, *FunctionArn* is the ARN of the Lambda function to be invoked\. The `Authorization` header is required by secure invocation of Lambda functions over HTTPS\. For more information, see the `[Invoke](http://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html)` action in the *AWS Lambda Developer Guide*\. 

 To illustrate how to create and configure an API as an AWS service proxy for Lambda, we will create a Lambda function \(`Calc`\) that performs addition \(\+\), subtraction \(\-\), multiplication \(\*\), and division \(/\)\. When a client submits a method request to perform any of these operations, API Gateway will post the corresponding integration request to call the specified Lambda function, passing the required input \(two operands and one operator\) as a JSON payload\. A synchronous call will return the result, if any, as the JSON payload\. An asynchronous call will return no data\. 

You can expose a GET or POST method on the `/calc` resource to invoke the Lambda function\. With the GET method, a client supplies the input to the backend Lambda function through three query string parameters \(`operand1`, `operand2`, and `operator`\)\. You will set up a mapping template to map these to the JSON payload of the integration request\. With the POST method, a client provides the input to the Lambda function as a JSON payload of the method request\. You can pass the method request payload through to the integration request, if the client input conforms to the input model\. Alternatively, you can expose a GET method on the `/calc/{operand1}/{operand2}/{operator}` resource\. With this method, the client specifies the Lambda function input as the values of the path parameters\. You will need to provide a mapping template to translate the path parameters of the method request into an integration request payload as the Lambda function input and to translate the output from the integration responses to the method response\. 

In this tutorial, we will cover the following topics: 
+  Create the `Calc` Lambda function to implement the arithmetic operations, accepting and returning JSON\-formatted input and output\. 
+ Expose GET on the `/calc` resource to invoke the Lambda function, supplying the input as query strings\. We will enable a request validator to ensure that the client submit all the required query string parameters before API Gateway calling the Lambda function\. 
+ Expose POST on the `/calc` resource to invoke the Lambda function, supplying the input in the payload\. We will enable a request validator to ensure that the client submitted the valid request payload before API Gateway call the Lambda function\.
+  Expose GET on the `/calc/{operand1}/{operand2}/{operator}` resource to invoke the Lambda function, specifying the input in the path parameters\. We also explain how to define a `Result` schema to model the method response body so that any strongly typed SDK of the API can access the method response data through properties defined in the `Result` schema\. 

 You can inspect the sample API in its [Swagger definition file](api-as-lambda-proxy-export-swagger-with-extensions.md)\. You can also [import](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/) the API Swagger definitions to API Gateway, following the instructions given in [Import an API into API Gateway](api-gateway-import-api.md)\.

 To use the API Gateway console to create the API, you must first sign up for an AWS account\. 

If you do not have an AWS account, use the following procedure to create one\.

**To sign up for AWS**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/) and choose **Create an AWS Account**\.

1. Follow the online instructions\.

 To allow the API to invoke Lambda functions, you must have an IAM role that has appropriate IAM policies attached to it\. The next section describes how to verify and to create, if necessary, the required IAM role and policies\. 

**Topics**
+ [Set Up an IAM Role and Policy for an API to Invoke Lambda Functions](#api-as-lambda-proxy-setup-iam-role-policies)
+ [Create a Lambda Function in the Backend](#api-as-lambda-proxy-create-lambda-function)
+ [Create API Resources for the Lambda Function](#api-as-lambda-proxy-create-api-resources)
+ [Create a GET Method with Query Parameters to Call the Lambda Function](#api-as-lambda-proxy-expose-get-method-with-query-strings-to-call-lambda-function)
+ [Create a POST Method with a JSON Payload to Call the Lambda Function](#api-as-lambda-proxy-expose-post-method-with-json-body-to-call-lambda-function)
+ [Create a GET Method with Path Parameters to Call the Lambda Function](#api-as-lambda-proxy-expose-get-method-with-path-parameters-to-call-lambda-function)
+ [Swagger Definitions of Sample API Integrated with a Lambda Function](api-as-lambda-proxy-export-swagger-with-extensions.md)

## Set Up an IAM Role and Policy for an API to Invoke Lambda Functions<a name="api-as-lambda-proxy-setup-iam-role-policies"></a>

 For API Gateway to invoke a Lambda function, the API must have a permission to call the Lambda's [InvokeFunction](http://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) action\. This means that, at minimum, you must attach the following IAM policy to an IAM role for API Gateway to assume the policy\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "*"
        }
    ]
}
```

 If you do not enact this policy, the API caller will receive a 500 Internal Server Error response\. The response contains the "Invalid permissions on Lambda function" error message\. For a complete list of error messages returned by Lambda, see the [Invoke](http://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) topic\. 

 An API Gateway assumable role is an IAM role with the following trusted relationship: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## Create a Lambda Function in the Backend<a name="api-as-lambda-proxy-create-lambda-function"></a>

The following procedure outlines the steps to create a Lambda function using the Lambda console\.

1. Go to the Lambda console\.

1. Choose **Create a Lambda function**\.

1. Choose the **Blank Function** blueprint for the runtime of Node\.js 4\.3 or later\.

1. Follow the on\-screen instructions to the **Lambda function code** editor\.

1.  Copy the following Lambda function and paste it into the code editor in the Lambda console\. 

   ```
   console.log('Loading the Calc function');
   
   exports.handler = function(event, context, callback) {
       console.log('Received event:', JSON.stringify(event, null, 2));
       if (event.a === undefined || event.b === undefined || event.op === undefined) {
           callback("400 Invalid Input");
       }
       
       var res = {};
       res.a = Number(event.a);
       res.b = Number(event.b);
       res.op = event.op;
       
       if (isNaN(event.a) || isNaN(event.b)) {
           callback("400 Invalid Operand");
       }
   
       switch(event.op)
       {
           case "+":
           case "add":
               res.c = res.a + res.b;
               break;
           case "-":
           case "sub":
               res.c = res.a - res.b;
               break;
           case "*":
           case "mul":
               res.c = res.a * res.b;
               break;
           case "/":
           case "div":
               res.c = res.b===0 ? NaN : Number(event.a) / Number(event.b);
               break;
           default:
               callback("400 Invalid Operator");
               break;
       }
       callback(null, res);
   };
   ```

1. Choose an existing or create a new IAM role

1. Follow the on\-screen instructions to finish creating the function\.

 This function requires two operands \(`a` and `b`\) and an operator \(`op`\) from the `event` input parameter\. The input is a JSON object of the following format: 

```
{
  "a": "Number" | "String",
  "b": "Number" | "String",
  "op": "String"
}
```

This function returns the calculated result \(`c`\) and the input\. For an invalid input, the function returns either the null value or the "Invalid op" string as the result\. The output is of the following JSON format: 

```
{
  "a": "Number",
  "b": "Number",
  "op": "String",
  "c": "Number" | "String"
}
```

You should test the function in the Lambda console before integrating it with the API in the next step\. 

## Create API Resources for the Lambda Function<a name="api-as-lambda-proxy-create-api-resources"></a>

The following procedure describes how to create API resources for the Lambda function\. As an illustration, we use multiple API resources and methods to enable different API behaviors for calling the same function\.

**To create API resources for Lambda functions**

1.  In the API Gateway console, create an API named **LambdaGate**\. 

1.  Create the **/calc** resource off the API's root\. We will expose the GET and POST methods on this resource for the client to invoke the backend Lambda function\. The caller must supply the required input as query string parameters \(to be declared as `?operand1=...&operand2=...&operator=...`\) in the GET request and as a JSON payload in the POST request, respectively\. 

    We will also create the **/calc/\{operand1\}/\{operand2\}/\{operator\}** resource subtree to expose the GET method to invoke the Lambda function\. The caller must supply the required input by specifying the three path parameters \(**operand1**, **operand2**, and **operator**\)\.   
![\[Create an API in API Gateway as a Lambda proxy\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_create_api_resources.png)

## Create a GET Method with Query Parameters to Call the Lambda Function<a name="api-as-lambda-proxy-expose-get-method-with-query-strings-to-call-lambda-function"></a>

 By creating a GET method with query parameters to call the Lambda function, we can let the API user to do the calculations via any browser\. This can be useful especially if the API allows open access\. 

**To set up the GET method with query strings to invoke the Lambda function**

1.  In the API Gateway console, choose the API's **/calc** resource under **Resources**\.

1. Choose **Create Method**, from the **Actions** drop\-down menu, to create the GET method\. 

1.  In the ensuing **Set up** pane, 

   1. Choose `AWS Service` for **Integration type**\.

   1. Choose the region \(e\.g\., `us-west-2`\) where you created the Lambda function for **AWS Region**\.

   1. Choose `Lambda` for **AWS Service**\.

   1. Leave **AWS Subdomain** blank because our Lambda function is not hosted on any of AWS subdomain\.

   1. Choose `POST` for **HTTP method**\. Lambda requires that the `POST` request be used to invoke any Lambda function\. This examples shows that the HTTP method in a frontend method request can be different from the integration request in the backend\.

   1. Choose `Use path override` for **Action Type**\. This option allows us to specify the ARN of the [Invoke](http://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) action to execute our `Calc` function\.

   1. Type `/2015-03-31/functions/arn:aws:lambda:region:account-id:function:Calc/invocations` in **Path override**\.

   1. Specify the ARN of an IAM role for **Execution role**\. You can find the ARN of the role in the IAM console\. The role must contain the necessary permissions for the caller to call the `Calc` function and for API Gateway to assume the role of the caller\.

   1. Leave the `Passthrough` as the default of **Content Handling**, because we will not deal with any binary data\.

   1. Choose the **Save** to finish setting up the `GET /calc` method\.

   After the setup succeeds, the configuration should look as follows:   
![\[Set up a method for integration with Lambda\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_settings.png)

    You can also add, in **Integration Request**, the `X-Amz-Invocation-Type: Event | RequestReponse | DryRun` header to have the action invoked asynchronously, as request and response, or as a test run, respectively\. If the header is not specified, the action will be invoked as request and response\. 

1.  Go to **Method Request** to set up query parameters for the **GET** method on **/calc** to receive input to the backend Lambda function\.

   1. Choose the pencil icon next to **Request Validator** to select `Validate query string parameters and headers`\. This setting will cause an error message to return to state the required parameters are missing if the client does not specify them\. You will not get charged for the call to the backend\.

   1. Expand the **URL Query String Parameters** section\. Choose **Add query string** to add the **operand1**, **operand2**, and **operator** query string parameters\. Check the **Required** option for each parameter to ensure that they are validated\.

   The configuration now looks as follows:  
![\[Set up a method request URL query strings for integration with Lambda\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_query_strings.png)

1.  Go back to **Integration Request** to set up the mapping template to translate the client\-supplied query strings to the integration request payload as required by the `Calc` function\.

   1. Expand the **Body Mapping Templates** section\.

   1. Choose `When no template matches the request Content-Type header` for **Request body passthrough**\. 

   1. If `application/json` is not shown under **Content\-Type**, choose **Add mapping template** to add it\. 

   1. And then type and save the following mapping script in the mapping template editor: 

      ```
      {
          "a":  "$input.params('operand1')",
          "b":  "$input.params('operand2')", 
          "op": "$input.params('operator')"   
      }
      ```

       This template maps the three query string parameters declared in **Method Request** into designated property values of the JSON object as the input to the backend Lambda function\. The transformed JSON object will be included as the integration request payload\. 

   The final settings of this step is shown as follows:  
![\[Map a method request URL query strings to integration request payload to call Lambda function\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_query_strings_mapping.png)

1.  You can now choose **Test** to verify that the GET method on the **/calc** resource has been properly set up to invoke the Lambda function\. 

## Create a POST Method with a JSON Payload to Call the Lambda Function<a name="api-as-lambda-proxy-expose-post-method-with-json-body-to-call-lambda-function"></a>

By creating a POST method with a JSON payload to call the Lambda function, we expect the client to submit the necessary input to the backend function in the request body\. To ensure that the client uploads the correct input data, we will enable request validation on the payload\.

**To set up the POST method with a JSON payload to invoke a Lambda function**

1. Go to the API Gateway console and choose the API created previously\.

1. Highlight the **/calc** resource from **Resources** pane\.

1.  Choose **Create Method** from the **Actions** menu to create the **POST /calc** method\. 

1.  In the method's **Set Up** panel, configure this POST method with the following integration settings\. For more information, follow the discussions in [ Create a GET Method with Query Parameters to Call the Lambda Function ](#api-as-lambda-proxy-expose-get-method-with-query-strings-to-call-lambda-function)\.  
![\[Set up the POST method to invoke the Lambda function\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_post_method_settings.png)

1. Choose **Models** under the API from the API Gateway console's primary navigation pane to create data models for the method's input and output:

   1. Choose **Create** in the **Models** pane\. Type `Input` in **Model name**, type `application/json` in **Content type**, and type the following schema definition in **Model schema**: 

      ```
      {
          "type":"object",
          "properties":{
              "a":{"type":"number"},
              "b":{"type":"number"},
              "op":{"type":"string"}
          },
          "title":"Input"
      }
      ```

      This model describes the input data structure and will be used to validate the incoming request body\.

   1. Choose **Create** in the **Models** pane\. Type `Output` in **Model name**, type `application/json` in **Content type**, and type the following schema definition in **Model schema**: 

      ```
      {
          "type":"object",
          "properties":{
              "c":{"type":"number"}
          },
          "title":"Output"
      }
      ```

      This model describes the data structure of the calculated output from the backend\. It can be used to map the integration response data to a different model\. This tutorial relies on the passthrough behavior and does not use this model\.

   1. Choose **Create** in the **Models** pane\. Type `Result` in **Model name**, type `application/json` in **Content type**, and type the following schema definition in **Model schema**: 

      ```
      {
          "type":"object",
          "properties":{
              "input":{
                  "$ref":"https://apigateway.amazonaws.com/restapis/restapi-id/models/Input"
              },
              "output":{
                  "$ref":"https://apigateway.amazonaws.com/restapis/restapi-id/models/Output"
              }
          },
          "title":"Output"
      }
      ```

      This model describes the data structure of the returned response data\. It references both the `Input` and `Output` schemas defined in the specified API \(`restapi-id`\)\. Again, this model is not used in this tutorial because it leverages the passthrough behavior\.

1. In the **Method Request** configuration settings, do the following to enable request validation on the incoming request body:

   1. Choose the pencil icon next to **Request Validator** to choose `Validate body`\.

   1. Expand the **Request Body** section, choose **Add model**

   1. Type `application/json` in the **Content\-Type** input field and choose `Input` from the drop\-down list in the **Model name** column\.

1.  You can now choose **Test** to verify the POST method works as expected\. The following input: 

   ```
   {
       "a": 1,
       "b": 2,
       "op": "+"
   }
   ```

    should produce the following output:

   ```
   {
     "a": 1,
     "b": 2,
     "op": "+",
     "c": 3
   }
   ```

 If you would like to implement this method as an asynchronous call, you can add an `InvocationType` header in the method request and map it to the `X-Amz-Invocation-Type` header in the integration request with either a static value of `'Event'` or the header mapping expression of `method.request.header.InvocationType`\. For the latter, the client must include the `InvocationType:Event` header in the method request\. The asynchronous call will return an empty response, instead\. 

## Create a GET Method with Path Parameters to Call the Lambda Function<a name="api-as-lambda-proxy-expose-get-method-with-path-parameters-to-call-lambda-function"></a>

 In this section, we create a GET method on a resource specified by a sequence of path parameters to call the backend Lambda function\. The path parameter values specify the input data to the Lambda function\. We will define a mapping template to map the incoming path parameter values to the required integration request payload\. 

In addition, we will use the simple Lambda integration feature provided by the API Gateway console to set up the method\. As you can see, this console\-provided feature provides much more streamlined user experiences\. 

**To set up the GET method with URL path parameters to call the Lambda function**

1. Go to the API Gateway console\. 

1. Highlight the **/calc/\{operand1\}/\{operand2\}/\{operator\}** resource on the **Resources** tree of the previously created API

1. Choose **Create Method** from the **Actions** drop\-down menu, choose `GET`\.

1. In the **Setup** pane, choose `Lambda Function` for **Integration type**, to use the streamlined setup process enabled by the console\.

1. Choose a region \(e\.g\., `us-west-2`\) for **Lambda Region**\. This is the region where the Lambda function is hosted\.

1. Choose an existing Lambda function \(e\.g\., `Calc`\) for **Lambda Function**\. 

1. Choose **Save** and then choose **OK** to consent to **Add Permissions to Lambda Function**\.

1. Choose **Integration Request** to set up body mapping template\.

   1. Expand the **Body Mapping Templates** section\.

   1. Choose **Add mapping template**\.

   1. Type `application/json` for **Content\-Type** and then choose the check mark icon to open the template editor\.

   1. Choose **Yes, secure this integration** to proceed\.

   1. Type the following mapping script to the template editor:

      ```
      {
         "a": "$input.params('operand1')",
         "b": "$input.params('operand2')",
         "op": #if($input.params('operator')=='%2F')"/"#{else}"$input.params('operator')"#end
         
      }
      ```

      This template maps the three URL path parameters, declared when the **/calc/\{operand1\}/\{operand2\}/\{operator\}** resource was created, into designated property values of the JSON object\. Because URL paths must be URL\-encoded, the division operator must be specified as `%2F` instead of `/`\. This template translates the `%2F` into '`/` before passing it to the Lambda function\. 

   1. Save the mapping template\.

    When the method is set up correctly, the settings should look similar to the following:   
![\[Set up the GET method with path parameters to invoke the Lambda function\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_with_paths_settings.png)

1. Now, let us test the API using the TestInvoke feature of the console\. 

   1. Choose **Test** from **Method Execution**\.

   1. Type `1`, `2` and `+` in **\{operand1\}**, **\{operand2\}** and **\{operator\}** fields, respectively\.

   1. Choose **Test**\.

   1. The result will be shown similar to the following:  
![\[Map a method request URL path parameters to the integration request payload to call the Lambda function\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_test_result.png)

   This test result shows the original output from the backend Lambda function, as passed through the integration response without mapping, because we have not configured any mapping template\. Next, we model the data structure of the method response payload after the `Result` schema\.

1.  By default, the method response body is assigned an Empty model\. This will cause the integration response body passed through without mapping\. However, when you generate an SDK for one of the strongly\-type languages, such as Java or Objective\-C, your SDK users will receive an empty object as the result\. To ensure both the REST client and SDK clients receive the desired result, you must model the response data using a predefined schema\. Here, we demonstrate how to define a model for the method response body and to construct a mapping template to transform the integration response body to the method response body\. 

   1. In **/calc/\{operand1\}/\{operand2\}/\{operator\} \- GET \- Method Execution**, choose **Method Response**\.

   1. Expand the **200** response, 

   1. Under **Response Body for 200** section\. If no model has been assigned for the method response, 

   1. Choose the pencil icon next to the model for the `application/json` content type\. 

   1. Choose a predefined model from the **Models** drop\-down list\. For this tutorial, this is `Result`\. 

   1. Save the model choice\. 
**Note**  
If no model is defined for the content type of `application/json`, choose **Add Response Model** and follow the on\-screen instructions to add the model\.

   Setting the model for the method response body ensure that the response data will be cast into the `Result` object of a given SDK\. For this, we also need to make sure that the integration response data is mapped accordingly, which we discuss next\.

1. To return the backend result according to the specified schema, 

   1.  Choose **Integration Response** and expand the 200 method response entry\.

   1. Expand the **Body Mapping Templates** section\.

   1. Choose or add `application/json` to the **Content\-Type** list\.

   1. Choose `Result` from the **Generate template** drop\-down list to bring up the `Result` template blueprint\.

   1. Change the template blueprint as follows:

      ```
      #set($inputRoot = $input.path('$'))
      {
        "input" : {
          "a" : $inputRoot.a,
          "b" : $inputRoot.b,
          "op" : "$inputRoot.op"
        },
        "output" : {
          "c" : $inputRoot.c
        }
      }
      ```

   1. Choose **Save**\.

   1. To test the mapping template, choose **Test** in **Method Execution** and type `1` `2` and `+` in the **operand1**, **operand2** and **operator** input fields, respectively\. The integration response from the Lambda function is now mapped to a `Result` object:

      ```
      {
        "input": {
          "a": 1,
          "b": 2,
          "op": "+"
        },
        "output": {
          "c": 3
        }
      }
      ```

1. To make the API accessible beyond Test Invoke in the API Gateway console, choose **Deploy API** from the **Actions** drop\-down menu\. Make sure to repeat deploying the API whenever you finish adding, modifying or deleting a resource or method, updating any data mapping, updating the stage settings\. Otherwise, new features or updates will not be available\.