# Associate or Disassociate a VPC Endpoint with a Private REST API<a name="associate-private-api-with-vpc-endpoint"></a>

When you associate a VPC endpoint with your private API, API Gateway generates a new Route53 ALIAS DNS record which you can use to invoke your private APIs just as you do your edge\-optimized or regional APIs without overriding a `Host` header or passing an `x-apigw-api-id` header\.

The generated base URL is in the following format:

```
https://{rest-api-id}-{vpce-id}.execute-api.{region}.amazonaws.com/{stage}
```

Associating or disassociating a VPC endpoint with a private REST API requires you to update the API's configuration\. You can perform this change using the API Gateway console, the AWS CLI, or an AWS SDK for API Gateway\. The update operation may take few minutes to complete due to DNS propagation\. During this time, your API will be available, but DNS propagation for the newly generated DNS URLs may still be in progress\. You may try [creating a new deployment for your API](how-to-deploy-api.md), if even after several minutes your new URLs are not resolving in DNS\.

## Use the AWS CLI to Associate VPC Endpoint with a Private REST API<a name="associate-private-api-with-vpc-endpoint-associate"></a>

To associate VPC endpoints at the time of API creation, use the following command:

```
aws apigateway create-rest-api \
    --name Petstore \
    --endpoint-configuration '{ "types": ["PRIVATE"], "vpcEndpointIds" : ["vpce-0212a4ababd5b8c3e", "vpce-0393a628149c867ee"] }' \
    --region us-west-2
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
    --patch-operations "op='add',path='/endpointConfiguration/vpcEndpointIds',value='vpce-01d622316a7df47f9'"
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

## Use the AWS CLI to Disassociate VPC Endpoint from a Private REST API<a name="associate-private-api-with-vpc-endpoint-disassociate"></a>

To disassociate a VPC endpoint from a private API, use the following CLI command:

```
aws apigateway update-rest-api \
    --rest-api-id u67n3ov968 \
    --patch-operations "op='remove',path='/endpointConfiguration/vpcEndpointIds',value='vpce-0393a628149c867ee'"
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