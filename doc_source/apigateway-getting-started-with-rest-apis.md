# Create a REST API with Lambda integrations in Amazon API Gateway<a name="apigateway-getting-started-with-rest-apis"></a>

You can use this walkthrough to create and deploy a REST API with a Lambda proxy integration and a Lambda non\-proxy integration in Amazon API Gateway\.

In a Lambda integration, the HTTP method request from the client is mapped to a backend [Lambda function invocation](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html)\.

In a *Lambda proxy integration*, the entire client request is sent to the backend Lambda function as is, except that the order of the request parameters isn't preserved\. API Gateway maps the entire client request to the input `event` parameter of the backend Lambda function\. The Lambda function's output, including status code, headers, and body, is returned to the client as is\. For many use cases, this is the preferred integration type\. For more information, see [Set up Lambda proxy integrations in API Gateway](set-up-lambda-proxy-integrations.md)\.

In a *Lambda non\-proxy integration* \(also called a "custom integration"\), you configure the way the parameters, headers, and body of the client's request are translated into the format that your backend Lambda function requires\. And you configure the way the Lambda function output is translated back to the format that's required by the client\. For more information, see [Set up Lambda custom integrations in API Gateway](set-up-lambda-custom-integrations.md)\.

Depending on your use case, you may choose to use Lambda proxy integration, Lambda non\-proxy integration, or both in your API Gateway API\.

**Topics**
+ [Step 1: Create a Lambda function in the Lambda console](#apigateway-getting-started-rest-step-1)
+ [Step 2: Create a REST API in the API Gateway console](#apigateway-getting-started-rest-step-2)
+ [Step 3: Deploy your REST API in the API Gateway console](#apigateway-getting-started-rest-step-3)
+ [Step 4: Create a second Lambda function in the Lambda console](#apigateway-getting-started-rest-step-4)
+ [Step 5: Add a resource, method, and parameter to the REST API in the API Gateway console](#apigateway-getting-started-rest-step-5)
+ [Next steps](#apigateway-getting-started-rest-next-steps)

## Step 1: Create a Lambda function in the Lambda console<a name="apigateway-getting-started-rest-step-1"></a>

In this step, you use the AWS Lambda console to create a simple Lambda function\. You'll use this function in the following steps\.

1. If you haven't already done so, complete the steps in [Prerequisites: Get ready to build an API in API Gateway](setting-up.md)\.

1. Complete the steps in [To create a Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/getting-started-create-function.html) in the AWS Lambda Developer Guide\.

## Step 2: Create a REST API in the API Gateway console<a name="apigateway-getting-started-rest-step-2"></a>

In this step, you create a simple REST API in the API Gateway console and attach your Lambda function to it as a backend\.

1. From the **Services** menu, choose **API Gateway** to go to the API Gateway console\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

1. Under **Create new API**, choose **New API**\.

1. Under **Settings**:
   + For **API name**, enter `my-api`\.
   + If desired, enter a description in the **Description** field; otherwise, leave it empty\.
   + Leave **Endpoint Type** set to **Regional**\.

1. Choose **Create API**\.

1. Under **Resources**, you'll see nothing but **/**\. This is the root\-level resource, which corresponds to the base path URL for your API \(`https://b123abcde4.execute-api.us-west-2.amazonaws.com/{stage-name})`\.

   From the **Actions** dropdown menu, choose **Create Method**\.

1. Under the resource name \(**/**\), you'll see a dropdown menu\. Choose **GET** and then choose the checkmark icon to save your choice\.

1. In the **/ – GET – Setup** pane, for **Integration type**, choose **Lambda Function**\.

1. Choose **Use Lambda proxy integration**\.

1. For **Lambda Region**, choose the Region where you created your Lambda function\.

1. In the **Lambda Function** field, type any character and then choose **my\-function** \(or whatever name you gave the function that you created in the previous step\) from the dropdown menu\. \(If the dropdown menu doesn't appear, delete the character you just typed to make the dropdown menu appear\.\) Leave Use **Default Timeout** checked\. Choose **Save** to save your choice\.

1. When the **Add Permission to Lambda Function** popup appears \(saying **"You are about to give API Gateway permission to invoke your Lambda function…"**\), choose **OK** to grant API Gateway that permission\.

Now you'll see a **/ – GET – Method Execution** pane:

![\[API Gateway method execution pane\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-get-method-execution-boxes.png)

The **Method Execution** pane contains these items, in clockwise order:
+ **Client**: This box represents the client \(browser or app\) that calls your API's `GET` method\. If you choose the **Test** link and then choose **Test**, this simulates a `GET` request from a client\.
+ **Method Request**: This box represents the client's `GET` request as it's received by API Gateway\. If you choose **Method Request**, you'll see settings for things like authorization and modifying the method request before it's passed through to the backend as an integration request\. For this step, leave everything set to the default values\.
+ **Integration Request**: This box represents the `GET` request as it's passed to the backend\. Here the settings depend on the **Integration Type** that's selected\. The **URL Path Parameters**, **URL Query String Parameters**, **HTTP Headers**, and **Mapping Templates** settings are for modifying the method request as needed by the specific backend\. Leave the **Integration Type** set to **Lambda function**, and leave the other settings set to the default values\.
+ **Lambda `my-function`**: This box represents the backend Lambda function you created in Step 1\. If you choose `my-function`, that opens the `my-function` Lambda function in the Lambda console\.
+ **Integration Response**: This box represents the response from the backend, before it's passed to the client as a method response\. For a Lambda proxy integration, this entire box is grayed out, because a proxy integration returns the [Lambda function's output](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html#api-gateway-simple-proxy-for-lambda-output-format) as is\. For a Lambda non\-proxy integration, you can configure the integration response to transform the Lambda function's output into a form that's appropriate for the client application\. You'll learn how to do that later in this walkthrough\.

  For this Getting Started procedure, leave everything set to the default values\.
+ **Method Response**: This box represents the method response that's returned to the client as an HTTP status code, an optional response header, and an optional response body\. By default, the response body that's returned by your Lambda function is passed through as is as a JSON document, so the response body default setting is `application/json` with an empty model \(indicating that the body is passed through as is\)\. Here, too, leave everything set to the default values\.

## Step 3: Deploy your REST API in the API Gateway console<a name="apigateway-getting-started-rest-step-3"></a>

Once you complete Step 2, you've created an API, but you can't actually use it yet\. This is because it needs to be deployed\.

1. From the **Actions** dropdown menu, choose **Deploy API**\.

1. From the **Deployment stage** dropdown menu, choose **\[New Stage\]**\.

1. For **Stage name**, enter `prod`\.

1. Choose **Deploy**\.

1. In the `prod` **Stage Editor**, note the **Invoke URL** at the top\. It should be in this format: \(`https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod`\)\. If you choose the **Invoke URL**, it will open a new browser tab with that URL\. If you refresh the new browser tab, you'll see the message body \(`"Hello from Lambda!"`\) returned\.

For actual API testing, you would use a tool such as [cURL](https://curl.haxx.se/) or [POSTMAN](https://www.postman.com/) to test your API\. For example, if you have `cURL` installed on your computer, you can test your API as follows:

1. Open a terminal window\.

1. Copy the following cURL command and paste it into the terminal window, replacing `b123abcde4` with your API's API ID and `us-west-2` with the Region where your API is deployed\.

------
#### [ Linux or Macintosh ]

   ```
   curl -X GET 'https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod'
   ```

------
#### [ Windows ]

   ```
   curl -X GET "https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod"
   ```

------

   This command returns the following output: `"Hello from Lambda!"`\.

## Step 4: Create a second Lambda function in the Lambda console<a name="apigateway-getting-started-rest-step-4"></a>

In this step, you'll create a second backend Lambda function\. This one takes an input parameter\. In Step 5 you'll create a child resource in your API that has its own `GET` method, which you'll configure to pass a parameter value to this new function\.

1. Choose **Lambda** from the **Services** menu to go to the Lambda console\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. For **Function name**, enter `my-function2`\.

1. Under **Runtime**, choose **Node\.js 12\.x** or **Python 3\.7**\.

1. Under **Permissions**, expand **Choose or create an execution role**\. From the **Execution role** dropdown list, choose **Use an existing role**\. From **Existing role**, choose the role from your previous Lambda function\. The role's name will be in the form `service-role/my-function-a1b23c4d`\. Or choose **Create a new role with basic Lambda permissions**\.

1. Choose **Create function**\.

1. In the **Function code** pane, you'll see the default "Hello from Lambda\!" function\. Replace the entire function with the following:

------
#### [ Node\.js 12\.x ]

   ```
   exports.handler = async (event) => {
       let myParam = event.myParam;
       const response = {
           statusCode: 200,
           body: JSON.stringify(myParam)
       };
       return response;
   };
   ```

------
#### [ Python 3\.7 ]

   ```
   import json
   def lambda_handler(event, context):
       myParam = event['myParam']
       return {
           'statusCode': 200,
           'body': json.dumps(myParam)
       }
   ```

------

1. Choose **Save**\.

## Step 5: Add a resource, method, and parameter to the REST API in the API Gateway console<a name="apigateway-getting-started-rest-step-5"></a>

Resources and methods are the nouns and verbs of your REST API\. In this step, you'll create a child resource for the API and add a `GET` method to the resource\. You'll add a query string parameter to the new method to match the input parameter for the Lambda function you created in Step 4\. You'll integrate the new function with the method to illustrate how to get user input \(a simple "Hello from API Gateway\!" text string\) and map it to the Lambda function's input \(also a simple string\)\.

1. Choose **API Gateway** from the **Services** menu to go to the API Gateway console\.

1. From the **APIs** list, choose **my\-api**\.

1. Under **Resources**, choose **/**\.

1. From the **Actions** dropdown menu, choose **Create Resource**\.

1. For **Resource Name**, enter `my-resource`\. Notice that the **Resource Path** field is populated automatically with the resource name\.

1. Choose **Create Resource**\.

1. From the **Actions** dropdown menu, choose **Create Method**\.

1. Under the resource name \(**my\-resource**\), you'll see a dropdown menu\. Choose **GET** and then choose the checkmark icon to save your choice\.

1. In the **/my\-resource – GET – Setup** pane, for **Integration type**, choose **Lambda Function**\.

1. For **Lambda Region**, choose the Region where you created your Lambda function\.

1. In the **Lambda Function** box, type any character and then choose **my\-function2** from the dropdown menu\. \(If the dropdown menu doesn't appear, delete the character you just typed to make the dropdown menu appear\.\) Choose **Save** to save your choice\.

1. When the **Add Permission to Lambda Function** popup appears \(saying **"You are about to give API Gateway permission to invoke your Lambda function…"**\), choose OK to grant API Gateway that permission\.

1. You'll now see a **/my\-resource – GET – Method Execution** pane\. Notice that this time the **Integration Response** box is not grayed out\.

   Choose **Integration Request**\.  
![\[API Gateway method execution pane\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-my-resource-get-method-execution-boxes.png)

1. Expand **Mapping Templates**\.

1. Set **Request body passthrough** to **When there are no templates defined**\.

1. Choose **Add mapping template**\.

1. For **Content\-Type**, enter `application/json` and choose the checkmark icon to save your choice\.

1. If a **Change passthrough behavior** popup appears, choose **Yes, secure this integration**\. This ensures that your integration only allows requests that match the **Content\-Type** you just defined\.

1. In the template window, enter the following:

   ```
   {
       "myParam": "$input.params('myParam')"
   }
   ```

   This is the JSON document that will be passed to your Lambda function in the body of the input event object\. The right\-hand side tells API Gateway what to look for in your input query string\. The left\-hand side stores it into the parameter that you're about to add to your Lambda function\.

1. Choose **Save**\.

1. From the **Actions** dropdown menu, choose **Deploy API**\.

1. From the **Deployment stage** dropdown menu, choose **prod**\.

1. Choose **Deploy**\.

Test your API as follows:

1. Open a terminal window\.

1. Copy the following cURL command and paste it into the terminal window, replacing `b123abcde4` with your API's API ID and `us-west-2` with the Region where your API is deployed\.

------
#### [ Linux or Macintosh ]

   ```
   curl -X GET 'https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod'
   ```

------
#### [ Windows ]

   ```
   curl -X GET "https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod"
   ```

------

   This will invoke your API, and you'll see "Hello from Lambda\!" returned\. This is because your new Lambda function, `my-function2`, isn't attached to the root resource \(`/`\)\. It's attached to the new resource you created, `my-resource`\. So the Lambda function you just invoked is the "Hello from Lambda\!" function you created earlier, `my-function`\.

1. In the cURL command line, after `prod`, type `/my-resource?myParam=Hello%20from%20API%20Gateway!`\. The complete command should look like this:

------
#### [ Linux or Macintosh ]

   ```
   curl -X GET 'https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod/my-resource?myParam=Hello%20from%20API%20Gateway!'
   ```

------
#### [ Windows ]

   ```
   curl -X GET "https://b123abcde4.execute-api.us-west-2.amazonaws.com/prod/my-resource?myParam=Hello%20from%20API%20Gateway!"
   ```

------

   You should now see the correct response returned: `{"statusCode": 200, "body": "\"Hello from API Gateway!\""}`\.

## Next steps<a name="apigateway-getting-started-rest-next-steps"></a>

Explore any or all of the following topics to continue getting familiar with Amazon API Gateway\.


| To learn more about | Go to | 
| --- | --- | 
| Passing input to a backend Lambda function | [Input format of a Lambda function for proxy integration](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-input-format) | 
| Returning output from a backend Lambda function | [Output format of a Lambda function for proxy integration](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-output-format) | 
| Setting up a custom domain name for your API | [Setting up custom domain names for REST APIs](how-to-custom-domains.md) | 
| Adding a Lambda authorizer function to your API | [Use API Gateway Lambda authorizers](apigateway-use-lambda-authorizer.md) | 
| Adding a Amazon Cognito user pool authorizer to your API | [Control access to a REST API using Amazon Cognito User Pools as authorizer](apigateway-integrate-with-cognito.md) | 
| Enabling CORS for your API | [Enabling CORS for a REST API resource](how-to-cors.md) | 

To get help with Amazon API Gateway from the community, see the [API Gateway Discussion Forum](http://forums.aws.amazon.com/forum.jspa?forumID=199)\. \(When you enter this forum, AWS might require you to sign in\.\)

To get help with API Gateway directly from AWS, see the support options on the [AWS Support page](http://aws.amazon.com/premiumsupport/)\.

See also our [frequently asked questions \(FAQs\)](http://aws.amazon.com/api-gateway/faqs/), or [contact us directly](http://aws.amazon.com/contact-us/)\.