# Working with stages for HTTP APIs<a name="http-api-stages"></a>

An API stage is a logical reference to a lifecycle state of your API \(for example, `dev`, `prod`, `beta`, or `v2`\)\. API stages are identified by their API ID and stage name, and they're included in the URL you use to invoke the API\. Each stage is a named reference to a deployment of the API and is made available for client applications to call\.

You can create a `$default` stage that is served from the base of your API's URL—for example, `https://{api_id}.execute-api.{region}.amazonaws.com/`\. You use this URL to invoke an API stage\.

A deployment is a snapshot of your API configuration\. After you deploy an API to a stage, it’s available for clients to invoke\. You must deploy an API for changes to take effect\. If you enable automatic deployments, changes to an API are automatically released for you\.

## Stage variables<a name="http-api-stages.stage-variables"></a>

Stage variables are key\-value pairs that you can define for a stage of an HTTP API\. They act like environment variables and can be used in your API setup\.

For example, you can define a stage variable, and then set its value as an HTTP endpoint for an HTTP proxy integration\. Later, you can reference the endpoint by using the associated stage variable name\. By doing this, you can use the same API setup with a different endpoint at each stage\. Similarly, you can use stage variables to specify a different AWS Lambda function integration for each stage of your API\. 

### Examples<a name="http-api-stages.stage-variables-examples"></a>

To use a stage variable to customize the HTTP integration endpoint, you must first set the name and value of the stage variable \(for example, `url`\) with a value of `example.com`\. Next, set up an HTTP proxy integration\. Instead of entering the endpoint's URL, you can tell API Gateway to use the stage variable value, **http://$\{stageVariables\.url\}**\. This value tells API Gateway to substitute your stage variable `${}` at runtime, depending on the stage of your API\. 

You can reference stage variables in a similar way to specify a Lambda function name or an AWS role ARN\.

When specifying a Lambda function name as a stage variable value, you must configure the permissions on the Lambda function manually\. You can use the AWS Command Line Interface \(AWS CLI\) to do this\.

```
aws lambda add-permission --function-name arn:aws:lambda:XXXXXX:your-lambda-function-name --source-arn arn:aws:execute-api:us-east-1:YOUR_ACCOUNT_ID:api_id/*/HTTP_METHOD/resource --principal apigateway.amazonaws.com --statement-id apigateway-access --action lambda:InvokeFunction
```

## API Gateway stage variables reference<a name="http-api-stages.stage-variables-reference"></a>

### HTTP integration URIs<a name="http-api-stages.stage-variables-in-integration-HTTP-uris"></a>

You can use a stage variable as part of an HTTP integration URI, as shown in the following examples\.
+ A full URI without protocol – `http://${stageVariables.<variable_name>}`
+ A full domain – `http://${stageVariables.<variable_name>}/resource/operation`
+ A subdomain – `http://${stageVariables.<variable_name>}.example.com/resource/operation`
+ A path – `http://example.com/${stageVariables.<variable_name>}/bar`
+ A query string – `http://example.com/foo?q=${stageVariables.<variable_name>}` 

### Lambda functions<a name="http-api-stages.stage-variables-in-integration-lambda-functions"></a>

 You can use a stage variable in place of a Lambda function name or alias, as shown in the following examples\. 
+ `arn:aws:apigateway:<region>:lambda:path/2015-03-31/functions/arn:aws:lambda:<region>:<account_id>:function:${stageVariables.<function_variable_name>}/invocations`
+ `arn:aws:apigateway:<region>:lambda:path/2015-03-31/functions/arn:aws:lambda:<region>:<account_id>:function:<function_name>:${stageVariables.<version_variable_name>}/invocations`

### AWS integration credentials<a name="http-api-stages.stage-variables-in-integration-aws-credentials"></a>

 You can use a stage variable as part of an AWS user or role credential ARN, as shown in the following example\. 
+  `arn:aws:iam::<account_id>:${stageVariables.<variable_name>}` 