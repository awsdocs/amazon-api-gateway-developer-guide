# Set up basic request validation in API Gateway<a name="api-gateway-request-validation-set-up"></a>

 You can set up request validators in an API's OpenAPI definition file and then import the OpenAPI definitions into API Gateway\. You can also set them up in the API Gateway console or by calling the API Gateway REST API, the AWS CLI, or one of the AWS SDKs\. Here, we show how to do this with an OpenAPI file, in the console, and using the API Gateway REST API\. 

**Topics**
+ [Set up basic request validation by importing OpenAPI definition](#api-gateway-request-validation-setup-importing-swagger)
+ [Set up request validators using the API Gateway REST API](#api-gateway-request-validation-setup-using-rest-api)
+ [Set up basic request validation using the API Gateway console](#api-gateway-request-validation-setup-in-console)

## Set up basic request validation by importing OpenAPI definition<a name="api-gateway-request-validation-setup-importing-swagger"></a>

The following steps describe how to enable basic request validation by importing an OpenAPI file\.

**To enable request validation by importing an OpenAPI file into API Gateway**

1.  Declare request validators in OpenAPI by specifying a set of the [x\-amazon\-apigateway\-request\-validators\.requestValidator object](api-gateway-swagger-extensions-request-validators.requestValidator.md) objects in the [x\-amazon\-apigateway\-request\-validators object](api-gateway-swagger-extensions-request-validators.md) map at the API level\. For example, the [sample API OpenAPI file](api-gateway-request-validation-sample-api-swagger.md) contains the `x-amazon-apigateway-request-validators` map, with the validators' names as the keys\. 

------
#### [ OpenAPI 3\.0 ]

   ```
   {
      "openapi": "3.0.0",
      "info": {
         "title": "ReqValidation Sample",
         "version": "1.0.0"
      },
      "servers": [
         {
            "url": "/{basePath}",
            "variables": {
               "basePath": {
                 "default": "/v1"
               }
            }
         }
      ],
      "x-amazon-apigateway-request-validators": {
         "all": {
            "validateRequestBody": true,
            "validateRequestParameters": true
         },
         "params-only": {
            "validateRequestBody": false,
            "validateRequestParameters": true
         }
      }
   }
   ```

------
#### [ OpenAPI 2\.0 ]

   ```
   {
         "swagger": "2.0",
         "info": {
           "title": "ReqValidation Sample",
           "version": "1.0.0"
         },
         "schemes": [
           "https"
         ],
         "basePath": "/v1",
         "produces": [
           "application/json"
         ],
         "x-amazon-apigateway-request-validators" : {
           "all" : {
             "validateRequestBody" : true,
             "validateRequestParameters" : true
           },
           "params-only" : {
             "validateRequestBody" : false,
             "validateRequestParameters" : true
           }
         },
         ...
   }
   ```

------

   You select a validator's name when enabling the validator on the API or on a method, as shown in the next step\. 

1.  To enable a request validator on all methods of an API, specify an [x\-amazon\-apigateway\-request\-validator property](api-gateway-swagger-extensions-request-validator.md) property at the API level of the OpenAPI definition file\. To enable a request validator on an individual method, specify the `x-amazon-apigateway-request-validator` property at the method level\. For example, the following `x-amazon-apigateway-request-validator` property enables the `params-only` validator on all API methods, unless otherwise overridden\. 

------
#### [ OpenAPI 3\.0 ]

   ```
   {
      "openapi": "3.0.0",
      "info": {
         "title": "ReqValidation Sample",
         "version": "1.0.0"
      },
      "servers": [
         {
            "url": "/{basePath}",
            "variables": {
               "basePath": {
                 "default": "/v1"
               }
            }
         }
      ],
      "x-amazon-apigateway-request-validator": "params-only",
      ...
   }
   ```

------
#### [ OpenAPI 2\.0 ]

   ```
   {
         "swagger": "2.0",
         "info": {
           "title": "ReqValidation Sample",
           "version": "1.0.0"
         },
         "schemes": [
           "https"
         ],
         "basePath": "/v1",
         "produces": [
           "application/json"
         ],
         ...
         "x-amazon-apigateway-request-validator" : "params-only",
         ...
   }
   ```

------

    To enable a request validator on an individual method, specify the `x-amazon-apigateway-request-validator` property at the method level\. For example, the following `x-amazon-apigateway-request-validator` property enables the `all` validator on the `POST /validation` method\. This overrides the `params-only` validator that is inherited from the API\. 

------
#### [ OpenAPI 3\.0 ]

   ```
   {
      "openapi": "3.0.0",
      "info": {
         "title": "ReqValidation Sample",
         "version": "1.0.0"
      },
      "servers": [
         {
            "url": "/{basePath}",
            "variables": {
               "basePath": {
                 "default": "/v1"
               }
            }
         }
      ],
      "paths": {
         "/validation": {
            "post": {
               "x-amazon-apigateway-request-validator": "all",
               ...
            }
         }
      }
      ...
   }
   ```

------
#### [ OpenAPI 2\.0 ]

   ```
   {
         "swagger": "2.0",
         "info": {
           "title": "ReqValidation Sample",
           "version": "1.0.0"
         },
         "schemes": [
           "https"
         ],
         "basePath": "/v1",
         "produces": [
           "application/json"
         ],
         ...
         "paths": {
           "/validation": {
             "post": {
               "x-amazon-apigateway-request-validator" : "all",
               ...
             },
             ...
           }
         }
         ...
   }
   ```

------

1.  In API Gateway, create the API with request validators enabled by importing this [sample OpenAPI definition](api-gateway-request-validation-sample-api-swagger.md): 

   ```
   POST /restapis?mode=import&failonwarning=true HTTP/1.1
   Content-Type: application/json
   Host: apigateway.us-east-1.amazonaws.com
   X-Amz-Date: 20170306T234936Z
   Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20170306/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={sig4_hash}
   
   Copy the JSON object from this [sample OpenAPI definition](api-gateway-request-validation-sample-api-swagger.md) and paste it here.
   ```

1.  Deploy the newly created API \(`fjd6crafxc`\) to a specified stage \(`testStage`\)\. 

   ```
   POST /restapis/fjd6crafxc/deployments HTTP/1.1
   Content-Type: application/json
   Host: apigateway.us-east-1.amazonaws.com
   X-Amz-Date: 20170306T234936Z
   Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20170306/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={sig4_hash}
   
   {
       "stageName" : "testStage",
       "stageDescription" : "Test stage",
       "description" : "First deployment",
       "cacheClusterEnabled" : "false"
   }
   ```

 For instructions on how to test the request validation using the API Gateway REST API, see [Test basic request validation using the API Gateway REST API](api-gateway-request-validation-test.md#api-gateway-request-validation-test-using-rest-api)\. For instructions on how to test using the API Gateway console, see [Test basic request validation using the API Gateway console](api-gateway-request-validation-test.md#api-gateway-request-validation-test-in-console)\. 

## Set up request validators using the API Gateway REST API<a name="api-gateway-request-validation-setup-using-rest-api"></a>

 In the API Gateway REST API, a request validator is represented by a [RequestValidator](https://docs.aws.amazon.com/apigateway/api-reference/resource/request-validator/) resource\. To have an API support the same request validators as the [Sample API](api-gateway-request-validation-sample-api-swagger.md), add to the [RequestValidators](https://docs.aws.amazon.com/apigateway/api-reference/resource/request-validators/) collection a parameters\-only validator with `params-only` as the key, and add a full validator with `all` as its key\. 

**To enable the basic request validation using the API Gateway REST API**

 We assume that you have an API similar to the [sample API](api-gateway-request-validation-sample-api-swagger.md), but have not set up the request validators\. If your API already has request validators enabled, call the appropriate `requestvalidator:update` or `method:put` action instead of `requestvalidator:create` or `method:put`\. 

1.  To set up the `params-only` request validator, call the [requestvalidator:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/requestvalidator-create/) action as follows: 

   ```
   POST /restapis/restapi-id/requestvalidators HTTP/1.1
   Content-Type: application/json
   Host: apigateway.region.amazonaws.com
   X-Amz-Date: 20170223T172652Z
   Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20170223/region/apigateway/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature={sig4_hash}
   
   {
     "name" : "params-only",
     "validateRequestBody" : "false",
     "validateRequestParameters" : "true"
   }
   ```

1.  To set up the `all` request validator, call the `requestvalidator:create` action as follows: 

   ```
   POST /restapis/restapi-id/requestvalidators HTTP/1.1
   Content-Type: application/json
   Host: apigateway.region.amazonaws.com
   X-Amz-Date: 20170223T172652Z
   Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20170223/region/apigateway/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature={sig4_hash}
   
   {
     "name" : "all",
     "validateRequestBody" : "true",
     "validateRequestParameters" : "true"
   }
   ```

    If the preceding validator keys already exist in the `RequestValidators` map, call the [requestvalidator:update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/requestvalidator-update/) action instead to reset the validation rules\. 

1.  To apply the `all` request validator to the `POST` method, call [method:put](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/) to enable the specified validator \(as identified by the [requestValidatorId](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/#requestValidatorId) property\), or call [method:update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-update/) to update the enabled validator\. 

   ```
   PUT /restapis/restapi-id/resources/resource-id/methods/POST HTTP/1.1
   Content-Type: application/json
   Host: apigateway.region.amazonaws.com
   X-Amz-Date: 20170223T172652Z
   Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20170223/region/apigateway/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature={sig4_hash}
   
   {
     "authorizationType" : "NONE",
     ...,
     "requestValidatorId" : "all"
   }
   ```

## Set up basic request validation using the API Gateway console<a name="api-gateway-request-validation-setup-in-console"></a>

 The API Gateway console lets you set up the basic request validation on a method using one of the three validators: 
+ **Validate body**: This is the body\-only validator\.
+ **Validate query string parameters and headers**: This is the parameters\-only validator\.
+ **Validate body, query string parameters, and headers**: This validator is for both body and parameters validation\.

 When you choose one of the above validators to enable it on an API method, the API Gateway console adds the validator to the API's [RequestValidators](https://docs.aws.amazon.com/apigateway/api-reference/resource/request-validators/) map if the validator has not already been added to the validators map of the API\. 

**To enable a request validator on a method**

1. Sign in to the API Gateway console if you're not already logged in\.

1. Create a new or choose an existing API\.

1. Create a new or choose an existing resource of the API\.

1. Create a new or choose an existing method the resource\.

1. Choose **Method Request**\.

1. Choose the pencil icon of **Request Validator** under **Settings**\.

1. Choose `Validate body`, `Validate query string parameters and headers`, or `Validate body, query string parameters, and headers` from the **Request Validator** drop\-down list\. Then choose the check mark icon to save your choice\.   
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/request-validator-choose-to-enable.png)

To test and use the request validator in the console, follow the instructions in [Test basic request validation using the API Gateway console](api-gateway-request-validation-test.md#api-gateway-request-validation-test-in-console)\.