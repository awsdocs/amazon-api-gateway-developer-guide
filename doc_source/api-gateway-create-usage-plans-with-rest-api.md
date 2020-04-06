# Create, configure, and test usage plans using the API Gateway CLI and REST API<a name="api-gateway-create-usage-plans-with-rest-api"></a>

Before configuring a usage plan, you must have already done the following: set up methods of a selected API to require API keys, deployed or redeployed the API to a stage, and created or imported one or more API keys\. For more information, see [Set up API keys using the API Gateway REST API](api-gateway-setup-api-key-with-restapi.md)\.

To configure a usage plan by using the API Gateway REST API, use the following instructions, assuming that you've already created the APIs to be added to the usage plan\.

**Topics**
+ [Migrate to default usage plans](#api-gateway-usage-plan-migrate-to-default-with-restapi)
+ [Create a usage plan](#api-gateway-usage-plan-create-using-rest-api)
+ [Manage a usage plan by using the AWS CLI](#api-gateway-usage-plan-manage-with-cli)
+ [Test usage plans](#api-gateway-usage-plan-test-with-postman)

## Migrate to default usage plans<a name="api-gateway-usage-plan-migrate-to-default-with-restapi"></a>

When creating a usage plan the first time, you can migrate existing API stages that are associated with selected API keys to a usage plan by calling [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/account-update/) with the following body:

```
{
  "patchOperations" : [ {
    "op" : "add",
    "path" : "/features",
    "value" : "UsagePlans"
  } ]
}
```

For more information about migrating API stages associated with API keys, see [Migrate to Default Usage Plans in the API Gateway Console](api-gateway-create-usage-plans-with-console.md#api-gateway-usage-plan-migrate-to-default)\.

## Create a usage plan<a name="api-gateway-usage-plan-create-using-rest-api"></a>

The following procedure describes how to create a usage plan\.

**To create a usage plan with the REST API**

1. Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplan-create/) to create a usage plan\. In the payload, specify the name and description of the plan, associated API stages, rate limits, and quotas\. 

   Make note of the resultant usage plan identifier\. You need it in the next step\.

1. Do one of the following:

   1. Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplankey-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/usageplankey-create/) to add an API key to the usage plan\. Specify `keyId` and `keyType` in the payload\. 

      To add more API keys to the usage plan, repeat the previous call, one API key at a time\.

   1. Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-import/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-import/) to add one or more API keys directly to the specified usage plan\. The request payload should contain API key values, the associated usage plan identifier, the Boolean flags to indicate that the keys are enabled for the usage plan, and, possibly, the API key names and descriptions\. 

      The following example of the `apikey:import` request adds three API keys \(as identified by `key`, `name`, and `description`\) to one usage plan \(as identified by `usageplanIds`\): 

      ```
      POST /apikeys?mode=import&format=csv&failonwarnings=fase HTTP/1.1
      Host: apigateway.us-east-1.amazonaws.com
      Content-Type: text/csv
      Authorization: ...
      
      key,name, description, enabled, usageplanIds
      abcdef1234ghijklmnop8901234567, importedKey_1, firstone,  tRuE, n371pt 
      abcdef1234ghijklmnop0123456789, importedKey_2, secondone, TRUE, n371pt
      abcdef1234ghijklmnop9012345678, importedKey_3,          , true, n371pt
      ```

      As a result, three `UsagePlanKey` resources are created and added to the `UsagePlan`\.

      You can also add API keys to more than one usage plan this way\. To do this, change each `usageplanIds` column value to a comma\-separated string that contains the selected usage plan identifiers, and is enclosed within a pair of quotes \(`"n371pt,m282qs"` or `'n371pt,m282qs'`\)\.
**Note**  
An API key can be associated with more than one usage plan\. A usage plan can be associated with more than one stage\. However, a given API key can only be associated with one usage plan for each stage of your API\.

## Manage a usage plan by using the AWS CLI<a name="api-gateway-usage-plan-manage-with-cli"></a>

The following code examples show how to add, remove, or modify the method\-level throttling settings in a usage plan by calling the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-usage-plan.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-usage-plan.html) command\.

**Note**  
Be sure to change `us-east-1` to the appropriate Region value for your API\.

To add or replace a rate limit for throttling an individual resource and method:

```
aws apigateway --region us-east-1 update-usage-plan --usage-plan-id <planId> --patch-operations 
                        op="replace",path="/apiStages/<apiId>:<stage>/throttle/<resourcePath>/<httpMethod>/rateLimit",value="0.1"
```

To add or replace a burst limit for throttling an individual resource and method:

```
aws apigateway --region us-east-1 update-usage-plan --usage-plan-id <planId> --patch-operations op="replace",path="/apiStages/<apiId>:<stage>/throttle/<resourcePath>/<httpMethod>/burstLimit",value="1"
```

To remove the method\-level throttling settings for an individual resource and method:

```
aws apigateway --region us-east-1 update-usage-plan --usage-plan-id <planId> --patch-operations op="remove",path="/apiStages/<apiId>:<stage>/throttle/<resourcePath>/<httpMethod>",value=""
```

To remove all method\-level throttling settings for an API:

```
aws apigateway --region us-east-1 update-usage-plan --usage-plan-id <planId> --patch-operations op="remove",path="/apiStages/<apiId>:<stage>/throttle ",value=""
```

Here is an example using the Pet Store sample API:

```
aws apigateway --region us-east-1 update-usage-plan --usage-plan-id <planId> --patch-operations 
                op="replace",path="/apiStages/<apiId>:<stage>/throttle",value='"{\"/pets/GET\":{\"rateLimit\":1.0,\"burstLimit\":1},\"//GET\":{\"rateLimit\":1.0,\"burstLimit\":1}}"'
```

## Test usage plans<a name="api-gateway-usage-plan-test-with-postman"></a>

As an example, let's use the PetStore API, which was created in [Tutorial: Create a REST API by importing an example](api-gateway-create-api-from-example.md)\. Assume that the API is configured to use an API key of `Hiorr45VR...c4GJc`\. The following steps describe how to test a usage plan\.

**To test your usage plan**
+ Make a `GET` request on the Pets resource \(`/pets`\), with the `?type=...&page=...` query parameters, of the API \(for example, `xbvxlpijch`\) in a usage plan:

  ```
  GET /testStage/pets?type=dog&page=1 HTTP/1.1
  x-api-key: Hiorr45VR...c4GJc
  Content-Type: application/x-www-form-urlencoded
  Host: xbvxlpijch.execute-api.ap-southeast-1.amazonaws.com
  X-Amz-Date: 20160803T001845Z
  Authorization: AWS4-HMAC-SHA256 Credential={access_key_ID}/20160803/ap-southeast-1/execute-api/aws4_request, SignedHeaders=content-type;host;x-amz-date;x-api-key, Signature={sigv4_hash}
  ```
**Note**  
You must submit this request to the `execute-api` component of API Gateway and provide the required API key \(for example, `Hiorr45VR...c4GJc`\) in the required `x-api-key` header\.

  The successful response returns a `200 OK` status code and a payload that contains the requested results from the backend\. If you forget to set the `x-api-key` header or set it with an incorrect key, you get a `403 Forbidden` response\. However, if you didn't configure the method to require an API key, you will likely get a `200 OK` response whether you set the `x-api-key` header correctly or not, and the throttle and quota limits of the usage plan are bypassed\. 

  Occasionally, when an internal error occurs where API Gateway is unable to enforce usage plan throttling limits or quotas for the request, API Gateway serves the request without applying the throttling limits or quotas as specified in the usage plan\. But, it logs an error message of `Usage Plan check failed due to an internal error` in CloudWatch\. You can ignore such occasional errors\. 