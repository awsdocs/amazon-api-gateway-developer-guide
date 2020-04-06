# Setting up a regional custom domain name in API Gateway<a name="apigateway-regional-api-custom-domain-create"></a>

You can create a custom domain name for a Regional API endpoint \(for an AWS Region\)\. To create a custom domain name, you must provide a Region\-specific ACM certificate\. For more information about creating or uploading a custom domain name certificate, see [Getting certificates ready in AWS Certificate Manager](how-to-custom-domains-prerequisites.md)\. 

**Important**  
For an API Gateway Regional custom domain name, you must request or import the certificate in the same Region as your API\.

 When you create a Regional custom domain name \(or migrate one\) with an ACM certificate, API Gateway creates a service\-linked role in your account if the role doesn't exist already\. The service\-linked role is required to attach your ACM certificate to your Regional endpoint\. The role is named **AWSServiceRoleForAPIGateway** and will have the **APIGatewayServiceRolePolicy** managed policy attached to it\. For more information about use of the service\-linked role, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html)\. 

**Important**  
You must create a DNS record to point the custom domain name to the Regional domain name\. This enables the traffic that is bound to the custom domain name to be routed to the API's Regional hostname\. The DNS record can be the CNAME or "A" type\.

**Topics**
+ [Set up a regional custom domain name with an ACM certificate using the API Gateway console](#create-regional-domain-using-console)
+ [Set up a regional custom domain name with an ACM certificate using AWS CLI](#apigateway-regional-api-custom-domain-create-with-awscli)

## Set up a regional custom domain name with an ACM certificate using the API Gateway console<a name="create-regional-domain-using-console"></a>

 To use the API Gateway console to set up a Regional custom domain name, use the following procedure\. 

**To set up a regional custom domain name using the API Gateway console**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **Custom domain names** from the main navigation pane\. 

1. Choose **Create**\.

1. For **Domain name**, enter a domain name\.

1. Under Configuration, choose **Regional**\.

1. Choose a minimum TLS version\.

1. Choose an ACM certificate\. The certificate must be in the same Region as the API\.

1. Choose **Create**\.

1. Follow the Route 53 documentation on [configuring Route 53 to route traffic to API Gateway](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-api-gateway.html)\.

The following procedure sets up API mappings to map paths from your custom domain name to your API stages\.

**To create API mappings name using the API Gateway console**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a custom domain name\.

1. Choose **Configure API mappings**\.

1. Choose **Add new mapping**\.

1. Specify the **API**, **Stage**, and **Path** for the mapping\.

1. Choose **Save**\.

To learn about setting basepath mappings for the custom domain, see [Configure base path mapping of an API with a custom domain name as its hostname](how-to-edge-optimized-custom-domain-name.md#how-to-custom-domains-mapping-console)\.

## Set up a regional custom domain name with an ACM certificate using AWS CLI<a name="apigateway-regional-api-custom-domain-create-with-awscli"></a>

To use the AWS CLI to set up a custom domain name for a Regional API, use the following procedure\.

1. Call `create-domain-name`, specifying a custom domain name and the ARN of a Regional certificate\.

   ```
   aws apigatewayv2 create-domain-name \ 
       --domain-name 'regional.example.com' \
       --domain-name-configurations CertificateArn=arn:aws:acm:us-west-2:123456789012:certificate/123456789012-1234-1234-1234-12345678
   ```

   Note that the specified certificate is from the `us-west-2` Region and for this example, we assume that the underlying API is from the same Region\.

   If successful, the call returns a result similar to the following:

   ```
   {
       "ApiMappingSelectionExpression": "$request.basepath",
       "DomainName": "regional.example.com",
       "DomainNameConfigurations": [
           {
               "ApiGatewayDomainName": "d-id.execute-api.us-west-2.amazonaws.com",
               "CertificateArn": "arn:aws:acm:us-west-2:123456789012:certificate/id",
               "DomainNameStatus": "AVAILABLE",
               "EndpointType": "REGIONAL",
               "HostedZoneId": "id",
               "SecurityPolicy": "TLS_1_2"
           }
       ]
   }
   ```

   The `DomainNameConfigurations` property value returns the Regional API's hostname\. You must create a DNS record to point your custom domain name to this Regional domain name\. This enables the traffic that is bound to the custom domain name to be routed to this Regional API's hostname\.

1. Create a DNS record to associate the custom domain name and the Regional domain name\. This enables requests that are bound to the custom domain name to be routed to the API's Regional hostname\.

1. Add a base path mapping to expose the specified API \(for example, `0qzs2sy7bh`\) in a deployment stage \(for example, `test`\) under the specified custom domain name \(for example, `regional.example.com`\)\.

   ```
   aws apigatewayv2 create-api-mapping \
       --domain-name 'regional.example.com' \
       --api-mapping-key 'myApi' \
       --api-id 0qzs2sy7bh \
       --stage 'test'
   ```

   As a result, the base URL using the custom domain name for the API that is deployed in the stage becomes `https://regional.example.com/myAPI`\.

1. Configure your DNS records to map the Regional custom domain name to its hostname of the given hosted zone ID\. First create a JSON file that contains the configuration for setting up a DNS record for the Regional domain name\. The following example shows how to create a DNS `A` record to map a Regional custom domain name \(`regional.example.com`\) to its Regional hostname \(`d-numh1z56v6.execute-api.us-west-2.amazonaws.com`\) provisioned as part of the custom domain name creation\. The `DNSName` and `HostedZoneId` properties of `AliasTarget` can take the `regionalDomainName` and `regionalHostedZoneId` values, respectively, of the custom domain name\. You can also get the Regional Route 53 Hosted Zone IDs in [Amazon API Gateway Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/apigateway.html)\.

   ```
   {
     "Changes": [
       {
         "Action": "CREATE",
         "ResourceRecordSet": {
           "Name": "regional.example.com",
           "Type": "A",
           "AliasTarget": {
             "DNSName": "d-numh1z56v6.execute-api.us-west-2.amazonaws.com",
             "HostedZoneId": "Z2OJLYMUO9EFXC",
             "EvaluateTargetHealth": false
           }
         }
       }
     ]
   }
   ```

1. Run the following CLI command:

   ```
   aws route53 change-resource-record-sets \
       --hosted-zone-id {your-hosted-zone-id} \
       --change-batch file://path/to/your/setup-dns-record.json
   ```

   where *\{your\-hosted\-zone\-id\}* is the Route 53 Hosted Zone ID of the DNS record set in your account\. The `change-batch` parameter value points to a JSON file \(*setup\-dns\-record\.json*\) in a folder \(*path/to/your*\)\.