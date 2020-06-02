# Using Amazon API Gateway stage variables<a name="amazon-api-gateway-using-stage-variables"></a>

You can use API Gateway stage variables to access the HTTP and Lambda backends for different API deployment stages\. You can also use stage variables to pass stage\-specific configuration metadata into an HTTP backend as a query parameter and into a Lambda function as a payload that is generated in an input mapping template\. 

## Prerequisites<a name="using-stage-variables-prerequisites"></a>

You must create two stages with a `url` variable set to two different HTTP endpoints: a `function` stage variable assigned to two different Lambda functions, and a `version` stage variable containing stage\-specific metadata\. Follow the instructions in [Setting stage variables using the Amazon API Gateway console](how-to-set-stage-variables-aws-console.md)\.

## Access an HTTP endpoint through an API with a stage variable<a name="call-api-http-backend-via-stage-variable"></a>

1. In the **Stages** navigation pane, choose **beta**\. In **beta Stage Editor**, choose the **Invoke URL** link\. This starts the **beta** stage `GET` request on the root resource of the API\. 
**Note**  
The **Invoke URL** link points to the root resource of the API in its **beta** stage\. Navigating to the URL by choosing the link calls the **beta** stage `GET` method on the root resource\. If methods are defined on child resources and not on the root resource itself, choosing the **Invoke URL** link returns a `{"message":"Missing Authentication Token"}` error response\. In this case, you must append the name of a specific child resource to the **Invoke URL** link\. 

1. The response you get from the **beta** stage `GET` request is shown next\. You can also verify the result by using a browser to navigate to **http://httpbin\.org/get**\. This value was assigned to the `url` variable in the **beta** stage\. The two responses are identical\. 

1. In the **Stages** navigation pane, choose the **prod** stage\. From **prod Stage Editor** , choose the **Invoke URL** link\. This starts the **prod** stage `GET` request on the root resource of the API\. 

1. The response you get from the **prod** stage `GET` request is shown next\. You can verify the result by using a browser to navigate to **http://petstore\-demo\-endpoint\.execute\-api\.com/petstore/pets**\. This value was assigned to the `url` variable in the **prod** stage\. The two responses are identical\. 

## Pass stage\-specific metadata to an HTTP backend through a stage variable in a query parameter expression<a name="call-api-http-backend-with-query-parameter-via-stage-variables"></a>

This procedure describes how to use a stage variable value in a query parameter expression to pass stage\-specific metadata into an HTTP backend\. We will use the `version` stage variable declared in [Setting stage variables using the Amazon API Gateway console](how-to-set-stage-variables-aws-console.md)\. 

1. In the **Resource** navigation pane, choose the **GET** method\. To add a query string parameter to the method's URL, in **Method Execution**, choose **Method Request**\. Enter **version** for the parameter name\. 

1.  In **Method Execution** choose **Integration Request**\. Edit the **Endpoint URL** value to append **`?version=${stageVariables.version}`** to the previously defined URL value, which, in this case, is also expressed with the `url` stage variable\. Choose **Deploy API** to deploy these changes\. 

1. In the **Stages** navigation pane, choose the **beta** stage\. From **beta Stage Editor**, verify that the current stage is in the most recent deployment, and then choose the **Invoke URL** link\. 
**Note**  
 We use the beta stage here because the HTTP endpoint \(as specified by the `url` variable "http://httpbin\.org/get"\) accepts query parameter expressions and returns them as the `args` object in its response\. 

1. The response is shown next\. Notice that `v-beta`, assigned to the `version` stage variable, is passed in the backend as the `version` argument\.   
![\[Response from the API's GET method using a proxy for an HTTP endpoint with the url stage variable in the beta stage\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/stageVariables-invoke-beta-stage-with-url-and-version-variables-response.png)

## Call a Lambda function through an API with a stage variable<a name="call-api-lambda-backend-with-stage-variable"></a>

This procedure describes how to use a stage variable to call a Lambda function as a backend of your API\. We will use the `function` stage variable declared earlier\. For more information, see [Setting stage variables using the Amazon API Gateway console](how-to-set-stage-variables-aws-console.md)\.

1. In the **Resources** pane, create a **/lambdasv1** child resource under the root directory, and then create a `GET` method on the child resource\. Set the **Integration type** to **Lambda Function**, and in **Lambda Function**, enter `${stageVariables.function}`\. Choose **Save**\.   
![\[Create a GET method integrated with a Lambda function as specified by the function stage variable.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/stageVariables-create-lambda-get-method.png)
**Tip**  
When prompted with **Add Permission to Lambda Function**, make a note of the AWS CLI command before choosing **OK**\. You must run the command on each Lambda function that is or will be assigned to the `function` stage variable for each of the newly created API methods\. For example, if the `$stageVariables.function` value is `HelloWorld` and you have not added permission to this function yet, you must run the following AWS CLI command:   

   ```
   aws lambda add-permission --function-name arn:aws:lambda:us-east-1:account-id:function:HelloWorld --source-arn arn:aws:execute-api:us-east-1:account-id:api-id/*/GET/lambdasv1 --principal apigateway.amazonaws.com --statement-id statement-id-guid --action lambda:InvokeFunction
   ```
 Failing to do so results in a `500 Internal Server Error` response when invoking the method\. Make sure to replace `${stageVariables.function}` with the Lambda function name that is assigned to the stage variable\.   

![\[Run the AWS CLI command to add permission to the Lambda function to be invoked by method you just created.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/stageVariables-add-permission-to-lambda-function.png)

1.  Deploy the API to available stages\. 

1. In the **Stages** navigation pane, choose the **beta** stage\. Verify that your most recent deployment is in **beta Stage Editor**\. Copy the **Invoke URL** link, paste it into the address bar of your browser, and append **`/lambdasv1`** to that URL\. This calls the underlying Lambda function through the `GET` method on the **LambdaSv1** child resource of the API\. 
**Note**  
Your `HelloWorld` Lambda function implements the following code\.   

   ```
   exports.handler = function(event, context, callback) {
       if (event.version)
           callback(null, 'Hello, World! (' + event.version + ')' );
       else
           callback(null, "Hello, world! (v-unknown)");  
   };
   ```
This implementation results in the following response\.  

   ```
   "Hello, world! (v-unknown)"
   ```

## Pass stage\-specific metadata to a Lambda function through a stage variable<a name="pass-version-info-to-lambda-backend-with-stage-variable"></a>

This procedure describes how to use a stage variable to pass stage\-specific configuration metadata into a Lambda function\. We will use a `POST` method and an input mapping template to generate payload using the `version` stage variable declared earlier\.

1. In the **Resources** pane, choose the **/lambdasv1** child resource\. Create a `POST ` method on the child resource, set the **Integration type** to **Lambda Function**, and enter **`${stageVariables.function}`** in **Lambda Function**\. Choose **Save**\. 
**Tip**  
``This step is similar to the step we used to create the `GET` method\. For more information, see [Call a Lambda function through an API with a stage variable](#call-api-lambda-backend-with-stage-variable)\. 

1.  From the **/Method Execution** pane, choose **Integration Request**\. In the **Integration Request** pane, expand **Mapping Templates**, and then choose **Add mapping template** to add a template for the `application/json` content\-type, as shown in the following\.   
![\[Generate a POST method payload, containing stage-specific configuration metadata represented by the version stage variable value, as an input to the backend Lambda function.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/stageVariables-generate-post-payload-with-mapping-template-as-input-to-function.png)
**Note**  
 In a mapping template, a stage variable must be referenced within quotes \(as in `"$stageVariables.version"` or `"${stageVariables.version}"`\)\. In other places, it must be referenced without quotes \(as in `${stageVariables.function}`\)\. 

1.  Deploy the API to available stages\. 

1. In the **Stages** navigation pane, choose **beta**\. In **beta Stage Editor**, verify that the current stage has the most recent deployment\. Copy the **Invoke URL** link, paste it into the URL input field of a REST API client, append **`/lambdasv1`** to that URL, and then submit a `POST` request to the underlying Lambda function\. 
**Note**  
You will get the following response\.  

   ```
   "Hello, world! (v-beta)"
   ```