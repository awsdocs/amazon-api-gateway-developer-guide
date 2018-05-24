# Set up a Regional API in API Gateway<a name="create-regional-api"></a>

 When API requests predominantly originate from an EC2 instance or services within the same region as the API is deployed, a regional API endpoint will typically lower the latency of connections and is recommended for such scenarios\. In addition, for customers to manage their own Amazon CloudFront distribution, they can use a regional API endpoint to ensure that API Gateway does not associate the API with the service\-controlled CloudFront distributions\. 

 To create a regional API, you follow the steps in [creating an edge\-optimized API](create-api-resources-methods.md), but must explicitly set `REGIONAL` type as the only option of the API's [endpointConfiguration](http://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/#endpointConfiguration)\. 

 In the following, we show how to create a regional API using the API Gateway console, AWS CLI, the AWS SDK for Javascript for Node\.js, and the API Gateway REST API\. 

**Topics**
+ [Create a Regional API Using the API Gateway Console](#create-regional-endpoint-api-using-console)
+ [Create a Regional API Using the AWS CLI](#create-regional-endpoint-api-using-aws-cli)
+ [Create a Regional API Using the AWS SDK for JavaScript](#create-regional-endpoint-api-using-nodejs-sdk)
+ [Create a Regional API Using the API Gateway REST API](#create-regional-endpoint-api-using-rest-api)
+ [Test a Regional API](#create-regional-endpoint-api-invoke-url)

## Create a Regional API Using the API Gateway Console<a name="create-regional-endpoint-api-using-console"></a>

**To create a regional API using the API Gateway console**

1.  Sign in to the API Gateway console and choose **\+ Create API**\.

1.  Under **Create new API**, choose the **New API** option\. 

1.  Type a name \(for example, `Simple PetStore (Console, Regional)`\) for **API name**\. 

1.  Choose `Regional` for **Endpoint Type**\. 

1.  Choose **Create API**\.

From here on, you can proceed to set up API methods and their associated integrations as described in [creating an edge optimized API](api-gateway-create-api-step-by-step.md)\. 

## Create a Regional API Using the AWS CLI<a name="create-regional-endpoint-api-using-aws-cli"></a>

To create a regional API using the AWS CLI, call the `create-rest-api` command:

```
aws apigateway create-rest-api \
        --name 'Simple PetStore (AWS CLI, Regional)' \
        --description 'Simple regional PetStore API' \
        --region us-west-2 \
        --endpoint-configuration '{ types: ["REGIONAL"] }'
```

The successful response returns an output similar to the following:

```
{
    "createdDate": "2017-10-13T18:41:39Z",
    "description": "Simple regional PetStore API",
    "endpointConfiguration": {
        "types": "REGIONAL"
    },
    "id": "0qzs2sy7bh",
    "name": "Simple PetStore (AWS CLI, Regional)"
}
```

 From here on, you can follow the same instructions given in [Set up an Edge\-Optimized API Using AWS CLI Commands](create-api-using-awscli.md) to set up methods and integrations for this API\. 

## Create a Regional API Using the AWS SDK for JavaScript<a name="create-regional-endpoint-api-using-nodejs-sdk"></a>

To create a regional API, using the AWS SDK for JavaScript:

```
apig.createRestApi({
	name: "Simple PetStore (node.js SDK, regional)",
	endpointConfiguration: {
		types: ['REGIONAL']
	},
	description: "Demo regional API created using the AWS SDK for node.js",
	version: "0.00.001"
}, function(err, data){
	if (!err) {
		console.log('Create API succeeded:\n', data);
		restApiId = data.id;
	} else {
		console.log('Create API failed:\n', err);
	}
});
```

The successful response returns an output similar to the following:

```
{
    "createdDate": "2017-10-13T18:41:39Z",
    "description": "Demo regional API created using the AWS SDK for node.js",
    "endpointConfiguration": {
        "types": "REGIONAL"
    },
    "id": "0qzs2sy7bh",
    "name": "Simple PetStore (node.js SDK, regional)"
}
```

 After completing the preceding steps, you can follow the instructions in [ Set up an Edge\-Optimized API Using the AWS SDK for Node\.js ](create-api-using-awssdk.md) to set up methods and integrations for this API\. 

## Create a Regional API Using the API Gateway REST API<a name="create-regional-endpoint-api-using-rest-api"></a>

To create a regional API using the API Gateway REST API, submit a POST request as follows:

```
POST /restapis HTTP/1.1
Host: apigateway.us-west-2.amazonaws.com
Content-Type: application/x-amz-json-1.0
X-Amz-Date: 20170511T214723Z
Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c

{
    "name": "Simple PetStore (REST API, Regional)",
    "description": "A sample API Gateway API created using the REST API.",
    "endpointConfiguration" : {
       "types" : ["REGIONAL"]
    }
}
```

The successful response has the status code of `201 Created` and a body similar to the following output:

```
{
    "createdDate": "2017-10-13T18:41:39Z",
    "description": "A sample API Gateway API created using the REST API.",
    "endpointConfiguration": {
        "types": "REGIONAL"
    },
    "id": "0qzs2sy7bh",
    "name": "Simple PetStore (REST API, Regional)"
}
```

 After completing the preceding steps, you can follow the instructions in [Set up an Edge\-Optimized API Using the API Gateway REST API](create-api-using-restapi.md) to set up methods and integrations for this API\. 

## Test a Regional API<a name="create-regional-endpoint-api-invoke-url"></a>

Once deployed, the regional API's default URL host name is of the following format: 

```
{restapi-id}.execute-api.{region}.amazonaws.com
```

The base URL to invoke the API is like the following:

```
https://{restapi-id}.execute-api.{region}.amazonaws.com/{stage}
```

Assuming you set up the `GET /pets` and `GET /pets/{petId}` methods in this example, you can test the API by typing the following URLs in a browser:

```
https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets
```

and 

```
https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets/1
```

Alternatively, you can use cURL commands:

```
curl -X GET https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets 
```

and 

```
curl -X GET https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets/2
```