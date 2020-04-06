# Set up Lambda custom integrations in API Gateway<a name="set-up-lambda-custom-integrations"></a>

 To show how to set up the Lambda custom integration, we create an API Gateway API to expose the `GET /greeting?greeter={name}` method to invoke a Lambda function\. The function responds with a message of `"Hello, {name}!"` if the `greeter` parameter value is a non\-empty string\. It returns a message of `"Hello, World!"` if the `greeter` value is an empty string\. The function returns an error message of `"Missing the required greeter parameter."` if the greeter parameter is not set in the incoming request\. We name the function `HelloWorld`\.

For reference, a Node\.js version of the Lambda function is shown as follows:

```
exports.handler = function(event, context, callback) {
    var res ={
        "statusCode": 200,
        "headers": {
            "Content-Type": "*/*"
        }
    };
    if (event.greeter==null) {
        callback(new Error('Missing the required greeter parameter.'));
    } else if (event.greeter === "") {
        res.body = "Hello, World";
        callback(null, res);
    } else {
        res.body = "Hello, " + event.greeter +"!";
        callback(null, res);
    }
};
```

You can create it in the Lambda console or by using the AWS CLI\. In this section, we reference this function using the following ARN:

```
arn:aws:lambda:us-east-1:123456789012:function:HelloWorld
```

With the Lambda function set in the backend, proceed to set up the API\.<a name="set-up-lambda-custom-integration-using-cli"></a>

**To set up the Lambda custom integration using the AWS CLI**

1.  Call the `create-rest-api` command to create an API:

   ```
   aws apigateway create-rest-api --name 'HelloWorld (AWS CLI)' --region us-west-2
   ```

   Note the resulting API's `id` value \(`te6si5ach7`\) in the response:

   ```
   {
       "name": "HelloWorld (AWS CLI)", 
       "id": "te6si5ach7", 
       "createdDate": 1508461860
   }
   ```

   You need the API `id` throughout this section\.

1.  Call the `get-resources` command to get the root resource `id`:

   ```
   aws apigateway get-resources --rest-api-id te6si5ach7 --region us-west-2
   ```

   The successful response is as follows:

   ```
   {
       "items": [
           {
               "path": "/", 
               "id": "krznpq9xpg"
           }
       ]
   }
   ```

   Note the root resource `id` value \(`krznpq9xpg`\)\. You need it in the next step and later\.

1.  Call `create-resource` to create an API Gateway [Resource](https://docs.aws.amazon.com/apigateway/api-reference/resource/resource/) of `/greeting`:

   ```
   aws apigateway create-resource --rest-api-id te6si5ach7 \
         --region us-west-2 \
         --parent-id krznpq9xpg \
         --path-part greeting
   ```

   The successful response is similar to the following:

   ```
   {
       "path": "/greeting", 
       "pathPart": "greeting", 
       "id": "2jf6xt", 
       "parentId": "krznpq9xpg"
   }
   ```

   Note the resulting `greeting` resource's `id` value \(`2jf6xt`\)\. You need it to create a method on the `/greeting` resource in the next step\.

1.  Call `put-method` to create an API method request of `GET /greeting?greeter={name}`:

   ```
   aws apigateway put-method --rest-api-id te6si5ach7 \
          --region us-west-2 \
          --resource-id 2jf6xt \
          --http-method GET \
          --authorization-type "NONE" \
          --request-parameters method.request.querystring.greeter=false
   ```

   The successful response is similar to the following:

   ```
   {
       "apiKeyRequired": false, 
       "httpMethod": "GET", 
       "authorizationType": "NONE", 
       "requestParameters": {
           "method.request.querystring.greeter": false
       }
   }
   ```

   This API method allows the client to receive a greeting from the Lambda function at the backend\. The `greeter` parameter is optional because the backend should handle either an anonymous caller or a self\-identified caller\.

1.  Call `put-method-response` to set up the `200 OK` response to the method request of `GET /greeting?greeter={name}`:

   ```
   aws apigateway put-method-response \
           --region us-west-2 \
           --rest-api-id te6si5ach7 \ 
           --resource-id 2jf6xt \
           --http-method GET \
           --status-code 200
   ```

1.  Call `put-integration` to set up the integration of the `GET /greeting?greeter={name}` method with a Lambda function, named `HelloWorld`\. The function responds to the request with a message of `"Hello, {name}!"`, if the `greeter` parameter is provided, or `"Hello, World!"`, if the query string parameter is not set\.

   ```
   aws apigateway put-integration \
           --region us-west-2 \
           --rest-api-id vaz7da96z6 \
           --resource-id 2jf6xt \
           --http-method GET \
           --type AWS \
           --integration-http-method POST \
           --uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:HelloWorld/invocations \
           --request-templates file://path/to/integration-request-template.json \
           --credentials arn:aws:iam::123456789012:role/apigAwsProxyRole
   ```

   Here, the `request-template` parameter value, `file://path/to/integration-request-template.json`, points to a JSON file, named `integration-request-template.json` in the `path/to` directory, which contains a key\-value map as a JSON object\. The key is a media type of the request payload and the value is a mapping template for the body of the specified content type\. In this example, the JSON file contains the following JSON object:

   ```
   {"application/json":"{\"greeter\":\"$input.params('greeter')\"}"}
   ```

   The mapping template supplied here translates the `greeter` query string parameter to the `greeter` property of the JSON payload\. This is necessary because input to a Lambda function in the Lambda function must be expressed in the body\. You could use JSON string of the map \(for example, `"{\"greeter"\: \"'john'\"}"`\) as the `request-template` input value to the `put-integration` command\. However, using the file input avoids the difficult, and sometimes impossible, quote\-escaping that is required to stringify a JSON object\.
**Important**  
For Lambda integrations, you must use the HTTP method of `POST` for the integration request, according to the [specification of the Lambda service action for function invocations](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html)\. The `uri` parameter is the ARN of the function\-invoking action\.  
Successful output is similar to the following:

   ```
   {
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "cacheKeyParameters": [], 
       "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:HelloWorld/invocations", 
       "httpMethod": "POST", 
       "requestTemplates": {
           "application/json": "{\"greeter\":\"$input.params('greeter')\"}"
       }, 
       "cacheNamespace": "krznpq9xpg", 
       "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole", 
       "type": "AWS"
   }
   ```

   The IAM role of `apigAwsProxyRole` must have policies that allow the `apigateway` service to invoke Lambda functions\. Instead of supplying an IAM role for `credentials`, you can call the [add\-permission](https://docs.aws.amazon.com/cli/latest/reference/lambda/add-permission.html) command to add resource\-based permissions\. This is how the API Gateway console adds these permissions\. 

1. Call `put-integration-response` to set up the integration response to pass the Lambda function output to the client as the `200 OK` method response\.

   ```
    aws apigateway put-integration-response \
           --region us-west-2 \
           --rest-api-id te6si5ach7 \
           --resource-id 2jf6xt \
           --http-method GET \
           --status-code 200 \
           --selection-pattern ""
   ```

   By setting the selection\-pattern to an empty string, the `200 OK` response is the default\. 

   The successful response should be similar to the following:

   ```
    {
       "selectionPattern": "", 
       "statusCode": "200"
   }
   ```

1.  Call `create-deployment` to deploy the API to a `test` stage:

   ```
   aws apigateway create-deployment --rest-api-id te6si5ach7 --stage-name test
   ```

1.  Test the API using the following cURL command in a terminal:

   ```
   curl -X GET 'https://te6si5ach7.execute-api.us-west-2.amazonaws.com/test/greeting?greeter=me' \
     -H 'authorization: AWS4-HMAC-SHA256 Credential={access_key}/20171020/us-west-2/execute-api/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature=f327...5751'
   ```