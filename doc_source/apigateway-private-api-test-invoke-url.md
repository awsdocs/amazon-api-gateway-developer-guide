# How to invoke a private API<a name="apigateway-private-api-test-invoke-url"></a>

Private APIs are accessible only from within your VPCs, and the [resource policies](apigateway-private-apis.md#apigateway-private-api-set-up-resource-policy) must allow access from the VPCs and VPC endpoints you have configured\. How you access your private API will depend upon whether or not you have enabled private DNS on the VPC endpoint\. For example, while accessing private API from on\-premises network via AWS Direct Connect, you will have private DNS enabled on the VPC endpoint\. In such a case, follow the steps outlined in [Invoking Your Private API Using Endpoint\-Specific Public DNS Hostnames](#apigateway-private-api-public-dns)\.

Once you have deployed a [private API](apigateway-private-apis.md), you can access it via private DNS \(if you've enabled private DNS naming\) and via public DNS\.

To get the DNS names for your private API, do the following:

1. Log in to the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left navigation pane, choose **Endpoints** and then choose your interface VPC endpoint for API Gateway\.

1. In the **Details** pane, you'll see 5 values in the **DNS names** field\. The first 3 are the public DNS names for your API\. The other 2 are the private DNS names for it\.

## Invoking your private API using private DNS names<a name="w108aac16b9c33c26c11"></a>

**Warning**  
When you select the Enable Private DNS Name option while creating an interface VPC endpoint for API Gateway, the VPC where the VPC Endpoint is present won't be able to access public \(edge\-optimized and regional\) APIs\. For more information, see [Why can't I connect to my public API from an API Gateway VPC endpoint?](https://aws.amazon.com/premiumsupport/knowledge-center/api-gateway-vpc-connections/)\.

If you've enabled private DNS, you can access your private API using the private DNS names as follows:

```
{restapi-id}.execute-api.{region}.amazonaws.com
```

The base URL to invoke the API is in the following format:

```
https://{restapi-id}.execute-api.{region}.amazonaws.com/{stage}
```

For example, assuming you set up the `GET /pets` and `GET /pets/{petId}` methods in this example, and assuming that your rest API ID was `01234567ab` and your region was `us-west-2`, you could test your API by typing the following URLs in a browser:

```
https://01234567ab.execute-api.us-west-2.amazonaws.com/test/pets
```

and 

```
https://01234567ab.execute-api.us-west-2.amazonaws.com/test/pets/1
```

Alternatively, you could use the following cURL commands:

```
curl -X GET https://01234567ab.execute-api.us-west-2.amazonaws.com/test/pets 
```

and 

```
curl -X GET https://01234567ab.execute-api.us-west-2.amazonaws.com/test/pets/2
```

## Accessing your private API using AWS Direct Connect<a name="w108aac16b9c33c26c13"></a>

You can also use AWS Direct Connect to establish a dedicated private connection from an on\-premises network to Amazon VPC and access your private API endpoint over that connection by using public DNS names\.

You cannot use private DNS names to access your private API from an on\-premises network\.

## Accessing your private API using a Route53 alias<a name="apigateway-private-api-route53-alias"></a>

You can associate or disassociate a VPC endpoint with your private API by using the procedure outlined in [Associate or Disassociate a VPC Endpoint with a Private REST API](apigateway-private-apis.md#associate-private-api-with-vpc-endpoint)\.

Once you associate your private API's REST API ID with the VPC endpoints you'll be calling your REST API from, you can use the following format base URL to invoke the API using a Route53 alias\.

The generated base URL is in the following format:

```
https://{rest-api-id}-{vpce-id}.execute-api.{region}.amazonaws.com/{stage}
```

For example, assuming you set up the GET /pets and GET /pets/*\{petId\} * methods in this example, and assuming that your API's API ID was 01234567ab, VPC Endpoint ID was vpce\-01234567abcdef012, and your region was us\-west\-2, you can invoke your API as:

```
curl -v https://01234567ab-vpce-01234567abcdef012.execute-api.us-west-2.amazonaws.com/test/pets/
```

## Invoking your private API using endpoint\-specific public DNS hostnames<a name="apigateway-private-api-public-dns"></a>

You can access your private API using endpoint\-specific DNS hostnames\. These are public DNS hostnames containing the VPC endpoint ID or API ID for your private API\.

The generated base URL is in the following format:

```
https://{public-dns-hostname}.execute-api.{region}.vpce.amazonaws.com/{stage}
```

For example, assuming you set up the `GET /pets` and `GET /pets/{petId}` methods in this example, and assuming that your API's API ID was `01234567ab`, its public DNS hostname was `vpce-01234567abcdef012-01234567`, and your region was `us-west-2`, you could test your API via its VPCE ID by using the `Host` header in a cURL command, as in the following example:

```
curl -v https://vpce-01234567abcdef012-01234567.execute-api.us-east-1.vpce.amazonaws.com/test/pets -H 'Host: 01234567ab.execute-api.us-west-2.amazonaws.com'
```

Alternatively, you can access your private API via its API ID by using the `x-apigw-api-id` header in a cURL command in the following format:

```
curl -v https://{public-dns-hostname}.execute-api.{region}.vpce.amazonaws.com/test -H'x-apigw-api-id:{api-id}'
```