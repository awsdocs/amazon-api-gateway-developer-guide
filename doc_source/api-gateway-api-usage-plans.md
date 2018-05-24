# Create and Use API Gateway Usage Plans<a name="api-gateway-api-usage-plans"></a>

After you create, test, and deploy your APIs, you can use API Gateway usage plans to extend them as product offerings for your customers\. You can provide usage plans to allow specified customers to access selected APIs at agreed\-upon request rates and quotas that can meet their business requirements and budget constraints\.

## What Is a Usage Plan?<a name="api-gateway-api-usage-plans-overview"></a>

A usage plan prescribes who can access one or more deployed API stages— and also how much and how fast the caller can access the APIs\. The plan uses an API key to identify an API client and meters access to an API stage with the configurable throttling and quota limits that are enforced on individual client API keys\. 

The throttling prescribes the request rate limits that are applied to each API key\. The quotas are the maximum number of requests with a given API key submitted within a specified time interval\. You can configure individual API methods to require API key authorization based on usage plan configuration\. An API stage is identified by an API identifier and a stage name\.

**Note**  
Throttling and quota limits apply to requests for individual API keys that are aggregated across all API stages within a usage plan\.

You can generate an API key in API Gateway, or import it into API Gateway from an external source\. For more information, see [Set Up API Keys Using the API Gateway Console](api-gateway-setup-api-key-with-console.md)\. 

## Expose API Key Source<a name="api-gateway-api-key-source"></a>

When you enact a usage plan for an API and enable API keys on API methods, the incoming request must come with an appropriate API key\. API Gateway reads this API key from an incoming request and verifies it against the key in the usage plan\. If the keys match, API Gateway throttles the requests according to the plan's request limit and quota\. Otherwise, it throws an `InvalidKeyParameter` exception\. As a result, the caller receives a `403 Forbidden` response\. 

There are two sources that API Gateway receives an API key from\. You can distribute the key to client developers and ask the client to pass the API key as the `X-API-Key` header of an incoming request when you require API keys on API methods\. This is known as the `HEADER` source because API Gateway reads it from the header\. Alternatively, you can have a Lambda authorizer return the API key as part of the authorization response\. For more information on the authorization response, see [Output from an Amazon API Gateway Lambda Authorizer](api-gateway-lambda-authorizer-output.md)\. This is known as the `AUTHORIZER` source because API Gateway reads it from the authorizer output\. 

 To choose an API key source using the API Gateway console, follow the instructions below:

**To choose an API key source for an API by using the API Gateway console:**

1.  Sign in to the API Gateway console\.

1.  Choose an existing API or create a new one\.

1.  In the primary navigation pane, choose **Settings** under the chosen or newly created API\.

1. Under the **API Key Source** section in the **Settings** pane, choose `HEADER` or `AUTHORIZER` from the drop\-down list\.

1.  Choose **Save Changes**\.

To choose an API key source for an API by using the AWS CLI, call the [http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html](http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) command as follows:

```
aws apigateway update-rest-api --rest-api-id 1234123412 --patch-operations op=replace,path=/apiKeySource,value=AUTHORIZER
```

To have the client submit an API key, set the `value` to `HEADER` in the above CLI command\.

To choose an API key source for an API by using the API Gateway REST API, call [http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/) as follows:

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

Depending on API key source type you choose, use one of the following procedures to use a header\-sourced API key or an authorized\-returned API key for a method invocation: 

**To use a header\-sourced API key for method invocation:**

1.  Create an API with desired API methods\. And deploy the API to a stage\.

1.  Create a new usage plan or choose an existing one\. Add the deployed API stage to the usage plan\. Attach an API key to the usage plan or choose an existing API key in the plan\. Note the chosen API key value\.

1.  Set up API methods to require an API key\. 

1.  Redeploy the API to the same stage\. If you deploy the API to a new stage, make sure to update the usage plan to attach the new API stage\. 

 The client can now call the API methods while supplying the `x-api-key` header with the chosen API key as the header value\.

**To use an authorized\-sourced API key for method invocation:**

1.  Create an API with desired API methods\. And deploy the API to a stage\.

1.  Create a new usage plan or choose an existing one\. Add the deployed API stage to the usage plan\. Attach an API key to the usage plan or choose an existing API key in the plan\. Note the chosen API Key value\.

1.  Create a custom Lambda authorizer of the token type\. Include, as a root\-level property of the authorization response, `usageIdentifierKey:{api-key}`, where `{api-key}` stands for the API key value mentioned in the previous step\.

1.  Set up API methods to require an API key and enable the Lambda authorizer on the methods as well\. 

1.  Redeploy the API to the same stage\. If you deploy the API to a new stage, make sure to update the usage plan to attach the new API stage\. 

The client can now call the API key\-required methods without explicitly supplying any API key\. The authorizer\-returned API key is used automatically\.

## How to Configure a Usage Plan?<a name="api-gateway-create-usage-plans"></a>

The following steps outline how you, as the API owner, configure a usage plan for your customers\.

**To configure a usage plan**

1. Create one or more APIs, configure the methods to require an API key, and deploy the APIs in stages\.

1. Generate API keys and distribute the keys to app developers \(your customers\) by using your APIs\.

1. Create the usage plan with the desired throttle and quota limits\.

1. Associate selected API stages and API keys to the usage plan\.

Callers of the API must supply an assigned API key in the `x-api-key` header in requests to the API\.

**Note**  
To include API methods in a usage plan, you must configure individual API methods to [require an API key](api-gateway-setup-api-key-with-console.md)\. For user authentication and authorization, don't use API keys\. Use an IAM role, [a Lambda authorizer](apigateway-use-lambda-authorizer.md), or an [Amazon Cognito user pool](apigateway-integrate-with-cognito.md)\.