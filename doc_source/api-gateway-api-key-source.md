# Choose an API key source<a name="api-gateway-api-key-source"></a>

When you associate a usage plan with an API and enable API keys on API methods, every incoming request to the API must contain an [API key](api-gateway-basic-concept.md#apigateway-definition-api-key)\. API Gateway reads the key and compares it against the keys in the usage plan\. If there is a match, API Gateway throttles the requests according to the plan's request limit and quota\. Otherwise, it throws an `InvalidKeyParameter` exception\. As a result, the caller receives a `403 Forbidden` response\.

Your API Gateway API can receive API keys from one of two sources:

**`HEADER`**  
You distribute API keys to your customers and require them to pass the API key as the `X-API-Key` header of each incoming request\. 

**`AUTHORIZER`**  
You have a Lambda authorizer return the API key as part of the authorization response\. For more information on the authorization response, see [Output from an Amazon API Gateway Lambda authorizer](api-gateway-lambda-authorizer-output.md)\.

**To choose an API key source for an API by using the API Gateway console:**

1. Sign in to the API Gateway console\.

1. Choose an existing API or create a new one\.

1. In the primary navigation pane, choose **Settings** under the chosen or newly created API\.

1. Under the **API Key Source** section in the **Settings** pane, choose `HEADER` or `AUTHORIZER` from the drop\-down list\.

1. Choose **Save Changes**\.

To choose an API key source for an API by using the AWS CLI, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) command as follows:

```
aws apigateway update-rest-api --rest-api-id 1234123412 --patch-operations op=replace,path=/apiKeySource,value=AUTHORIZER
```

To have the client submit an API key, set the `value` to `HEADER` in the preceding CLI command\.

To choose an API key source for an API by using the API Gateway REST API, call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/) as follows:

```
PATCH /restapis/fugvjdxtri/ HTTP/1.1
Content-Type: application/json
Host: apigateway.us-east-1.amazonaws.com
X-Amz-Date: 20160603T205348Z
Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20160603/us-east-1/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature={sig4_hash}

{
  "patchOperations" : [
    {
        "op" : "replace",
        "path" : "/apiKeySource",
        "value" : "HEADER"
    }
  ]
}
```

To have an authorizer return an API key, set the `value` to `AUTHORIZER` in the previous `patchOperations` input\.

Depending on the API key source type you choose, use one of the following procedures to use header\-sourced API keys or authorizer\-returned API keys in method invocation: 

**To use header\-sourced API keys:**

1.  Create an API with desired API methods\. And deploy the API to a stage\.

1.  Create a new usage plan or choose an existing one\. Add the deployed API stage to the usage plan\. Attach an API key to the usage plan or choose an existing API key in the plan\. Note the chosen API key value\.

1.  Set up API methods to require an API key\. 

1.  Redeploy the API to the same stage\. If you deploy the API to a new stage, make sure to update the usage plan to attach the new API stage\. 

 The client can now call the API methods while supplying the `x-api-key` header with the chosen API key as the header value\.

**To use authorizer\-sourced API keys:**

1.  Create an API with desired API methods\. And deploy the API to a stage\.

1.  Create a new usage plan or choose an existing one\. Add the deployed API stage to the usage plan\. Attach an API key to the usage plan or choose an existing API key in the plan\. Note the chosen API key value\.

1.  Create a custom Lambda authorizer of the token type\. Include, as a root\-level property of the authorization response, `usageIdentifierKey:{api-key}`, where `{api-key}` stands for the API key value mentioned in the previous step\.

1.  Set up API methods to require an API key and enable the Lambda authorizer on the methods as well\. 

1.  Redeploy the API to the same stage\. If you deploy the API to a new stage, make sure to update the usage plan to attach the new API stage\. 

The client can now call the API key\-required methods without explicitly supplying any API key\. The authorizer\-returned API key is used automatically\.