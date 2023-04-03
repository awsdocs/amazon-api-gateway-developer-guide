# Set up asynchronous invocation of the backend Lambda function<a name="set-up-lambda-integration-async"></a>

In Lambda non\-proxy \(custom\) integration, the backend Lambda function is invoked synchronously by default\. This is the desired behavior for most REST API operations\. Some applications, however, require work to be performed asynchronously \(as a batch operation or a long\-latency operation\), typically by a separate backend component\. In this case, the backend Lambda function is invoked asynchronously, and the front\-end REST API method doesn't return the result\.

You can configure the Lambda function for a Lambda non\-proxy integration to be invoked asynchronously by specifying `'Event'` as the [Lambda invocation type](https://docs.aws.amazon.com/lambda/latest/dg/invocation-options.html)\. This is done as follows:

## Configure Lambda asynchronous invocation in the API Gateway console<a name="asynchronous-invocation-console-examples"></a>

For all invocations to be asynchronous:
+ In **Integration Request**, add an `X-Amz-Invocation-Type` header with a static value of `'Event'`\.

For clients to decide if invocations are asynchronous or synchronous:

1. In **Method Request**, add an `InvocationType` header\.

1. In **Integration Request** add an `X-Amz-Invocation-Type` header with a mapping expression of `method.request.header.InvocationType`\.

1. Clients can include the `InvocationType: Event` header in API requests for asynchronous invocations or `InvocationType: RequestResponse` for synchronous invocations\.

## Configure Lambda asynchronous invocation using OpenAPI<a name="asynchronous-invocation-OpenAPI-examples"></a>

For all invocations to be asynchronous:
+  Add the `X-Amz-Invocation-Type` header to the **x\-amazon\-apigateway\-integration** section\.

  ```
  "x-amazon-apigateway-integration" : {
            "type" : "aws",
            "httpMethod" : "POST",
            "uri" : "arn:aws:apigateway:us-east-2:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-2:123456789012:function:my-function/invocations",
            "responses" : {
              "default" : {
                "statusCode" : "200"
              }
            },
            "requestParameters" : {
              "integration.request.header.X-Amz-Invocation-Type" : "'Event'"
            },
            "passthroughBehavior" : "when_no_match",
            "contentHandling" : "CONVERT_TO_TEXT"
          }
  ```

For clients to decide if invocations are asynchronous or synchronous:

1.  Add the following header on any [OpenAPI Path Item Object](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.1.0.md#pathItemObject)\. 

   ```
   "parameters" : [ {
   "name" : "InvocationType",
   "in" : "header",
   "schema" : {
     "type" : "string"
   }
   } ]
   ```

1.  Add the `X-Amz-Invocation-Type` header to **x\-amazon\-apigateway\-integration** section\.

   ```
   "x-amazon-apigateway-integration" : {
             "type" : "aws",
             "httpMethod" : "POST",
             "uri" : "arn:aws:apigateway:us-east-2:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-2:123456789012:function:my-function/invocations",
             "responses" : {
               "default" : {
                 "statusCode" : "200"
               }
             },
             "requestParameters" : {
               "integration.request.header.X-Amz-Invocation-Type" : "method.request.header.InvocationType"
             },
             "passthroughBehavior" : "when_no_match",
             "contentHandling" : "CONVERT_TO_TEXT"
           }
   ```

1.  Clients can include the `InvocationType: Event` header in API requests for asynchronous invocations or `InvocationType: RequestResponse` for synchronous invocations\. 