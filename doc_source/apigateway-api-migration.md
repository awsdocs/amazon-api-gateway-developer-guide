# Update an API Endpoint Type in API Gateway<a name="apigateway-api-migration"></a>

 Updating an API endpoint type requires you to update the API's configuration\. You can update an existing API type from edge\-optimized to regional, and vice\-versa, using the API Gateway console, AWS CLI, an AWS SDK for API Gateway, or the API Gateway REST API\. The update operation may take up to 60 seconds to complete\. 

An edge\-optimized API may have different behaviors than a regional API\. For example, an edge\-optimized API removes the `Content-MD5` header\. Any MD5 hash value passed to the backend can be expressed in a request string parameter or a body property\. However, the regional API passes this header through, although it may remap the header name to some other name\.  Understanding the differences helps you decide how to update an edge\-optimized API to a regional one or from a regional API to an edge\-optimized one\. 

**Topics**
+ [Use the API Gateway Console to Update an API Endpoint](#migrate-api-using-console)
+ [Use the AWS CLI to Update an API Endpoint](#migrate-api-using-aws-cli)
+ [Use the API Gateway REST API to Update an API Endpoint](#migrate-api-using-api-gateway-rest-api)

## Use the API Gateway Console to Update an API Endpoint<a name="migrate-api-using-console"></a>

1.  Sign in to the console and choose **APIs** in the primary navigation pane\.

1.  Choose the settings \(gear icon\) of an API under **\+ Create API**\.  
![\[Update API endpoint in console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/update-api-endpoint-in-console.png)

1.  Change the **Endpoint Type** option under **Endpoint Configuration** from `Edge Optimized` to `Regional` or from `Regional` to `Edge Optimized`\.

1.  Choose **Save** to start the update\.

## Use the AWS CLI to Update an API Endpoint<a name="migrate-api-using-aws-cli"></a>

 To use the AWS CLI commands to update an edge\-optimized API of `{api-id}`, call the [restapi:update](http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) as follows: 

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

Because [put\-rest\-api](http://docs.aws.amazon.com/cli/latest/reference/apigateway/put-rest-api.html) is for updating API definitions, it is not applicable to updating an API endpoint type\.

## Use the API Gateway REST API to Update an API Endpoint<a name="migrate-api-using-api-gateway-rest-api"></a>

 To use the API Gateway REST API to update an edge\-optimized API of `{api-id}`, call the [restapi:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/) as follows: 

```
PATCH /restapis/{api-id}

{
  "patchOperations" : [{
    "op" : "replace",
    "path" : "/endpointConfiguration/types/EDGE",
    "value" : "REGIONAL"
  }]
}
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

Conversely, to update a regional API to an edge\-optimized API, call the [http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-update/) as follows:

```
PATCH /restapis/{api-id}

{
  "patchOperations" : [{
    "op" : "replace",
    "path" : "/endpointConfiguration/types/REGIONAL",
    "value" : "EDGE"
  }]
}
```

Because [restapi:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-put/) is for updating API definitions, it is not applicable to updating an API endpoint type\.