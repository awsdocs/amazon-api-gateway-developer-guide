# Create a Private API in Amazon API Gateway<a name="apigateway-private-apis"></a>

Using Amazon API Gateway, you can create private REST APIs that can only be accessed from your Amazon Virtual Private Cloud \(VPC\) using an [interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html), which is an endpoint network interface \(ENI\) that you create in your VPC\. Using [ resource policies](#apigateway-private-api-set-up-resource-policy), you can allow or deny access to your API from selected VPCs and VPC endpoints, including across AWS accounts\. Each endpoint can be used to access multiple private APIs\. You can also use AWS Direct Connect to establish a connection from an on\-premises network to Amazon VPC and access your private API over that connection\. In all cases, traffic to your private API uses secure connections and does not leave the Amazon network; it is isolated from the public internet\. 

You can [access](#apigateway-private-api-test-invoke-url) your private APIs through interface VPC endpoints for API Gateway as shown in the following diagram\. If you have private DNS enabled, you can use private or public DNS names to access your APIs\. If you have private DNS disabled, you can only use public DNS names\.

![\[Accessing Private API with Private DNS Enabled\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-private-api-accessing-api.png)

At a high level, the steps for creating a private API are as follows:

1. First, [create an interface VPC endpoint](#apigateway-private-api-create-interface-vpc-endpoint) for the API Gateway component service for API execution, known as `execute-api`, in your VPC\. 

1. Create and test your private API\.

   1. Use one of the following procedures to create your API:
      + [API Gateway console](#apigateway-private-api-create-using-console)
      + [API Gateway CLI](#apigateway-private-api-create-using-aws-cli)
      + [AWS SDK for JavaScript](#apigateway-private-api-create-using-nodejs-sdk)
      + [API Gateway REST API](#apigateway-private-api-create-using-rest-api)

   1. To grant access to your VPC endpoint, [create a resource policy and attach it to your API](#apigateway-private-api-set-up-resource-policy)\.

   1. [Test your API](#apigateway-private-api-test-invoke-url)\.

**Note**  
The procedures below assume you already have a fully configured VPC\. For more information, and to get started with creating a VPC, see [Getting Started With Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/GetStarted.html) in the Amazon VPC User Guide\.

## Create an Interface VPC Endpoint for API Gateway `execute-api`<a name="apigateway-private-api-create-interface-vpc-endpoint"></a>

The API Gateway component service for API execution is called `execute-api`\. To access your private API once it's deployed, you'll need to create an interface VPC endpoint for it in your VPC\.

Once you've created your VPC endpoint, you can use it to access multiple private APIs\. 

**To create an interface VPC endpoint for API Gateway `execute-api`**

1. Log in to to the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, ensure that **AWS services** is selected\.

1. For **Service Name**, choose the API Gateway service endpoint, including the region to which to connect\. This will be in the form `com.amazonaws.region.execute-api`, for example `com.amazonaws.us-east-1.execute-api`\. 

   For **Type**, ensure that it indicates **Interface**\.

1. Complete the following information:
   + For **VPC**, choose the VPC in which to you want create the endpoint\.
   + For **Subnets**, choose the subnets \(Availability Zones\) in which to create the endpoint network interfaces\. 
**Note**  
Not all Availability Zones may be supported for all AWS services\.
   + For **Enable Private DNS Name**, you can optionally select the check box to enable private DNS for the interface endpoint\. 

     If you choose to enable private DNS, you'll be able to to access your API via private or public DNS\. This is the recommended choice\.

     If you choose not to enable private DNS, you'll only be able to access your API via public DNS\.

     This setting does not affect who can access your API, only how DNS addresses they can use\.
**Note**  
To use the private DNS option, the `enableDnsSupport` and `enableDnsHostnames` attributes of your VPC must be set to `true`\. For more information, see [DNS Support in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) and [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-updating) in the Amazon VPC User Guide\.
   + For **Security group**, select the security group to associate with the VPC endpoint network interfaces\.

     The security group you choose must be set to allow TCP Port 443 inbound HTTPS traffic from either an IP range in your VPC or another security group in your VPC\.

1. Choose **Create endpoint**\.

## Create a Private API Using the API Gateway Console<a name="apigateway-private-api-create-using-console"></a>

**To create a private API using the API Gateway console**

1.  Sign in to the API Gateway console and choose **\+ Create API**\.

1.  Under **Create new API**, choose the **New API** option\.

1. Type a name \(for example, `Simple PetStore (Console, Private)`\) for **API name**\.

1. For **Endpoint Type**, choose `Private`\.

1. Choose **Create API**\.

From here on, you can set up API methods and their associated integrations as described in steps 1\-6 of [Create the API with HTTP Custom Integration](api-gateway-create-api-step-by-step.md#api-gateway-create-resource-and-methods)\. 

**Note**  
Until your API has a resource policy that grants access to your [VPC or VPC endpoint](#apigateway-private-api-create-interface-vpc-endpoint), all API calls will fail\. Before you test and deploy  your API, you'll need to create a resource policy and attach it to the API as described in [Set Up a Resource Policy for a Private API](#apigateway-private-api-set-up-resource-policy)\. 

## Create a Private API Using the AWS CLI<a name="apigateway-private-api-create-using-aws-cli"></a>

To create a private API using the AWS CLI, call the `create-rest-api` command:

```
aws apigateway create-rest-api \
        --name 'Simple PetStore (AWS CLI, Private)' \
        --description 'Simple private PetStore API' \
        --region us-west-2 \
        --endpoint-configuration '{ types: ["PRIVATE"] }'
```

The successful response returns an output similar to the following:

```
{
    "createdDate": "2017-10-13T18:41:39Z",
    "description": "Simple private PetStore API",
    "endpointConfiguration": {
        "types": "PRIVATE"
    },
    "id": "0qzs2sy7bh",
    "name": "Simple PetStore (AWS CLI, Private)"
}
```

 From here on, you can follow the same instructions given in [Set up an Edge\-Optimized API Using AWS CLI Commands](create-api-using-awscli.md) to set up methods and integrations for this API\. 

When you are ready to test your API, be sure to create a resource policy and attach it to the API as described in [Set Up a Resource Policy for a Private API](#apigateway-private-api-set-up-resource-policy)\.

## Create a Private API Using the AWS SDK for JavaScript<a name="apigateway-private-api-create-using-nodejs-sdk"></a>

To create a private API, using the AWS SDK for JavaScript:

```
apig.createRestApi({
	name: "Simple PetStore (node.js SDK, private)",
	endpointConfiguration: {
		types: ['PRIVATE']
	},
	description: "Demo private API created using the AWS SDK for node.js",
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
    "description": "Demo private API created using the AWS SDK for node.js",
    "endpointConfiguration": {
        "types": "PRIVATE"
    },
    "id": "0qzs2sy7bh",
    "name": "Simple PetStore (node.js SDK, private)"
}
```

 After completing the preceding steps, you can follow the instructions in [ Set up an Edge\-Optimized API Using the AWS SDK for Node\.js ](create-api-using-awssdk.md) to set up methods and integrations for this API\. 

When you are ready to test your API, be sure to create a resource policy and attach it to the API as described in [Set Up a Resource Policy for a Private API](#apigateway-private-api-set-up-resource-policy)\.

## Create a Private API Using the API Gateway REST API<a name="apigateway-private-api-create-using-rest-api"></a>

To create a private API using the API Gateway REST API, submit a POST request as follows:

```
POST /restapis HTTP/1.1
Host: apigateway.us-west-2.amazonaws.com
Content-Type: application/x-amz-json-1.0
X-Amz-Date: 20170511T214723Z
Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c

{
    "name": "Simple PetStore (REST API, Private)",
    "description": "A sample API Gateway API created using the REST API.",
    "endpointConfiguration" : {
       "types" : ["PRIVATE"]
    }
}
```

The successful response has the status code of `201 Created` and a body similar to the following output:

```
{
    "createdDate": "2017-10-13T18:41:39Z",
    "description": "A sample API Gateway API created using the REST API.",
    "endpointConfiguration": {
        "types": "PRIVATE"
    },
    "id": "0qzs2sy7bh",
    "name": "Simple PetStore (REST API, Private)"
}
```

 After completing the preceding steps, you can follow the instructions in [Set up an Edge\-Optimized API Using the API Gateway REST API](create-api-using-restapi.md) to set up methods and integrations for this API\.

When you are ready to test your API, be sure to create a resource policy and attach it to the API as described in [Set Up a Resource Policy for a Private API](#apigateway-private-api-set-up-resource-policy)\.

## Set Up a Resource Policy for a Private API<a name="apigateway-private-api-set-up-resource-policy"></a>

Before your private API can be accessed, you need to create a resource policy and attach it to the API This will grant access to the API from your VPCs and VPC endpoints or from VPCs and VPC endpoints in other AWS accounts that you explicitly grant access\.

To do this, follow the instructions in [Create and Attach an API Gateway Resource Policy to an API](apigateway-resource-policies-create-attach.md)\. In step 4, choose the **Source VPC Whitelist** example\. Replace `{{vpceID}}` \(including the curly braces\) with your VPC endpoint ID, choose **Save** to save your resource policy\.

## Deploy a Private API Using the API Gateway Console<a name="apigateway-private-api-deploy-using-console"></a>

To deploy your private API, do the following in the API Gateway console:

1. In the left navigation pane, select the API and then choose **Deploy API** from the **Actions** drop\-down menu\.

1.  In the **Deploy API** dialog, choose a stage \(or `[New Stage]` for the API's first deployment\); enter a name \(e\.g\., "test", "prod", "dev", etc\.\) in the **Stage name** input field; optionally, provide a description in **Stage description** and/or **Deployment description**; and then choose **Deploy**\. 

## How to Invoke a Private API<a name="apigateway-private-api-test-invoke-url"></a>

Once you have deployed your private API, you can access it via private DNS \(if you've enabled private DNS naming\) and via public DNS\.

To get the DNS names for your private API, do the following:

1. Log in to to the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left\-hand navigation pane, choose **Endpoints** and then choose your interface VPC endpoint for API Gateway\.

1. In the **Details** pane, you'll see 4 values in the **DNS names** field\. The first 2 are the private DNS names for your API\. The other 2 are the public DNS names for it\. 

### Invoking Your Private API Using Private DNS Names<a name="w4aac12c15c23c29b9"></a>

If you've enabled private DNS, you can access your private API using the private DNS names as follows:

```
{restapi-id}.execute-api.{region}.amazonaws.com
```

The base URL to invoke the API is in the following format:

```
https://{restapi-id}.execute-api.{region}.amazonaws.com/{stage}
```

For example, assuming you set up the `GET /pets` and `GET /pets/{petId}` methods in this example, and assuming that your rest API ID was `0qzs2sy7bh` and your region was `us-west-2`, you could test your API by typing the following URLs in a browser:

```
https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets
```

and 

```
https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets/1
```

Alternatively, you could use the following cURL commands:

```
curl -X GET https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets 
```

and 

```
curl -X GET https://0qzs2sy7bh.execute-api.us-west-2.amazonaws.com/test/pets/2
```

### Invoking Your API Using Public DNS Names<a name="w4aac12c15c23c29c11"></a>

You can access your private API using public DNS names\.

The base URL is in the following format:

```
https://{restapi-id}.execute-api.{region}.amazonaws.com/{stage}
```

For example, assuming you set up the `GET /pets` and `GET /pets/{petId}` methods in this example, and assuming that your API's API ID was `0qzs2sy7bh` and its public DNS name was `vpce-0c1308d7312217cd7-01234567.execute-api.us-west-1.vpce.amazonaws.com` and your region was `us-west-2`, you could test your API by using the following cURL command:

```
curl -v https://vpce-0c1308d7312217cd7-01234567.execute-api.us-west-1.vpce.amazonaws.com/test/pets -H 'Host: 0qzs2sy7bh.execute-api.us-west-2.amazonaws.com'
```

### Accessing Your API Using AWS Direct Connect<a name="w4aac12c15c23c29c13"></a>

You can also use AWS Direct Connect to establish a dedicated private connection from an on\-premises network to Amazon VPC and access your private API endpoint over that connection by using public DNS names\. 

You cannot use private DNS names to access your private API from an on\-premises network\.

## Private API Development Considerations<a name="apigateway-private-api-design-considerations"></a>
+ You can convert an existing public API \(regional or edge\-optimized\) to a private API, and you can convert a private API to a regional API\. You cannot convert a private API to an edge\-optimized API\. For more information, see [Change a Public or Private API Endpoint Type in API Gateway](apigateway-api-migration.md)\.
+ To grant access to your private API to VPCs and VPC endpoints, you'll need to create a resource policy and attach it to the newly created \(or converted\) API\. Until you do so, all calls to the API will fail\. For more information, see [Set Up a Resource Policy for a Private API](#apigateway-private-api-set-up-resource-policy)\. 
+ [Custom domain names](how-to-custom-domains.md) are not supported for private APIs\.
+ You can use a single VPC endpoint to access multiple private APIs\.
+ VPC endpoints for private APIs are subject to the same limitations as other interface VPC endpoints\. For more information, see [Interface Endpoint Properties and Limitations](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-limitations) in the Amazon VPC User Guide\.