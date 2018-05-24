# Migrate a Custom Domain Name to a Different API Endpoint<a name="apigateway-regional-api-custom-domain-migrate"></a>

 You can migrate your custom domain name between edge\-optimized and regional endpoints\. You first add the new endpoint configuration type to the existing `endpointConfiguration.types` list for the custom domain name\. Next, you set up a DNS record to point the custom domain name to the newly provisioned endpoint\. An optional last step is to remove the obsolete custom domain name configuration data\.

When planning the migration, remember that for an edge\-optimized API's custom domain name, the required certificate provided by ACM must be from the US East \(N\. Virginia\) Region \(`us-east-1`\)\. This certificate is distributed to all the geographic locations\. However, for a regional API, the ACM Certificate for the regional domain name must be from the same region hosting the API\. You can migrate an edge\-optimized custom domain name that is not in the `us-east-1` region to a regional custom domain name by first requesting a new ACM Certificate from the region that is local to the API\.

 It may take up to 60 seconds to complete a migration between an edge\-optimized custom domain name and a regional custom domain name in API Gateway\. For the newly created endpoint to become ready to accept traffic, the migration time also depends on when you update your DNS records\.

**Topics**
+ [Migrate Regional and Edge\-Optimized Domain Names Using the API Gateway Console](#migrate-custom-domain-name-using-console)
+ [Update Custom Domain Names Using the AWS CLI](#apigateway-api-custom-domain-names-migrate-with-awscli)
+ [Update Custom Domain Names Using the API Gateway REST API](#apigateway-api-custom-domain-names-migrate-with-rest-api)

## Migrate Regional and Edge\-Optimized Domain Names Using the API Gateway Console<a name="migrate-custom-domain-name-using-console"></a>

 To use the API Gateway console to migrate a regional custom domain name to an edge\-optimized custom domain name and vice versa, use the following procedure\. 

**To migrate a regional or edge\-optimized custom domain name using the API Gateway console**

1.  Sign in to the API Gateway console and choose **Custom Domain Names** in the primary navigation pane\.

1.  Choose an existing domain name from **Custom Domain Names**, and then choose **Edit**\.

1. Depending on the existing endpoint type, do the following:

   1.  For an edge\-optimized domain name, choose **Add Regional Configuration**\. 

   1.  For a regional domain name, choose **Add Edge Configuration**\. 

1. Choose a certificate from the drop\-down list\. 

1.  Choose **Save**\. 

1.  Choose **Proceed** to confirm adding the new endpoint\.

1.  Update the DNS records to point the new domain name to the newly provisioned target domain name\.

## Update Custom Domain Names Using the AWS CLI<a name="apigateway-api-custom-domain-names-migrate-with-awscli"></a>

To use the AWS CLI to update a custom domain name from an edge\-optimized endpoint to a regional endpoint or vice versa, call the [http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html](http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html) command to add the new endpoint type and, optionally, call the [http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html](http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html) command to remove the old endpoint type\.

**Topics**
+ [Update an Edge\-Optimized Custom Domain Name to Regional](#update-edge-optimized-domain-name-to-regional-cli)
+ [Update a Regional Custom Domain Name to Edge\-Optimized](#update-regional-domain-name-to-edge-optimized-cli)

### Update an Edge\-Optimized Custom Domain Name to Regional<a name="update-edge-optimized-domain-name-to-regional-cli"></a>

To migrate an edge\-optimized custom domain name to a regional custom domain name, call the `update-domain-name` command of AWS CLI, as follows:

```
aws apigateway update-domain-name \
    --domain-name 'api.example.com' \
    --patch-operations  [ \ 
        { op:'add', path: '/endpointConfiguration/types',value: 'REGIONAL' }, \
        { op:'add', path: '/regionalCertificateArn', value: 'arn:aws:acm:us-west-2:123456789012:certificate/cd833b28-58d2-407e-83e9-dce3fd852149' } \
      ]
```

The regional certificate must be of the same region as the regional API\. 

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

For the updated regional custom domain name, the resulting `regionalDomainName` property returns the regional API host name\. You must set up a DNS record to point the regional custom domain name to this regional host name\. This enables the traffic that is bound to the custom domain name to be routed to the regional host\. 

After the DNS record is set, you can remove the edge\-optimized custom domain name by calling the `[update\-domain\-name](http://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html)` command of AWS CLI:

```
aws apigateway update-domain-name \
    --domain-name api.example.com \
    --patch-operations [ \
            {op:'remove', path:'/endpointConfiguration/types', value:'EDGE'}, \
            {op:'remove', path:'certificateName'}, \
            {op:'remove', path:'certificateArn'} \
        ]
```

### Update a Regional Custom Domain Name to Edge\-Optimized<a name="update-regional-domain-name-to-edge-optimized-cli"></a>

To migrate a regional custom domain name to an edge\-optimized custom domain name, call the `update-domain-name` command of the AWS CLI, as follows:

```
aws apigateway update-domain-name \
    --domain-name 'api.example.com' \
    --patch-operations  [ \
            { op:'add', path:'/endpointConfiguration/types',value: 'EDGE' }, \
            { op:'add', path:'/certificateName', value:'edge-cert'}, \
            { op:'add', path:'/certificateArn', value: 'arn:aws:acm:us-east-1:123456789012:certificate/34a95aa1-77fa-427c-aa07-3a88bd9f3c0a' } \
        ]
```

The edge\-optimized domain certificate must be created in the `us-east-1` region\. 

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

For the specified custom domain name, API Gateway returns the edge\-optimized API host name as the `distributionDomainName` property value\. You must set a DNS record to point the edge\-optimized custom domain name to this distribution domain name\. This enables traffic that is bound to the edge\-optimized custom domain name to be routed to the edge\-optimized API host name\. 

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

## Update Custom Domain Names Using the API Gateway REST API<a name="apigateway-api-custom-domain-names-migrate-with-rest-api"></a>

To use the API Gateway REST API to update an edge\-optimized custom domain name to a regional one, or from a regional custom domain name to an edge\-optimized one, use the [domainname:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update) link\-relation and, optionally, the [domainname:delete](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-delete) link\-relation\. 

**Topics**
+ [Update an Edge\-Optimized Custom Domain Name to Regional](#update-edge-domain-name-to-regional)
+ [Update a Region Domain Name to Edge\-Optimized](#update-regional-domain-name-to-edge-optimized)

### Update an Edge\-Optimized Custom Domain Name to Regional<a name="update-edge-domain-name-to-regional"></a>

To update an edge\-optimized custom domain name \(`api.example.com`\) to a regional custom domain name, call [http://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/) of the API Gateway REST API:

```
PATCH /domainnames/api.example.com HTTP/1.1
Host: apigateway.us-west-2.amazonaws.com
Content-Type: application/x-amz-json-1.0
X-Amz-Date: 20170511T214723Z
Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c

{
    "patchOperations": [
      {
        "op" : "add",
        "path" : "/endpointConfiguration/types",
        "value" : "REGIONAL"
      },
      {
        "op" : "add",
        "path" : "/regionalCertificateArn",
        "value" : "arn:aws:acm:us-west-2:123456789012:certificate/c19332f1-3be7-457f-a245-e03a423084e7"
      }
    ]
}
```

The regional certificate must be of the same region as the regional API\. 

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
    "regionalCertificateArn": "arn:aws:acm:us-west-2:123456789012:certificate/c19332f1-3be7-457f-a245-e03a423084e7",
    "regionalDomainName": "d-gdisjchyn5.execute-api.us-west-2.amazonaws.com"
}
```

For the regional custom domain name, the returned `regionalDomainName` property value is the regional API host name\. You must set up a DNS record to point the regional custom domain name to this regional API host name\. This enables traffic that is bound to the regional custom domain name to be routed to the regional API host\. 

You can then remove the edge\-optimized API custom domain name:

```
PATCH /domainnames/{domain-name} HTTP/1.1
Host: apigateway.us-west-2.amazonaws.com
Content-Type: application/x-amz-json-1.0
X-Amz-Date: 20170511T214723Z
Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c

{
    "patchOperations": [
      {
        "op" : "remove",
        "path" : "/endpointConfiguration/types"
      },
      {
        "op" : "remove",
        "path" : "/certificateName"
      },
      {
        "op" : "remove",
        "path" : "/certificateArn"
      }
    ]
}
```

### Update a Region Domain Name to Edge\-Optimized<a name="update-regional-domain-name-to-edge-optimized"></a>

To migrate a regional custom domain name to an edge\-optimized custom domain name, call `domainname:update` from the API Gateway REST API, as follows:

```
PATCH /domainnames/{domain-name} HTTP/1.1
Host: apigateway.us-west-2.amazonaws.com
Content-Type: application/x-amz-json-1.0
X-Amz-Date: 20170511T214723Z
Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c

{
  "patchOperations" : [ {
      "op" : "add",
      "path" : "/endpointConfiguration/types",
      "value" : "EDGE"
  }, {
      "op": "add",
      "path": "/certificateName",
      "value": "edge-cert"
  },{
      "op": "add",
      "path": "/certificateArn",
      "value": "arn:aws:acm:us-east-1:123456789012:certificate/34a95aa1-77fa-427c-aa07-3a88bd9f3c0a"
  } ]
}
```

For an edge\-optimized API custom domain name, the ACM Certificate must be from the `us-east-1` region\. 

The successful response has a `200 OK` status code and a body similar to the following:

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

For the specified custom domain name, API Gateway returns the domain name of an Amazon CloudFront distribution\. You must set a DNS record to point the custom domain name to this distribution domain name, so that traffic to the custom domain name is routed to the named CloudFront distribution\. 

You can then remove the regional API custom domain name:

```
PATCH /domainnames/{domain-name} HTTP/1.1
Host: apigateway.us-west-2.amazonaws.com
Content-Type: application/x-amz-json-1.0
X-Amz-Date: 20170511T214723Z
Authorization: AWS4-HMAC-SHA256 Credential={ACCESS-KEY-ID}/20170511/us-west-2/apigateway/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=d0abd98a2a06199531c2916b162ede9f63a247032cdc8e4d077216446d13103c

{
    "patchOperations": [
      {
        "op" : "remove",
        "path" : "/endpointConfiguration/types"
      },
      {
        "op" : "remove",
        "path" : "/regionalCertificateArn"
      }
    ]
}
```

The successful response to the preceding request has a `200 OK` status code and a body similar to the following:

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