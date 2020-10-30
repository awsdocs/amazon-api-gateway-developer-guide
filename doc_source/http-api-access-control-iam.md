# Using IAM authorization<a name="http-api-access-control-iam"></a>

You can enable IAM authorization for HTTP API routes\. When IAM authorization is enabled, clients must use [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) to sign their requests with AWS credentials\. API Gateway invokes your API route only if the client has `execute-api` permission for the route\.

IAM authorization for HTTP APIs is similar to that for [REST APIs](api-gateway-control-access-using-iam-policies-to-invoke-api.md)\.

For examples of IAM policies that grant clients the permission to invoke APIs, see [ Control access for invoking an API](api-gateway-control-access-using-iam-policies-to-invoke-api.md)\.

## Enable IAM authorization for a route<a name="http-api-access-control-iam-example"></a>

The following AWS CLI command enables IAM authorization for an HTTP API route\.

```
aws apigatewayv2 update-route \
    --api-id abc123 \
    --route-id abcdef \
    --authorization-type AWS_IAM
```