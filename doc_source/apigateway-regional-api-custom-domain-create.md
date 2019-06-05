# Set up a Custom Domain Name for a Regional API in API Gateway<a name="apigateway-regional-api-custom-domain-create"></a>

As with an edge\-optimized API endpoint, you can create a custom domain name for a regional API endpoint\. To support a regional custom domain name, you must provide a certificate\. If an AWS Certificate Manager \(ACM\) Certificate is used, this certificate must be region\-specific\. If ACM is available in the region, you must provide an ACM Certificate specific to that region\. If ACM is not supported in the region, you must upload a certificate to API Gateway in that region when creating the regional custom domain name\. For more information about creating or uploading a custom domain name certificate, see [Get Certificates Ready in AWS Certificate Manager](how-to-custom-domains-prerequisites.md)\. 

**Important**  
For an API Gateway regional custom domain name, you must request or import the certificate in the same region as your API\.

 When you create a regional custom domain name \(or migrate one\) with an ACM Certificate, API Gateway creates a service\-linked role in your account, if the role does not exist already\. The service\-linked role is required to attach your ACM Certificate to your regional endpoint\. The role is named **AWSServiceRoleForAPIGateway** and will have the **APIGatewayServiceRolePolicy** managed policy attached to\. For more information about use of the service\-linked role, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html)\. 

**Important**  
You must create a DNS record to point the custom domain name to the regional domain name\. This enables the traffic that is bound to the custom domain name to be routed to the API's regional host name\. The DNS record can be of the CNAME or A type\.

**Topics**
+ [Set up a Regional Custom Domain Name Using the API Gateway Console](#create-regional-domain-using-console)
+ [Set up a Regional Custom Domain Name Using AWS CLI](#apigateway-regional-api-custom-domain-create-with-awscli)

## Set up a Regional Custom Domain Name Using the API Gateway Console<a name="create-regional-domain-using-console"></a>

 To use the API Gateway console to set up a regional custom domain name, use the following procedure\. 

**To set up a regional custom domain name using the API Gateway console**

1.  Sign in to the API Gateway console and choose **Custom Domain Names** in the primary navigation pane\.

1.  Choose ** \+Create New Custom Domain Name** above the **Custom Domain Names** table\.

1. In **New Custom Domain Name**, type a custom domain name, for example, `my-api.example.com`, in **Domain Name**\.

1.  Choose **Regional** for **Endpoint Configuration**\.

1. Choose a certificate from the **ACM Certificate** drop\-down list\. The certificate must be from the same region where the API is deployed\.

1. If you have created and deployed an API to use this custom domain name, choose** Add mapping**, type a base path under the custom domain name in **Path**, choose an API from the API drop\-down list under **Destination**, and choose a stage from the **Stage** drop\-down list\. To add another base path mapping, repeat the step\.

1.  Choose **Save**\. 

1. Follow the Route 53 documentation on [configuring Route 53 to route traffic to API Gateway](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-api-gateway.html)\.

## Set up a Regional Custom Domain Name Using AWS CLI<a name="apigateway-regional-api-custom-domain-create-with-awscli"></a>

To use the AWS CLI to set up a custom domain name for a regional API, use the following procedure\.

1. Call `create-domain-name`, specifying a custom domain name of the `REGIONAL` type and the ARN of a regional certificate\.

   ```
   aws apigateway create-domain-name \ 
       --domain-name 'regional.example.com' \
       --endpoint-configuration types=REGIONAL \ 
       --regional-certificate-arn 'arn:aws:acm:us-west-2:123456789012:certificate/c19332f0-3be6-457f-a244-e03a423084e6'
   ```

   Note that the specified certificate is from the `us-west-2` region and for this example, we assume that the underlying API is from the same region\.

   If successful, the call returns a result similar to the following:

   ```
   {
       "certificateUploadDate": "2017-10-13T23:02:54Z",
       "domainName": "regional.example.com",
       "endpointConfiguration": {
           "types": "REGIONAL"
       },
       "regionalCertificateArn": "arn:aws:acm:us-west-2:123456789012:certificate/c19332f0-3be6-457f-a244-e03a423084e6",
       "regionalDomainName": "d-numh1z56v6.execute-api.us-west-2.amazonaws.com"
   }
   ```

   The `regionalDomainName` property value returns the regional API's host name\. You must create a DNS record to point your custom domain name to this regional domain name\. This enables the traffic that is bound to the custom domain name to be routed to this regional API's host name\.

1. Create a DNS record to associate the custom domain name and the regional domain name\. This enables requests that are bound to the custom domain name to be routed to the API's regional host name\.

1. Add a base path mapping to expose the specified API \(for example, `0qzs2sy7bh`\) in a deployment stage \(for example, `test`\) under the specified custom domain name \(for example, `regional.example.com`\)\.

   ```
   aws apigateway create-base-path-mapping \
       --domain-name 'regional.example.com' \
       --base-path 'RegionalApiTest' \
       --rest-api-id 0qzs2sy7bh \
       --stage 'test'
   ```

   As a result, the base URL using the custom domain name for the API that is deployed in the stage becomes `https://regional.example.com/RegionalApiTest`\.

1. Configure your DNS records to map the regional custom domain name to its host name of the given hosted zone ID\. First create a JSON file that contains the configuration for setting up a DNS record for the regional domain name\. The following example shows how to create a DNS `A` record to map a regional custom domain name \(`regional.example.com`\) to its regional host name \(`d-numh1z56v6.execute-api.us-west-2.amazonaws.com`\) provisioned as part of the custom domain name creation\. The `DNSName` and `HostedZoneId` properties of `AliasTarget` can take the `regionalDomainName` and `regionalHostedZoneId` values, respectively, of the custom domain name\. You can also get the regional Route 53 Hosted Zone IDs in [API Gateway Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#apigateway_region)\.

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