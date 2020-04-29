# Set up a method using the API Gateway console<a name="how-to-set-up-method-using-console"></a>

Before setting up an API method, verify the following:
+ You must have the method available in API Gateway\. Follow the instructions in [Tutorial: Build a REST API with HTTP non\-proxy integration](api-gateway-create-api-step-by-step.md)\.
+ If you want the method to communicate with a Lambda function, you must have already created the Lambda invocation role and Lambda execution role in IAM\. You must also have created the Lambda function with which your method will communicate in AWS Lambda\. To create the roles and function, use the instructions in [Create a Lambda function for Lambda non\-proxy integration](getting-started-lambda-non-proxy-integration.md#getting-started-new-lambda) of the [Build an API Gateway REST API with Lambda integration](getting-started-with-lambda-integration.md)\. 
+ If you want the method to communicate with an HTTP or HTTP proxy integration, you must have already created, and have access to, the HTTP endpoint URL with which your method will communicate\.
+  Verify that your certificates for HTTP and HTTP proxy endpoints are supported by API Gateway\. For details see [API Gateway\-supported certificate authorities for HTTP and HTTP proxy integrations](api-gateway-supported-certificate-authorities-for-http-endpoints.md)\. 

**Topics**
+ [Set up an API Gateway method request in the API Gateway console](#how-to-method-settings-callers-console)
+ [Set up an API Gateway method response using the API Gateway console](#how-to-method-response-settings-console)

## Set up an API Gateway method request in the API Gateway console<a name="how-to-method-settings-callers-console"></a>

 To use the API Gateway console to specify an API's method request/response, and to configure how the method will authorize requests, follow these instructions\.

**Note**  
 These instructions assume you have already completed the steps in [Set up an API integration request using the API Gateway console](how-to-method-settings-console.md)\. They are best used to supplement the discussions given in [Build an API Gateway REST API with Lambda integration](getting-started-with-lambda-integration.md)\. 

1. With the method selected in the **Resources** pane, choose **Method Request** from the **Method Execution** pane\.

1.  Under **Settings**, choose the pencil icon to open the **Authorization** drop\-down menu and choose one of the available authorizers\. 

   1. To enable open access to the method for any user, choose `NONE`\. This step can be skipped if the default setting has not been changed\.

   1. To use IAM permissions to control the client access to the method, choose `AWS_IAM`\. With this choice, only users of the IAM roles with the correct IAM policy attached are allowed to call this method\. 

      To create the IAM role, specify an access policy with a format like the following: 

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "execute-api:Invoke"
            ],
            "Resource": [
              "resource-statement"
            ]
          }
        ]
      }
      ```

      In this access policy, *resource\-statement* is the value of the **ARN** field in the **Authorization Settings** section\. For more information about setting the IAM permissions, see [Control access to an API with IAM permissions](permissions.md)\. 

      To create the IAM role, you can adapt the instructions in "To create the Lambda invocation role and its policy" and "To create the Lambda execution role and its policy" in the [Create Lambda Functions](getting-started-lambda-non-proxy-integration.md#getting-started-new-lambda) section of the [Build an API Gateway REST API with Lambda integration](getting-started-with-lambda-integration.md)\. 

      To save your choice, choose **Update**\. Otherwise, choose **Cancel**\.

   1.  To use a Lambda authorizer, choose one under **Token authorizer**\. You must have created a Lambda authorizer to have this choice displayed in the drop\-down menu\. For information on how to create a Lambda authorizer, see [Use API Gateway Lambda authorizers](apigateway-use-lambda-authorizer.md)\. 

   1.  To use an Amazon Cognito user pool, choose an available user pool under **Cognito user pool authorizers**\. You must have created a user pool in Amazon Cognito and an Amazon Cognito user pool authorizer in API Gateway to have this choice displayed in the drop\-down menu\. For information on how to create an Amazon Cognito user pool authorizer, see [Control access to a REST API using Amazon Cognito User Pools as authorizer](apigateway-integrate-with-cognito.md)\. 

1.  To enable or disable request validation, choose the pencil icon from the **Request Validator** drop\-down menu and choose one of the listed options\. For more information about each option, see [Enable request validation in API Gateway](api-gateway-method-request-validation.md)\. 

1. To require an API key, choose the pencil icon to open the **API Key Required** drop\-down menu and choose either `true` or `false` according to your API requirements\. When enabled, API keys are used in [usage plans](api-gateway-api-usage-plans.md) to throttle client traffic\. 

1. To add a query string parameter to the method, do the following:

   1. Choose the arrow next to **URL Query String Parameters**, and then choose **Add query string**\.

   1. For **Name**, type the name of the query string parameter\.

   1. Choose the check\-mark icon to save the new query string parameter name\.

   1. If the newly created query string parameter is to be used for request validation, choose the **Required** option\. For more information about the request validation, see [Enable request validation in API Gateway](api-gateway-method-request-validation.md)\.

   1. If the newly created query string parameter is to be used as part of a caching key, check the **Caching** option\. This is applicable only when caching is enabled\. For more information about caching, see [Use method or integration parameters as cache keys to index cached responses](api-gateway-caching.md#enable-api-gateway-cache-keys)\.
**Tip**  
To remove the query string parameter, choose the **x** icon associated with it and then choose **Remove this parameter and any dependent parameters** to confirm the removal\.   
To change the name of the query string parameter, remove it and then create a new one\.

1. To add a header parameter to the method, do the following:

   1. Choose the arrow next to **HTTP Request Headers**, and then choose **Add header**\.

   1. For **Name**, type the name of the header parameter and then choose the check\-mark icon to save the settings\.

   1. If the newly created header parameter is to be used for request validation, choose the **Required** option\. For more information about request validation, see [Enable request validation in API Gateway](api-gateway-method-request-validation.md)\.

   1. If the newly created header parameter is to be used as part of a caching key, choose the **Caching** option\. This is applicable only when caching is enabled\. For more information about caching, see [Use method or integration parameters as cache keys to index cached responses](api-gateway-caching.md#enable-api-gateway-cache-keys)\.
**Tip**  
To remove the header parameter, choose the **x** icon associated with it and then choose **Remove this parameter and any dependent parameters** to confirm the removal\.   
To change the name of the header parameter, remove it and then create a new one\.

1.  To declare the payload format of a method request with the `POST`, `PUT`, or `PATCH` HTTP verb, expand **Request Body**, and do the following: 

   1. Choose **Add model**\.

   1. Type a MIME\-type \(for example, `application/json`\) for **Content type**\.

   1. Open the **Model name** drop\-down menu to choose an available model for the payload and choose the check\-mark icon to save the settings\.

      The currently available models for the API include the default `Empty` and `Error` models as well as any models you have created and added to the [Models](https://docs.aws.amazon.com/apigateway/api-reference/resource/models/) collection of the API\. For more information about creating a model, see [Create a model](how-to-create-model.md)\. 
**Note**  
 The model is useful to inform the client of the expected data format of a payload\. It is helpful to generate a skeletal mapping template\. It is important to generate a strongly typed SDK of the API in such languages as Java, C\#, Objective\-C, and Swift\. It is only required if request validation is enabled against the payload\. 

1. To assign an operation name in a Java SDK of this API, generated by API Gateway, expand **SDK Settings** and type a name in **Operation name**\. For example, for the method request of `GET /pets/{petId}`, the corresponding Java SDK operation name is, by default ,`GetPetsPetId`\. This name is constructed from the method's HTTP verb \(`GET`\) and the resource path variable names \(`Pets` and `PetId`\)\. If you set the operation name as `getPetById`, the SDK operation name becomes `GetPetById`\.

## Set up an API Gateway method response using the API Gateway console<a name="how-to-method-response-settings-console"></a>

 An API method can have one or more responses\. Each response is indexed by its HTTP status code\. By default, the API Gateway console adds `200` response to the method responses\. You can modify it, for example, to have the method return `201` instead\. You can add other responses, for example, `409` for access denial and `500` for uninitialized stage variables used\. 

 To use the API Gateway console to modify, delete, or add a response to an API method, follow these instructions\.

1. Choose **Method Response** from **Method Execution** for a given method of an API resource\.

1. To add a new response, choose **Add Response**\.

   1.  Type an HTTP status code; for example, `200`, `400`, or `500`\) for **HTTP Status**, and then choose the check\-mark icon to save the choice\. 

       When a backend\-returned response does not have a corresponding method response defined, API Gateway fails to return the response to the client\. Instead, it returns a `500 Internal server error` error response\. 

   1. Expand the response of the given status code\.

   1. Choose **Add Header**\.

   1.  Type a name for **Name** under **Response Headers for *\{status\}***, and then choose the check\-mark icon to save the choice\. 

       If you need to translate any backend\-returned header to one defined in a method response, you must first add the method response header as described in this step \. 

   1.  Choose **Add Response Model** under **Response Body for *\{status\}***\. 

   1. Type the media type of the response payload for **Content type** and choose a model from the **Models** drop\-down menu\.

   1. Choose the check\-mark icon to save the settings\.

1. To modify an existing response, expand the response and follow Step 2 above\.

1. To remove a response, choose the **x** icon for the response and confirm you want to delete the response\.

For every response returned from the backend, you must have a compatible response configured as the method response\. However, the configuring method response headers and payload model are optional unless you map the result from the backend to the method response before returning to the client\. Also, a method response payload model is important if you are generating a strongly typed SDK for your API\.