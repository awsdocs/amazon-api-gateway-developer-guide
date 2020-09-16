# Creating a private API in Amazon API Gateway<a name="apigateway-private-apis"></a>

Using Amazon API Gateway, you can create private REST APIs that can only be accessed from your virtual private cloud in Amazon VPC by using an [interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html)\. This is an endpoint network interface that you create in your VPC\. 

Using [ resource policies](#apigateway-private-api-set-up-resource-policy), you can allow or deny access to your API from selected VPCs and VPC endpoints, including across AWS accounts\. Each endpoint can be used to access multiple private APIs\. You can also use AWS Direct Connect to establish a connection from an on\-premises network to Amazon VPC and access your private API over that connection\. 

In all cases, traffic to your private API uses secure connections and does not leave the Amazon network—it is isolated from the public internet\. 

You can [access](apigateway-private-api-test-invoke-url.md) your private APIs through interface VPC endpoints for API Gateway as shown in the following diagram\. If you have private DNS enabled, you can use private or public DNS names to access your APIs\. If you have private DNS disabled, you can only use public DNS names\.

**Note**  
API Gateway private APIs only support TLS 1\.2\. Earlier TLS versions are not supported\.

![\[Accessing private API with private DNS enabled\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apigateway-private-api-accessing-api.png)

At a high level, the steps for creating a private API are as follows:

1. First, [create an interface VPC endpoint](#apigateway-private-api-create-interface-vpc-endpoint) for the API Gateway component service for API execution, known as `execute-api`, in your VPC\. 

1. Create and test your private API\.

   1. Use one of the following procedures to create your API:
      + [API Gateway console](#apigateway-private-api-create-using-console)
      + [API Gateway CLI](#apigateway-private-api-create-using-aws-cli)
      + [AWS SDK for JavaScript](#apigateway-private-api-create-using-nodejs-sdk)

   1. To grant access to your VPC endpoint, [create a resource policy and attach it to your API](#apigateway-private-api-set-up-resource-policy)\.

   1. [Test your API](apigateway-private-api-test-invoke-url.md)\.

**Note**  
The procedures below assume you already have a fully configured VPC\. For more information, and to get started with creating a VPC, see [Getting Started With Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/GetStarted.html) in the Amazon VPC User Guide\.

## Private API development considerations<a name="apigateway-private-api-design-considerations"></a>
+ You can convert an existing public API \(Regional or edge\-optimized\) to a private API, and you can convert a private API to a Regional API\. You cannot convert a private API to an edge\-optimized API\. For more information, see [Change a public or private API endpoint type in API Gateway](apigateway-api-migration.md)\.
+ To grant access to your private API to VPCs and VPC endpoints, you need to create a resource policy and attach it to the newly created \(or converted\) API\. Until you do so, all calls to the API will fail\. For more information, see [Set up a resource policy for a private API](#apigateway-private-api-set-up-resource-policy)\. 
+ [Custom domain names](how-to-custom-domains.md) are not supported for private APIs\.
+ You can use a single VPC endpoint to access multiple private APIs\.
+ VPC endpoints for private APIs are subject to the same limitations as other interface VPC endpoints\. For more information, see [Interface Endpoint Properties and Limitations](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-limitations) in the Amazon VPC User Guide\.
+ You can associate or disassociate a VPC endpoint to a REST API, which gives a Route 53 alias DNS record and simplifies invoking your private API\. For more information, see [Associate or Disassociate a VPC Endpoint with a Private REST API](#associate-private-api-with-vpc-endpoint)\.

**Topics**
+ [Private API development considerations](#apigateway-private-api-design-considerations)
+ [Create an interface VPC endpoint for API Gateway `execute-api`](#apigateway-private-api-create-interface-vpc-endpoint)
+ [Create a private API using the API Gateway console](#apigateway-private-api-create-using-console)
+ [Create a private API using the AWS CLI](#apigateway-private-api-create-using-aws-cli)
+ [Create a private API using the AWS SDK for JavaScript](#apigateway-private-api-create-using-nodejs-sdk)
+ [Set up a resource policy for a private API](#apigateway-private-api-set-up-resource-policy)
+ [Deploy a private API using the API Gateway console](#apigateway-private-api-deploy-using-console)
+ [Associate or disassociate a VPC endpoint with a private REST API](#associate-private-api-with-vpc-endpoint)

## Create an interface VPC endpoint for API Gateway `execute-api`<a name="apigateway-private-api-create-interface-vpc-endpoint"></a>

The API Gateway component service for API execution is called `execute-api`\. To access your private API once it's deployed, you need to create an interface VPC endpoint for it in your VPC\.

After you've created your VPC endpoint, you can use it to access multiple private APIs\. 

**To create an interface VPC endpoint for API Gateway `execute-api`**

1. Log in to the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, ensure that **AWS services** is selected\.

1. For **Service Name**, choose the API Gateway service endpoint, including the AWS Region that you want to connect to\. This is in the form `com.amazonaws.region.execute-api`—for example, `com.amazonaws.us-east-1.execute-api`\. 

   For **Type**, ensure that it indicates **Interface**\.

1. Complete the following information:
   + For **VPC**, choose the VPC that you want to create the endpoint in\.
   + For **Subnets**, choose the subnets \(Availability Zones\) in which to create the endpoint network interfaces\. 
**Note**  
Not all Availability Zones may be supported for all AWS services\.
   + For **Enable Private DNS Name**, leave the check box selected\. Private DNS is enabled by default\.

     When private DNS is enabled, you're able to access your API via private or public DNS\. \(This setting doesn't affect who can access your API, only which DNS addresses they can use\.\) However, you cannot access public APIs from a VPC by using an API Gateway VPC endpoint with private DNS enabled\. Note that these DNS settings don't affect the ability to call these public APIs from the VPC if you're using an edge\-optimized custom domain name to access the public API\. Using an edge\-optimized custom domain name to access your public API \(while using private DNS to access your private API\) is one way to access both public and private APIs from a VPC where the endpoint has been created with private DNS enabled\.
**Note**  
Leaving private DNS enabled is the recommended choice\. If you choose not to enable private DNS, you're only able to access your API via public DNS\.

     To use the private DNS option, the `enableDnsSupport` and `enableDnsHostnames` attributes of your VPC must be set to `true`\. For more information, see [DNS Support in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) and [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-updating) in the Amazon VPC User Guide\.
   + For **Security group**, select the security group to associate with the VPC endpoint network interfaces\.

     The security group you choose must be set to allow TCP Port 443 inbound HTTPS traffic from either an IP range in your VPC or another security group in your VPC\.

1. Choose **Create endpoint**\.

## Create a private API using the API Gateway console<a name="apigateway-private-api-create-using-console"></a>

**To create a private API using the API Gateway console**

1.  Sign in to the API Gateway console and choose **\+ Create API**\.

1.  Under **Create new API**, choose the **New API** option\.

1. Type a name \(for example, `Simple PetStore (Console, Private)`\) for **API name**\.

1. For **Endpoint Type**, choose `Private`\.

1. Choose **Create API**\.

From here on, you can set up API methods and their associated integrations as described in steps 1\-6 of [Create an API with HTTP custom integration](api-gateway-create-api-step-by-step.md#api-gateway-create-resource-and-methods)\. 

**Note**  
Until your API has a resource policy that grants access to your [VPC or VPC endpoint](#apigateway-private-api-create-interface-vpc-endpoint), all API calls will fail\. Before you test and deploy  your API, you need to create a resource policy and attach it to the API as described in [Set up a resource policy for a private API](#apigateway-private-api-set-up-resource-policy)\. 

## Create a private API using the AWS CLI<a name="apigateway-private-api-create-using-aws-cli"></a>

To create a private API using the AWS CLI, call the `create-rest-api` command:

```
aws apigateway create-rest-api \
        --name 'Simple PetStore (AWS CLI, Private)' \
        --description 'Simple private PetStore API' \
        --region us-west-2 \
        --endpoint-configuration '{ "types": ["PRIVATE"] }'
```

A successful call returns output similar to the following:

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

 From here on, you can follow the same instructions given in [Set up an edge\-optimized API using AWS CLI commands](create-api-using-awscli.md) to set up methods and integrations for this API\. 

When you are ready to test your API, be sure to create a resource policy and attach it to the API as described in [Set up a resource policy for a private API](#apigateway-private-api-set-up-resource-policy)\.

## Create a private API using the AWS SDK for JavaScript<a name="apigateway-private-api-create-using-nodejs-sdk"></a>

To create a private API by using the AWS SDK for JavaScript:

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

A successful call returns output similar to the following:

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

 After completing the preceding steps, you can follow the instructions in [Set up an edge\-optimized API using the AWS SDK for Node\.js](create-api-using-awssdk.md) to set up methods and integrations for this API\. 

When you are ready to test your API, be sure to create a resource policy and attach it to the API as described in [Set up a resource policy for a private API](#apigateway-private-api-set-up-resource-policy)\.

## Set up a resource policy for a private API<a name="apigateway-private-api-set-up-resource-policy"></a>

Before your private API can be accessed, you need to create a resource policy and attach it to the API. This grants access to the API from your VPCs and VPC endpoints or from VPCs and VPC endpoints in other AWS accounts that you explicitly grant access\.

To do this, follow the instructions in [Create and attach an API Gateway resource policy to an API](apigateway-resource-policies-create-attach.md)\. In step 4, choose the **Source VPC Whitelist** example\. Replace `{{vpceID}}` \(including the curly braces\) with your VPC endpoint ID, and then choose **Save** to save your resource policy\.

You should also consider attaching an endpoint policy to the VPC endpoint to specify the access that's being granted\. For more information, see [Use VPC endpoint policies for private APIs in API Gateway](apigateway-vpc-endpoint-policies.md)\.

## Deploy a private API using the API Gateway console<a name="apigateway-private-api-deploy-using-console"></a>

To deploy your private API, do the following in the API Gateway console:

1. In the left navigation pane, select the API and then choose **Deploy API** from the **Actions** drop\-down menu\.

1.  In the **Deploy API** dialog, choose a stage \(or `[New Stage]` for the API's first deployment\)\. Enter a name \(for example, "test", "prod", or "dev"\) in the **Stage name** input field\. Optionally, provide a description in **Stage description** and/or **Deployment description**\. Then choose **Deploy**\. 

## Associate or disassociate a VPC endpoint with a private REST API<a name="associate-private-api-with-vpc-endpoint"></a>

When you associate a VPC endpoint with your private API, API Gateway generates a new Route 53 ALIAS DNS record\. You can use this record to invoke your private APIs just as you do your edge\-optimized or Regional APIs without overriding a `Host` header or passing an `x-apigw-api-id` header\.

The generated base URL is in the following format:

```
https://{rest-api-id}-{vpce-id}.execute-api.{region}.amazonaws.com/{stage}
```

Associating or disassociating a VPC endpoint with a private REST API requires you to update the API's configuration\. You can perform this change using the API Gateway console, the AWS CLI, or an AWS SDK for API Gateway\. The update operation may take few minutes to complete due to DNS propagation\. During this time, your API is available, but DNS propagation for the newly generated DNS URLs may still be in progress\. You may try [creating a new deployment for your API](how-to-deploy-api.md), if even after several minutes your new URLs are not resolving in DNS\.

### Use the AWS CLI to associate VPC endpoint with a private REST API<a name="associate-private-api-with-vpc-endpoint-associate"></a>

To associate VPC endpoints at the time of API creation, use the following command:

```
aws apigateway create-rest-api \
    --name Petstore \
    --endpoint-configuration '{ "types": ["PRIVATE"], "vpcEndpointIds" : ["vpce-0212a4ababd5b8c3e", "vpce-0393a628149c867ee"] }' \
    --region us-west-2
```

A successful call returns output similar to the following:

```
{
    "apiKeySource": "HEADER",
    "endpointConfiguration": {
        "types": [
            "PRIVATE"
        ],
        "vpcEndpointIds": [
            "vpce-0212a4ababd5b8c3e",
            "vpce-0393a628149c867ee"
        ]
    },
    "id": "u67n3ov968",
    "createdDate": 1565718256,
    "name": "Petstore"
}
```

To associate VPC endpoints to an already created private API, use the following CLI command:

```
aws apigateway update-rest-api \
    --rest-api-id u67n3ov968 \
    --patch-operations "op='add',path='/endpointConfiguration/vpcEndpointIds',value='vpce-01d622316a7df47f9'" \
    --region us-west-2
```

The output will look like the following:

```
{
    "name": "Petstore",
    "apiKeySource": "1565718256",
    "tags": {},
    "createdDate": 1565718256,
    "endpointConfiguration": {
        "vpcEndpointIds": [
            "vpce-0212a4ababd5b8c3e",
            "vpce-0393a628149c867ee",
            "vpce-01d622316a7df47f9"
        ],
        "types": [
            "PRIVATE"
        ]
    },
    "id": "u67n3ov968"
}
```

### Use the AWS CLI to disassociate VPC endpoint from a private REST API<a name="associate-private-api-with-vpc-endpoint-disassociate"></a>

To disassociate a VPC endpoint from a private API, use the following CLI command:

```
aws apigateway update-rest-api \
    --rest-api-id u67n3ov968 \
    --patch-operations "op='remove',path='/endpointConfiguration/vpcEndpointIds',value='vpce-0393a628149c867ee'" \
    --region us-west-2
```

The output will look like the following:

```
{
    "name": "Petstore",
    "apiKeySource": "1565718256",
    "tags": {},
    "createdDate": 1565718256,
    "endpointConfiguration": {
        "vpcEndpointIds": [
            "vpce-0212a4ababd5b8c3e",
            "vpce-01d622316a7df47f9"
        ],
        "types": [
            "PRIVATE"
        ]
    },
    "id": "u67n3ov968"
}
```
