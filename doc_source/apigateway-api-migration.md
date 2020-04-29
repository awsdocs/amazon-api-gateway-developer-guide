# Change a public or private API endpoint type in API Gateway<a name="apigateway-api-migration"></a>

Changing an API endpoint type requires you to update the API's configuration\. You can change an existing API type using the API Gateway console, the AWS CLI, or an AWS SDK for API Gateway\. The update operation may take up to 60 seconds to complete\. During this time, your API will be available, but its endpoint type cannot be changed again until the current change is completed\.

The following endpoint type changes are supported:
+ From edge\-optimized to regional or private
+ From regional to edge\-optimized or private
+ From private to regional

You cannot change a private API into an edge\-optimized API\.

If you are changing a public API from edge\-optimized to regional or vice versa, note that an edge\-optimized API may have different behaviors than a regional API\. For example, an edge\-optimized API removes the `Content-MD5` header\. Any MD5 hash value passed to the backend can be expressed in a request string parameter or a body property\. However, the regional API passes this header through, although it may remap the header name to some other name\. Understanding the differences helps you decide how to update an edge\-optimized API to a regional one or from a regional API to an edge\-optimized one\. 

**Topics**
+ [Use the API Gateway console to change an API endpoint type](#migrate-api-using-console)
+ [Use the AWS CLI to change an API endpoint type](#migrate-api-using-aws-cli)

## Use the API Gateway console to change an API endpoint type<a name="migrate-api-using-console"></a>

To change the API endpoint type of your API, perform one of the following sets of steps:

**To convert a public endpoint from regional or edge\-optimized and vice versa**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a REST API\.

1. Choose **Settings**\.

1. Change the **Endpoint Type** option under **Endpoint Configuration** from `Edge Optimized` to `Regional` or from `Regional` to `Edge Optimized`\.

1.  Choose **Save Changes** to start the update\.

**To convert a private endpoint to a regional endpoint**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a REST API\.

1. Edit the resource policy for your API to remove any mention of VPCs or VPC endpoints so that API calls from outside your VPC as well as inside your VPC will succeed\.

1. Choose **Settings**\.

1. Change the **Endpoint Type** to `Regional`\.

1. Choose **Save Changes** to start the update\.

1. Remove the resource policy from your API\.

1. Redeploy your API so that the changes will take effect\.

## Use the AWS CLI to change an API endpoint type<a name="migrate-api-using-aws-cli"></a>

 To use the AWS CLI to update an edge\-optimized API whose API ID is `{api-id}`, call [update\-rest\-api](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) as follows: 

```
aws apigateway update-rest-api \
    --rest-api-id {api-id} 
    --patch-operations op=replace,path=/endpointConfiguration/types/EDGE,value=REGIONAL
```

The successful response has a status code of `200 OK` and a payload similar to the following:

```
{
    
    "createdDate": "2017-10-16T04:09:31Z",
    "description": "Your first API with Amazon API Gateway. This is a sample API that integrates via HTTP with our demo Pet Store endpoints",
    "endpointConfiguration": {
        "types": "REGIONAL"
    },
    "id": "0gsnjtjck8",
    "name": "PetStore imported as edge-optimized"
}
```

Conversely, update a regional API to an edge\-optimized API as follows:

```
aws apigateway update-rest-api \
    --rest-api-id {api-id} 
    --patch-operations op=replace,path=/endpointConfiguration/types/REGIONAL,value=EDGE
```

Because [put\-rest\-api](https://docs.aws.amazon.com/cli/latest/reference/apigateway/put-rest-api.html) is for updating API definitions, it is not applicable to updating an API endpoint type\.