# Data encryption in Amazon API Gateway<a name="data-protection-encryption"></a>

Data protection refers to protecting data while in transit \(as it travels to and from API Gateway\) and at rest \(while it is stored in AWS\)\.

## Data encryption at rest in Amazon API Gateway<a name="data-protection-at-rest"></a>

If you choose to enable caching for a REST API, you can enable cache encryption\. To learn more, see [Enabling API caching to enhance responsiveness](api-gateway-caching.md)\.

For more information about data protection, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

## Data encryption in transit in Amazon API Gateway<a name="data-protection-in-transit"></a>

The APIs created with Amazon API Gateway expose HTTPS endpoints only\. API Gateway doesn't support unencrypted \(HTTP\) endpoints\.

For greater security, you can choose a minimum Transport Layer Security \(TLS\) protocol version to be enforced for your API Gateway custom domain\. You can choose either a TLS version 1\.2 or TLS version 1\.0 security policy\. WebSocket APIs and HTTP APIs support only TLS 1\.2\. To learn more, see [Choosing a minimum TLS version for a custom domain in API Gateway](apigateway-custom-domain-tls-version.md)\.

You can also set up a Amazon CloudFront distribution with a custom SSL certificate in your account and use it with Regional APIs\. You can then configure the security policy for the CloudFront distribution with TLS 1\.1 or higher based on your security and compliance requirements\.

For more information about data protection, see [Protecting your REST API](rest-api-protect.md) and the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.