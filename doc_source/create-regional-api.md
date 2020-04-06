# Set up a regional API in API Gateway<a name="create-regional-api"></a>

When API requests predominantly originate from an EC2 instance or services within the same region as the API is deployed, a regional API endpoint will typically lower the latency of connections and is recommended for such scenarios\.

**Note**  
In cases where API clients are geographically dispersed, it may still make sense to use a regional API endpoint, together with your own Amazon CloudFront distribution to ensure that API Gateway does not associate the API with service\-controlled CloudFront distributions\. For more information about this use case, see [How do I set up API Gateway with my own CloudFront distribution?](https://aws.amazon.com/premiumsupport/knowledge-center/api-gateway-cloudfront-distribution/)\.

 To create a regional API, you follow the steps in [creating an edge\-optimized API](create-api-resources-methods.md), but must explicitly set `REGIONAL` type as the only option of the API's [endpointConfiguration](https://docs.aws.amazon.com/apigateway/api-reference/resource/rest-api/#endpointConfiguration)\. 

 In the following, we show how to create a regional API using the API Gateway console, AWS CLI, and the AWS SDK for Javascript for Node\.js\. 

**Topics**
+ [Create a regional API using the API Gateway console](#create-regional-endpoint-api-using-console)
+ [Create a regional API using the AWS CLI](#create-regional-endpoint-api-using-aws-cli)
+ [Create a regional API using the AWS SDK for JavaScript](#create-regional-endpoint-api-using-nodejs-sdk)
+ [Test a regional API](#create-regional-endpoint-api-invoke-url)

## Create a regional API using the API Gateway console<a name="create-regional-endpoint-api-using-console"></a>

**To create a regional API using the API Gateway console**

1.  Sign in to the API Gateway console and choose **\+ Create API**\.

1.  Under **Create new API**, choose the **New API** option\. 

1.  Type a name \(for example, `Simple PetStore (Console, Regional)`\) for **API name**\. 

1.  Choose `Regional` for **Endpoint Type**\. 

1.  Choose **Create API**\.

From here on, you can proceed to set up API methods and their associated integrations as described in [creating an edge optimized API](create-api-using-console.md)\. 

## Create a regional API using the AWS CLI<a name="create-regional-endpoint-api-using-aws-cli"></a>

To create a regional API using the AWS CLI, call the `create-rest-api` command:

```
aws apigateway create-rest-api \
        --name 'Simple PetStore (AWS CLI, Regional)' \
        --description 'Simple regional PetStore API' \
        --region us-west-2 \
        --endpoint-configuration '{ "types": ["REGIONAL"] }'
```

A successful response returns a payload similar to the following:

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

 From here on, you can follow the same instructions given in [Set up an edge\-optimized API using AWS CLI commands](create-api-using-awscli.md) to set up methods and integrations for this API\. 

## Create a regional API using the AWS SDK for JavaScript<a name="create-regional-endpoint-api-using-nodejs-sdk"></a>

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

A successful response returns a payload similar to the following:

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

 After completing the preceding steps, you can follow the instructions in [Set up an edge\-optimized API using the AWS SDK for Node\.js](create-api-using-awssdk.md) to set up methods and integrations for this API\. 

## Test a regional API<a name="create-regional-endpoint-api-invoke-url"></a>

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