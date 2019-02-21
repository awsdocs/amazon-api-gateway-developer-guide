# Output from an Amazon API Gateway Lambda Authorizer<a name="api-gateway-lambda-authorizer-output"></a>

A Lambda authorizer function's output is a dictionary\-like object, which must include the principal identifier \(`principalId`\) and a policy document \(`policyDocument`\) containing a list of policy statements\. The output can also include a `context` map containing key\-value pairs\. If the API uses a usage plan \(the [https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/#apiKeySource](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/#apiKeySource) is set to `AUTHORIZER`\), the Lambda authorizer function must return one of the usage plan's API keys as the `usageIdentifierKey` property value\.

The following shows an example of this output\. 

```
{
  "principalId": "yyyyyyyy", // The principal user identification associated with the token sent by the client.
  "policyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Allow|Deny",
        "Resource": "arn:aws:execute-api:{regionId}:{accountId}:{appId}/{stage}/{httpVerb}/[{resource}/[child-resources]]"
      }
    ]
  },
  "context": {
    "stringKey": "value",
    "numberKey": "1",
    "booleanKey": "true"
  },
  "usageIdentifierKey": "{api-key}"
}
```

 Here, a policy statement specifies whether to allow or deny \(`Effect`\) the API Gateway execution service to invoke \(`Action`\) the specified API method \(`Resource`\)\. You can use a wild card \(`*`\) to specify a resource type \(method\)\. For information about setting valid policies for calling an API, see [Statement Reference of IAM Policies for Executing API in API Gateway](api-gateway-control-access-using-iam-policies-to-invoke-api.md#api-gateway-calling-api-permissions)\. 

For an authorization\-enabled method ARN, e\.g\., `arn:aws:execute-api:{region-id}:{account-id}:{api-id}/{stage-id}/{method}/{resource}/{path}`, the maximum length is 1600 bytes\. The path parameter values, the size of which are determined at run time, can cause the ARN length to exceed the limit\. When this happens, the API client will receive a `414 Request URI too long` response\. 

In addition, the Resource ARN, as shown in the policy statement output by the authorizer, is currently limited to 512 characters long\. For this reason, you must not use URI with a JWT token of a significant length in a request URI\. You can safely pass the JWT token in a request header, instead\.

 You can access the `principalId` value in a mapping template using the `$context.authorizer.principalId` variable\. This is useful if you want to pass the value to the backend\. For more information, see [Accessing the `$context` Variable](api-gateway-mapping-template-reference.md#context-variable-reference)\. 

 You can access the `stringKey`, `numberKey`, or `booleanKey` value \(for example, `"value"`, `"1"`, or `"true"`\) of the `context` map in a mapping template by calling `$context.authorizer.stringKey`, `$context.authorizer.numberKey`, or `$context.authorizer.booleanKey`, respectively\. The returned values are all stringified\. Notice that you cannot set a JSON object or array as a valid value of any key in the `context` map\. 

`{api-key}` stands for an API key in the API stage's usage plan\. For more information, see [Create and Use Usage Plans with API Keys](api-gateway-api-usage-plans.md)\.

 The following shows example output from the example Lambda authorizer\. The example output contains a policy statement to block \(`Deny`\) calls to the `GET` method in an API \(`ymy8tbxw7b`\) of an AWS account \(`123456789012`\) in any stage \(`*`\)\. 

```
{
  "principalId": "user",
  "policyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "execute-api:Invoke",
        "Effect": "Deny",
        "Resource": "arn:aws:execute-api:us-west-2:123456789012:ymy8tbxw7b/*/GET/"
      }
    ]
  }
}
```