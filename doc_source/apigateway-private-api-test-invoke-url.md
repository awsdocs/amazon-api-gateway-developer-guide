# How to Invoke a Private API<a name="apigateway-private-api-test-invoke-url"></a>

Once you have deployed a [private API](apigateway-private-apis.md), you can access it via private DNS \(if you've enabled private DNS naming\) and via public DNS\.

To get the DNS names for your private API, do the following:

1. Log in to the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left navigation pane, choose **Endpoints** and then choose your interface VPC endpoint for API Gateway\.

1. In the **Details** pane, you'll see 5 values in the **DNS names** field\. The first 3 are the public DNS names for your API\. The other 2 are the private DNS names for it\.

## Invoking Your Private API Using Private DNS Names<a name="w55aac13c16c28b9"></a>

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

## Invoking Your Private API Using Endpoint\-Specific Public DNS Hostnames<a name="w55aac13c16c28c11"></a>

You can access your private API using endpoint\-specific DNS hostnames\. These are public DNS hostnames containing the VPC endpoint ID or API ID for your private API\.

The base URL is in the following format:

```
https://{public-dns-hostname}.execute-api.{region}.vpce.amazonaws.com/{stage}
```

For example, assuming you set up the `GET /pets` and `GET /pets/{petId}` methods in this example, and assuming that your API's API ID was `0qzs2sy7bh`, its public DNS hostname was `vpce-0c1308d7312217cd7-01234567`, and your region was `us-west-2`, you could test your API via its VPCE ID by using the `Host` header in a cURL command, as in the following example:

```
curl -v https://vpce-0c1308d7312217cd7-01234567.execute-api.us-east-1.vpce.amazonaws.com/test/pets -H 'Host: 0qzs2sy7bh.execute-api.us-west-2.amazonaws.com'
```

Alternatively, you can access your private API via its API ID by using the `x-apigw-api-id` header in a cURL command in the following format:

```
curl -v https://{vpce-id}.execute-api.{region}.vpce.amazonaws.com/test -H'x-apigw-api-id:{api-id}'
```

## Accessing Your Private API Using AWS Direct Connect<a name="w55aac13c16c28c13"></a>

You can also use AWS Direct Connect to establish a dedicated private connection from an on\-premises network to Amazon VPC and access your private API endpoint over that connection by using public DNS names\.

You cannot use private DNS names to access your private API from an on\-premises network\.