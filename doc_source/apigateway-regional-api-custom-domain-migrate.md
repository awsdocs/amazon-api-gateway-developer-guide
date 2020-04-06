# Migrating a custom domain name to a different API endpoint<a name="apigateway-regional-api-custom-domain-migrate"></a>

 You can migrate your custom domain name between edge\-optimized and Regional endpoints\. You first add the new endpoint configuration type to the existing `endpointConfiguration.types` list for the custom domain name\. Next, you set up a DNS record to point the custom domain name to the newly provisioned endpoint\. An optional last step is to remove the obsolete custom domain name configuration data\.

When planning the migration, remember that for an edge\-optimized API's custom domain name, the required certificate provided by ACM must be from the US East \(N\. Virginia\) Region \(`us-east-1`\)\. This certificate is distributed to all the geographic locations\. However, for a Regional API, the ACM certificate for the Regional domain name must be from the same Region hosting the API\. You can migrate an edge\-optimized custom domain name that is not in the `us-east-1` Region to a Regional custom domain name by first requesting a new ACM certificate from the Region that is local to the API\.

 It may take up to 60 seconds to complete a migration between an edge\-optimized custom domain name and a Regional custom domain name in API Gateway\. For the newly created endpoint to become ready to accept traffic, the migration time also depends on when you update your DNS records\.

**Topics**
+ [Migrate custom domain names using the AWS CLI](#apigateway-api-custom-domain-names-migrate-with-awscli)

## Migrate custom domain names using the AWS CLI<a name="apigateway-api-custom-domain-names-migrate-with-awscli"></a>

To use the AWS CLI to migrate a custom domain name from an edge\-optimized endpoint to a Regional endpoint or vice versa, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html) command to add the new endpoint type and, optionally, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html) command to remove the old endpoint type\.

**Topics**
+ [Migrate an edge\-optimized custom domain name to regional](#update-edge-optimized-domain-name-to-regional-cli)
+ [Migrate a regional custom domain name to edge\-optimized](#update-regional-domain-name-to-edge-optimized-cli)

### Migrate an edge\-optimized custom domain name to regional<a name="update-edge-optimized-domain-name-to-regional-cli"></a>

To migrate an edge\-optimized custom domain name to a Regional custom domain name, call the `update-domain-name` CLI command, as follows:

```
aws apigateway update-domain-name \
    --domain-name 'api.example.com' \
    --patch-operations  [ \ 
        { op:'add', path: '/endpointConfiguration/types',value: 'REGIONAL' }, \
        { op:'add', path: '/regionalCertificateArn', value: 'arn:aws:acm:us-west-2:123456789012:certificate/cd833b28-58d2-407e-83e9-dce3fd852149' } \
      ]
```

The Regional certificate must be of the same Region as the Regional API\. 

The success response has a `200 OK` status code and a body similar to the following:

```
{
    "certificateArn": "arn:aws:acm:us-east-1:123456789012:certificate/34a95aa1-77fa-427c-aa07-3a88bd9f3c0a",
    "certificateName": "edge-cert",
    "certificateUploadDate": "2017-10-16T23:22:57Z",
    "distributionDomainName": "d1frvgze7vy1bf.cloudfront.net",
    "domainName": "api.example.com",
    "endpointConfiguration": {
        "types": [
            "EDGE",
            "REGIONAL"
        ]
    },
    "regionalCertificateArn": "arn:aws:acm:us-west-2:123456789012:certificate/cd833b28-58d2-407e-83e9-dce3fd852149",
    "regionalDomainName": "d-fdisjghyn6.execute-api.us-west-2.amazonaws.com"
}
```

For the migrated Regional custom domain name, the resulting `regionalDomainName` property returns the Regional API hostname\. You must set up a DNS record to point the Regional custom domain name to this Regional hostname\. This enables the traffic that is bound to the custom domain name to be routed to the Regional host\. 

After the DNS record is set, you can remove the edge\-optimized custom domain name by calling the `[update\-domain\-name](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html)` command of AWS CLI:

```
aws apigateway update-domain-name \
    --domain-name api.example.com \
    --patch-operations [ \
            {op:'remove', path:'/endpointConfiguration/types', value:'EDGE'}, \
            {op:'remove', path:'certificateName'}, \
            {op:'remove', path:'certificateArn'} \
        ]
```

### Migrate a regional custom domain name to edge\-optimized<a name="update-regional-domain-name-to-edge-optimized-cli"></a>

To migrate a Regional custom domain name to an edge\-optimized custom domain name, call the `update-domain-name` command of the AWS CLI, as follows:

```
aws apigateway update-domain-name \
    --domain-name 'api.example.com' \
    --patch-operations  [ \
            { op:'add', path:'/endpointConfiguration/types',value: 'EDGE' }, \
            { op:'add', path:'/certificateName', value:'edge-cert'}, \
            { op:'add', path:'/certificateArn', value: 'arn:aws:acm:us-east-1:123456789012:certificate/34a95aa1-77fa-427c-aa07-3a88bd9f3c0a' } \
        ]
```

The edge\-optimized domain certificate must be created in the `us-east-1` Region\. 

The success response has a `200 OK` status code and a body similar to the following:

```
{
    "certificateArn": "arn:aws:acm:us-east-1:738575810317:certificate/34a95aa1-77fa-427c-aa07-3a88bd9f3c0a",
    "certificateName": "edge-cert",
    "certificateUploadDate": "2017-10-16T23:22:57Z",
    "distributionDomainName": "d1frvgze7vy1bf.cloudfront.net",
    "domainName": "api.example.com",
    "endpointConfiguration": {
        "types": [
            "EDGE",
            "REGIONAL"
        ]
    },
    "regionalCertificateArn": "arn:aws:acm:us-east-1:123456789012:certificate/3d881b54-851a-478a-a887-f6502760461d",
    "regionalDomainName": "d-cgkq2qwgzf.execute-api.us-east-1.amazonaws.com"
}
```

For the specified custom domain name, API Gateway returns the edge\-optimized API hostname as the `distributionDomainName` property value\. You must set a DNS record to point the edge\-optimized custom domain name to this distribution domain name\. This enables traffic that is bound to the edge\-optimized custom domain name to be routed to the edge\-optimized API hostname\. 

After the DNS record is set, you can remove the `REGION` endpoint type of the custom domain name:

```
aws apigateway update-domain-name \
    --domain-name api.example.com \
    --patch-operations [ \
        {op:'remove', path:'/endpointConfiguration/types', value:'REGIONAL'}, \
        {op:'remove', path:'regionalCertificateArn'} \
      ]
```

The result of this command is similar to the following output, with only edge\-optimized domain name configuration data:

```
{
    "certificateArn": "arn:aws:acm:us-east-1:738575810317:certificate/34a95aa1-77fa-427c-aa07-3a88bd9f3c0a",
    "certificateName": "edge-cert",
    "certificateUploadDate": "2017-10-16T23:22:57Z",
    "distributionDomainName": "d1frvgze7vy1bf.cloudfront.net",
    "domainName": "regional.haymuto.com",
    "endpointConfiguration": {
        "types": "EDGE"
    }
}
```