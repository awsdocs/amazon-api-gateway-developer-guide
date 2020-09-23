# Tutorial: Create a `Calc` REST API with two AWS service integrations and one Lambda non\-proxy integration<a name="integrating-api-with-aws-services-lambda"></a>

The [Getting Started non\-proxy integration tutorial](getting-started-lambda-non-proxy-integration.md) uses `Lambda Function` integration exclusively\. `Lambda Function` integration is a special case of the `AWS Service` integration type that performs much of the integration setup for you, such as automatically adding the required resource\-based permissions for invoking the Lambda function\. Here, two of the three integrations use `AWS Service` integration\. In this integration type, you have more control, but you'll need to manually perform tasks like creating and specifying an IAM role containing appropriate permissions\.

In this tutorial, you'll create a `Calc` Lambda function that implements basic arithmetic operations, accepting and returning JSON\-formatted input and output\. Then you'll create a REST API and integrate it with the Lambda function in the following ways:

1. By exposing a `GET` method on the `/calc` resource to invoke the Lambda function, supplying the input as query string parameters\. \(`AWS Service` integration\)

1. By exposing a `POST` method on the `/calc` resource to invoke the Lambda function, supplying the input in the method request payload\. \(`AWS Service` integration\)

1. By exposing a `GET` on nested `/calc/{operand1}/{operand2}/{operator}` resources to invoke the Lambda function, supplying the input as path parameters\. \(`Lambda Function` integration\)

In addition to trying out this tutorial, you may wish to study the [OpenAPI definition file](api-as-lambda-proxy-export-swagger-with-extensions.md) for the `Calc` API, which you can import into API Gateway by following the instructions in [Configuring a REST API using OpenAPI](api-gateway-import-api.md)\.

**Topics**
+ [Create an AWS account](#w108aac11c17c15)
+ [Create an assumable IAM role](#api-as-lambda-proxy-setup-iam-role-policies)
+ [Create a `Calc` Lambda function](#api-as-lambda-proxy-create-lambda-function)
+ [Test the `Calc` Lambda function](#api-as-lambda-proxy-create-lambda-function)
+ [Create a `Calc` API](#api-as-lambda-proxy-create-api-resources)
+ [Integration 1: Create a `GET` method with query parameters to call the Lambda function](#api-as-lambda-proxy-expose-get-method-with-query-strings-to-call-lambda-function)
+ [Integration 2: Create a `POST` method with a JSON payload to call the Lambda function](#api-as-lambda-proxy-expose-post-method-with-json-body-to-call-lambda-function)
+ [Integration 3: Create a `GET` method with path parameters to call the Lambda function](#api-as-lambda-proxy-expose-get-method-with-path-parameters-to-call-lambda-function)
+ [OpenAPI definitions of sample API integrated with a Lambda function](api-as-lambda-proxy-export-swagger-with-extensions.md)

## Create an AWS account<a name="w108aac11c17c15"></a>

Before you begin this tutorial, you'll need an AWS account\. 

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Create an assumable IAM role<a name="api-as-lambda-proxy-setup-iam-role-policies"></a>

In order for your API to invoke your `Calc` Lambda function, you'll need to have an API Gateway assumable IAM role, which is an IAM role with the following trusted relationship:

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

The role you create will need to have Lambda [InvokeFunction](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) permission\. Otherwise, the API caller will receive a `500 Internal Server Error` response\. To give the role this permission, you'll attach the following IAM policy to it:

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

Here's how to accomplish all this:

**Create an API Gateway assumable IAM role**

1. Log in to to the IAM console\.

1. Choose **Roles**\.

1. Choose **Create Role**\.

1. Under **Select type of trusted entity**, choose **AWS Service**\.

1. Under **Choose the service that will use this role**, choose **Lambda**\.

1. Choose **Next: Permissions**\.

1. Choose **Create Policy**\.

   A new **Create Policy** console window will open up\. In that window, do the following:

   1. In the **JSON** tab, replace the existing policy with the following:

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

   1. Choose **Review policy**\.

   1. Under **Review Policy**, do the following:

      1. For **Name**, type a name such as **lambda\_execute**\.

      1. Choose **Create Policy**\.

1. In the original **Create Role** console window, do the following:

   1. Under **Attach permissions policies**, choose your **lambda\_execute** policy from the dropdown list\.

      If you don't see your policy in the list, choose the refresh button at the top of the list\. \(Don't refresh the browser page\!\)

   1. Choose **Next:Tags**\.

   1. Choose **Next:Review**\.

   1. For the **Role name**, type a name such as **lambda\_invoke\_function\_assume\_apigw\_role**\.

   1. Choose **Create role**\.

1. Choose your **lambda\_invoke\_function\_assume\_apigw\_role** from the list of roles\.

1. Choose the **Trust relationships** tab\.

1. Choose **Edit trust relationship**\.

1. Replace the existing policy with the following:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "lambda.amazonaws.com",
             "apigateway.amazonaws.com"
           ]
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Choose **Update Trust Policy**\.

1. Make a note of the role ARN for the role you just created\. You'll need it later\.

## Create a `Calc` Lambda function<a name="api-as-lambda-proxy-create-lambda-function"></a>

Next you'll create a Lambda function using the Lambda console\.

1. In the Lambda console, choose **Create function**\.

1. Choose **Author from Scratch**\.

1. For **Name**, enter **Calc**\.

1. Set the **Runtime** to a supported Node\.js runtime\.

1. Choose **Create function**\.

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

1. Under Execution role, choose **Choose an existing role**\.

1. Enter the role ARN for the **lambda\_invoke\_function\_assume\_apigw\_role** role you created earlier\.

1. Choose **Save**\.

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

## Test the `Calc` Lambda function<a name="api-as-lambda-proxy-create-lambda-function"></a>

Here's how to test your `Calc` function in the Lambda console:

1. In the **Saved test events** dropdown menu, choose **Configure test events**\.

1. For the test event name, enter **calc2plus5**\.

1. Replace the test event definition with the following:

   ```
   {
     "a": "2",
     "b": "5",
     "op": "+"
   }
   ```

1. Choose **Save**\.

1. Choose **Test**\.

1. Expand **Execution result: succeeded**\. You should see the following:

   ```
   {
     "a": 2,
     "b": 5,
     "op": "+",
     "c": 7
   }
   ```

## Create a `Calc` API<a name="api-as-lambda-proxy-create-api-resources"></a>

The following procedure shows how to create an API for the `Calc` Lambda function you just created\. In subsequent sections, you'll add resources and methods to it\.

**Create the `Calc` API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

1. Under **Create new API**, choose **New API**\.

1. For **API Name**, enter **LambdaCalc**\.

1. Leave the **Description** blank, and leave the **Endpoint Type** set to **Regional**\.

1. Choose **Create API**\.

## Integration 1: Create a `GET` method with query parameters to call the Lambda function<a name="api-as-lambda-proxy-expose-get-method-with-query-strings-to-call-lambda-function"></a>

By creating a `GET` method that passes query string parameters to the Lambda function, you enable the API to be invoked from a browser\. This approach can be useful, especially for APIs that allow open access\.

**To set up the `GET` method with query string parameters**

1. In the API Gateway console, under your `LambdaCalc` API's **Resources**, choose **/**\.

1. In the **Actions** drop\-down menu, choose **Create Resource**\. 

1. For **Resource Name**, enter **calc**\.

1. Choose **Create Resource**\.

1. Choose the **/calc** resource you just created\.

1. In the **Actions** drop\-down menu, choose **Create Method**\. 

1. In the method drop\-down menu that appears, choose **GET**\.

1. Choose the checkmark icon to save your choice\.

1. In the **Set up** pane:

   1. For **Integration type**, choose **AWS Service**\.

   1. For **AWS Region**, choose the region \(e\.g\., `us-west-2`\) where you created the Lambda function\.

   1. For **AWS Service**, choose **Lambda**\.

   1. Leave **AWS Subdomain** blank, because our Lambda function is not hosted on any AWS subdomain\.

   1. For **HTTP method**, choose **POST** and choose the checkmark icon to save your choice\. Lambda requires that the `POST` request be used to invoke any Lambda function\. This example shows that the HTTP method in a frontend method request can be different from the integration request in the backend\.

   1. Choose `Use path override` for **Action Type**\. This option allows us to specify the ARN of the [Invoke](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) action to execute our `Calc` function\.

   1. Enter `/2015-03-31/functions/arn:aws:lambda:region:account-id:function:Calc/invocations` in **Path override**, where *region* is the region where you created your Lambda function and *account\-id* is the account number for the AWS account\.

   1. For **Execution role**, enter the role ARN for the **lambda\_invoke\_function\_assume\_apigw\_role** IAM role you created [earlier](#api-as-lambda-proxy-setup-iam-role-policies)\.

   1. Leave **Content Handling** set to **Passthrough**, because this method will not deal with any binary data\.

   1. Leave **Use default timeout** checked\.

   1. Choose **Save**\.

1. Choose **Method Request**\.

   Now you will set up query parameters for the **GET** method on **/calc** so it can receive input on behalf of the backend Lambda function\.

   1. Choose the pencil icon next to **Request Validator** and choose **Validate query string parameters and headers** from the drop\-down menu\. This setting will cause an error message to return to state the required parameters are missing if the client does not specify them\. You will not get charged for the call to the backend\.

   1. Choose the checkmark icon to save your changes\.

   1. Expand the **URL Query String Parameters** section\. 

   1. Choose **Add query string**\.

   1. For **Name**, type **operand1**\.

   1. Choose the checkmark icon to save the parameter\.

   1. Repeat the previous steps to create parameters named **operand2** and **operator**\.

   1. Check the **Required** option for each parameter to ensure that they are validated\.

1. Choose **Method Execution** and then choose **Integration Request** to set up the mapping template to translate the client\-supplied query strings to the integration request payload as required by the `Calc` function\.

   1. Expand the **Mapping Templates** section\.

   1. Choose **When no template matches the request Content\-Type header** for **Request body passthrough**\. 

   1. Under **Content\-Type**, choose **Add mapping template**\.

   1. Type **application/json** and choose the checkmark icon to open the template editor\.

   1. Choose **Yes, secure this integration** to proceed\.

   1. Copy the following mapping script into the mapping template editor: 

      ```
      {
          "a":  "$input.params('operand1')",
          "b":  "$input.params('operand2')", 
          "op": "$input.params('operator')"   
      }
      ```

       This template maps the three query string parameters declared in **Method Request** into designated property values of the JSON object as the input to the backend Lambda function\. The transformed JSON object will be included as the integration request payload\. 

   1. Choose **Save**\.

1. Choose **Method Execution**\.

1. You can now test your `GET` method to verify that it has been properly set up to invoke the Lambda function:

   1. For **Query Strings**, type **operand1=2&operand2=3&operator=\+**\.

   1. Choose **Test**\.

      The results should look similar to this:  
![\[Create an API in API Gateway as a Lambda proxy\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_test.png)

## Integration 2: Create a `POST` method with a JSON payload to call the Lambda function<a name="api-as-lambda-proxy-expose-post-method-with-json-body-to-call-lambda-function"></a>

By creating a `POST` method with a JSON payload to call the Lambda function, you make it so that the client must provide the necessary input to the backend function in the request body\. To ensure that the client uploads the correct input data, you'll enable request validation on the payload\.

**To set up the `POST` method with a JSON payload to invoke a Lambda function**

1. Go to the API Gateway console\.

1. Choose **APIs**\.

1. Choose the **LambdaCalc** API you created previously\.

1. Choose the **/calc** resource from **Resources** pane\.

1. In the **Actions** menu, choose **Create Method**\.

1. Choose **POST** from the method drop\-down list\.

1. Choose the checkmark icon to save your choice\.

1. In the **Set up** pane:

   1. For **Integration type**, choose **AWS Service**\.

   1. For **AWS Region**, choose the region \(e\.g\., `us-west-2`\) where you created the Lambda function\.

   1. For **AWS Service**, choose **Lambda**\.

   1. Leave **AWS Subdomain** blank, because our Lambda function is not hosted on any AWS subdomain\.

   1. For **HTTP method**, choose **POST**\. This example shows that the HTTP method in a frontend method request can be different from the integration request in the backend\.

   1. For **Action**, choose `Use path override` for **Action Type**\. This option allows you to specify the ARN of the [Invoke](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) action to execute your `Calc` function\.

   1. For **Path override**, type `/2015-03-31/functions/arn:aws:lambda:region:account-id:function:Calc/invocations` , where *region* is the region where you created your Lambda function and *account\-id* is the account number for the AWS account\.

   1. For **Execution role**, enter the role ARN for the **lambda\_invoke\_function\_assume\_apigw\_role** IAM role you created [earlier](#api-as-lambda-proxy-setup-iam-role-policies)\.

   1. Leave **Content Handling** set to **Passthrough**, because this method will not deal with any binary data\.

   1. Leave **Use Default Timeout** checked\.

   1. Choose **Save**\.

1. Choose **Models** under your **LambdaCalc** API in the API Gateway console's primary navigation pane to create data models for the method's input and output:

   1. Choose **Create** in the **Models** pane\. Type `Input` in **Model name**, type `application/json` in **Content type**, and copy the following schema definition into the **Model schema** box: 

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

   1. Choose **Create model**\.

   1. Choose **Create** in the **Models** pane\. Type `Output` in **Model name**, type `application/json` in **Content type**, and copy the following schema definition into the **Model schema** box: 

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

   1. Locate the API ID for your API at the top of the console screen and make a note of it\. It will appear in parentheses after the API name\.

   1. In the **Models** pane, choose **Create**\.

   1. Type `Result` in **Model name**\.

   1. Type `application/json` in **Content type**\.

   1. Copy the following schema definition, where *restapi\-id* is the REST API ID you noted earlier, into the **Model schema** box: 

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

   1. Choose **Create model**\.

1. In the main navigation pane, under your `LambdaCalc` API, choose **Resources**\.

1. In the **Resources** pane, choose the `POST` method for your API\.

1. Choose **Method Request**\.

1. In the **Method Request** configuration settings, do the following to enable request validation on the incoming request body:

   1. Choose the pencil icon next to **Request Validator** to choose `Validate body`\. Choose the checkmark icon to save your choice\.

   1. Expand the **Request Body** section, choose **Add model**

   1. Type `application/json` in the **Content\-Type** input field and choose `Input` from the drop\-down list in the **Model name** column\. Choose the checkmark icon to save your choice\.

1. To test your `POST` method, do the following:

   1. Choose **Method Execution**\.

   1. Choose **Test**\.

1. Copy the following JSON payload into the **Request Body**: 

   ```
   {
       "a": 1,
       "b": 2,
       "op": "+"
   }
   ```

1. Choose **Test**\.

   You should see the following output in **Response Body**:

   ```
   {
     "a": 1,
     "b": 2,
     "op": "+",
     "c": 3
   }
   ```

## Integration 3: Create a `GET` method with path parameters to call the Lambda function<a name="api-as-lambda-proxy-expose-get-method-with-path-parameters-to-call-lambda-function"></a>

Now you'll create a `GET` method on a resource specified by a sequence of path parameters to call the backend Lambda function\. The path parameter values specify the input data to the Lambda function\. You'll use a mapping template to map the incoming path parameter values to the required integration request payload\.

This time you'll use the built\-in Lambda integration support in the API Gateway console to set up the method integration\.

The resulting API resource structure will look like this:

![\[Create an API in API Gateway as a Lambda proxy\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_create_api_resources.png)

**To set up a `GET` method with URL path parameters**

1. Go to the API Gateway console\.

1. Under **APIs**, choose the **LambdaCalc** API you created previously\.

1. In the API's Resources navigation pane, choose **/calc**\.

1. From the **Actions** drop\-down menu, choose **Create Resource**\.

1. For **Resource Name**, type **\{operand1\}**\.

1. For **Resource Path**, type **\{operand1\}**\.

1. Choose **Create Resource**\.

1. Choose the **/calc/\{operand1\}** resource you just created\.

1. From the **Actions** drop\-down menu, choose **Create Resource**\.

1. For **Resource Name**, type **\{operand2\}**\.

1. For **Resource Path**, type **\{operand2\}**\.

1. Choose **Create Resource**\.

1. Choose the **/calc/\{operand1\}/\{operand2\}** resource you just created\.

1. From the **Actions** drop\-down menu, choose **Create Resource**\.

1. For **Resource Path**, type **\{operator\}**\.

1. For **Resource Name**, type **\{operator\}**\.

1. Choose **Create Resource**\.

1. Choose the **/calc/\{operand1\}/\{operand2\}/\{operator\}** resource you just created\.

1. From the **Actions** drop\-down menu, choose **Create Method**\.

1. From the method drop\-down menu, choose `GET`\.

1. In the **Setup** pane, choose `Lambda Function` for **Integration type**, to use the streamlined setup process enabled by the console\.

1. Choose a region \(e\.g\., `us-west-2`\) for **Lambda Region**\. This is the region where the Lambda function is hosted\.

1. Choose your existing Lambda function \(`Calc`\) for **Lambda Function**\. 

1. Choose **Save** and then choose **OK** to consent to **Add Permissions to Lambda Function**\.

1. Choose **Integration Request**\.

1. Set up the mapping template as follows:

   1. Expand the **Mapping Templates** section\.

   1. Leave set to **When no template matches the requested Content\-Type header**\.

   1. Choose **Add mapping template**\.

   1. Type `application/json` for **Content\-Type** and then choose the check mark icon to open the template editor\.

   1. Choose **Yes, secure this integration** to proceed\.

   1. Copy the following mapping script into the template editor:

      ```
      {
         "a": "$input.params('operand1')",
         "b": "$input.params('operand2')",
         "op": #if($input.params('operator')=='%2F')"/"#{else}"$input.params('operator')"#end
         
      }
      ```

      This template maps the three URL path parameters, declared when the **/calc/\{operand1\}/\{operand2\}/\{operator\}** resource was created, into designated property values in the JSON object\. Because URL paths must be URL\-encoded, the division operator must be specified as `%2F` instead of `/`\. This template translates the `%2F` into `'/'` before passing it to the Lambda function\. 

   1. Choose **Save**\.

   When the method is set up correctly, the settings should look more or less like this:   
![\[Set up the GET method with path parameters to invoke the Lambda function\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_with_paths_settings.png)

1. To test your `GET` function, do the following:

   1. Choose **Method Execution**\.

   1. Choose **Test**\.

   1. Type **1**, **1** and **\+** in the **\{operand1\}**, **\{operand2\}** and **\{operator\}** fields, respectively\.

   1. Choose **Test**\.

   1. The result should look like this:  
![\[Map a method request URL path parameters to the integration request payload to call the Lambda function\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_lambda_calc_get_method_test_result.png)

   This test result shows the original output from the backend Lambda function, as passed through the integration response without mapping, because there is no mapping template\. Next, you'll model the data structure of the method response payload after the `Result` schema\.

1. By default, the method response body is assigned an empty model\. This will cause the integration response body to be passed through without mapping\. However, when you generate an SDK for one of the strongly\-type languages, such as Java or Objective\-C, your SDK users will receive an empty object as the result\. To ensure that both the REST client and SDK clients receive the desired result, you must model the response data using a predefined schema\. Here you'll define a model for the method response body and to construct a mapping template to translate the integration response body into the method response body\.

   1. Choose **/calc/\{operand1\}/\{operand2\}/\{operator\}**\.

   1. Choose **GET**\.

   1. Choose **Method Execution**\.

   1. Choose **Method Response**\.

   1. Expand the **200** response, 

   1. Under **Response Body for 200**, choose the pencil icon next to the model for the `application/json` content type\.

   1. From the **Models** drop\-down list, choose `Result`\.

   1. Choose the checkmark icon to save your choice\.

   Setting the model for the method response body ensures that the response data will be cast into the `Result` object of a given SDK\. To make sure that the integration response data is mapped accordingly, you'll need a mapping template\.

1. To create the mapping template, do the following:

   1. Choose **Method Execution**\.

   1.  Choose **Integration Response** and expand the 200 method response entry\.

   1. Expand the **Mapping Templates** section\.

   1. Choose `application/json` from the **Content\-Type** list\.

   1. Choose `Result` from the **Generate template** drop\-down list to bring up the `Result` template blueprint\.

   1. Change the template blueprint to the following:

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

1. To test the mapping template, do the following:

   1. Choose **Method Execution**\.

   1. Choose **Test**\.

   1. Type `1` `2` and `+` in the **operand1**, **operand2** and **operator** input fields, respectively\.

      The integration response from the Lambda function is now mapped to a `Result` object\.

   1. Choose **Test**, and you'll see the following under **Response Body** in the console:

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

1. At this point the API can be called only via **Test Invoke** in the API Gateway console\. To make it available to clients, you'll need to deploy it as follows:

   1. Choose **Deploy API** from the **Actions** dropdown menu\.

   1. Choose **\[New Stage\]** from the **Deployment Stage** dropdown menu\.

   1. For **Stage Name**, enter **test**\.

   1. Choose **Deploy**\.

   1. Note the **Invoke URL** at the top of the console window\. You can use this with tools such as [Postman](http://www.postman.com) and [cURL](https://curl.haxx.se/) to test your API\.

**Note**  
Always be sure to redeploy your API whenever you add, modify, or delete a resource or method, update a data mapping, or update stage settings\. Otherwise, new features or updates will not be available to clients of your API\.