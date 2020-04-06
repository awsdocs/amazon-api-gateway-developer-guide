# Creating an edge\-optimized custom domain name<a name="how-to-edge-optimized-custom-domain-name"></a>

**Topics**
+ [Set up an edge\-optimized custom domain name for an API Gateway API](#how-to-custom-domains-console)
+ [Log custom domain name creation in CloudTrail](#how-to-custom-domain-log-cloudfront-distribution-update-in-cloudtrail)
+ [Configure base path mapping of an API with a custom domain name as its hostname](#how-to-custom-domains-mapping-console)
+ [Rotate a certificate imported into ACM](#how-to-rotate-custom-domain-certificate)
+ [Call your API with custom domain names](#how-to-custom-domains-call-api-with-sni)

## Set up an edge\-optimized custom domain name for an API Gateway API<a name="how-to-custom-domains-console"></a>

The following procedure describes how to set create a custom domain name for an API using the API Gateway console\.

**To create a custom domain name using the API Gateway console**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1.  Choose **Custom domain names** from the main navigation pane\. 

1. Choose **Create**\.

1. For **Domain name**, enter a domain name\.

1. Under Configuration, choose **Edge\-optimized**\.

1. Choose a minimum TLS version\.

1. Choose an ACM certificate\.
**Note**  
To use an ACM certificate with an API Gateway edge\-optimized custom domain name, you must request or import the certificate in the `us-east-1` Region \(US East \(N\. Virginia\)\)\.

1. Choose **Create**\.

1.  After the custom domain name is created, the console displays the associated CloudFront distribution domain name, in the form of `distribution-id.cloudfront.net`, along with the certificate ARN\. Note the CloudFront distribution domain name shown in the output\. You need it in the next step to set the custom domain's CNAME value or A\-record alias target in your DNS\. 
**Note**  
 The newly created custom domain name takes about 40 minutes to be ready\. In the meantime, you can configure the DNS record alias to map the custom domain name to the associated CloudFront distribution domain name and to set up the base path mapping for the custom domain name while the custom domain name is being initialized\. 

1.  In this step, we use Amazon Route 53 as an example DNS provider to show how to set up an A\-record alias for your internet domain to map the custom domain name to the associated CloudFront distribution name\. The instructions can be adapted to other DNS providers\. 

   1. Sign in to the Route 53 console\.

   1. Create an `A-IPv4 address` record set for your custom domain \(e\.g\., `api.example.com`\)\. An A\-record maps a custom domain name to an IP4 address\.

   1. Choose **Yes** for **Alias**, enter the CloudFront domain name \(e\.g\., `d3boq9ikothtgw.cloudfront.net`\) in **Alias Target**, and then choose **Create**\. The A\-record alias here maps your custom domain name to the specified CloudFront domain name that is itself mapped to an IP4 address\.   
![\[Set a DNS record alias for a custom domain name for an API in API Gateway\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-custom-domain-dns-alias-set.png)
**Tip**  
 The **Alias Hosted Zone ID** identifies the hosted zone of the specified **Alias Target**\. The Route 53 console automatically fills in the value when you enter a valid domain name for **Alias Target**\. To create an A\-record alias without using the Route 53 console, such as when you use the AWS CLI, you must specify the required hosted zone Id\. For any CloudFront distribution domain name, the hosted zone Id value is always `Z2FDTNDATAQYW2`, as documented in [AWS Regions and Endpoints for CloudFront](https://docs.aws.amazon.com/general/latest/gr/rande.html#cf_region)\. 

    For most DNS providers, a custom domain name is added to the hosted zone as a CNAME resource record set\. The CNAME record name specifies the custom domain name you entered earlier in **Domain Name** \(for example, `api.example.com`\)\. The CNAME record value specifies the domain name for the CloudFront distribution\. However, use of a CNAME record will not work if your custom domain is a zone apex \(i\.e\., `example.com` instead of `api.example.com`\)\. A zone apex is also commonly known as the root domain of your organization\. For a zone apex you need to use an A\-record alias, provided that is supported by your DNS provider\. 

   With Route 53 you can create an A record alias for your custom domain name and specify the CloudFront distribution domain name as the alias target, as shown above\. This means that Route 53 can route your custom domain name even if it is a zone apex\. For more information, see [Choosing Between Alias and Non\-Alias Resource Record Sets](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html) in the *Amazon Route 53 Developer Guide*\. 

   Use of A\-record aliases also eliminates exposure of the underlying CloudFront distribution domain name because the domain name mapping takes place solely within Route 53\. For these reasons, we recommend that you use Route 53 A\-record alias whenever possible\. 

 In addition to using the API Gateway console, you can use the API Gateway REST API, AWS CLI or one of the AWS SDKs to set up the custom domain name for your APIs\. As an illustration, the following procedure outlines the steps to do so using the REST API calls\. 

**To set up a custom domain name using the API Gateway REST API**

1. Call [domainname:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-create/), specifying the custom domain name and the ARN of a certificate stored in AWS Certificate Manager\.

    The successful API call returns a `201 Created` response containing the certificate ARN as well as the associated CloudFront distribution name in its payload\.

1. Note the CloudFront distribution domain name shown in the output\. You need it in the next step to set the custom domain's CNAME value or A\-record alias target in your DNS\.

1. Follow the previous procedure to set up an A\-record alias to map the custom domain name to its CloudFront distribution name\.

For code examples of this REST API call, see [domainname:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-create/)\.

## Log custom domain name creation in CloudTrail<a name="how-to-custom-domain-log-cloudfront-distribution-update-in-cloudtrail"></a>

When CloudTrail is enabled for logging API Gateway calls made by your account, API Gateway logs the associated CloudFront distribution updates when a custom domain name is created or updated for an API\. Because these CloudFront distributions are owned by API Gateway, each of these reported CloudFront distributions is identified by one of the following Region\-specific API Gateway account IDs, instead of the API owner's account ID\. 


| Region | Account ID | 
| --- | --- | 
| us\-east\-1 | 392220576650 | 
| us\-east\-2 | 718770453195 | 
| us\-west\-1 | 968246515281 | 
| us\-west\-2 | 109351309407 | 
| ca\-central\-1 | 796887884028 | 
| eu\-west\-1 | 631144002099 | 
| eu\-west\-2 | 544388816663 | 
| eu\-west\-3 | 061510835048 | 
| eu\-central\-1 | 474240146802 | 
| eu\-north\-1 | 394634713161 | 
| ap\-northeast\-1 | 969236854626 | 
| ap\-northeast\-2 | 020402002396 | 
| ap\-southeast\-1 | 195145609632 | 
| ap\-southeast\-2 | 798376113853 | 
| ap\-south\-1 | 507069717855 | 
| ap\-east\-1 | 174803364771 | 
| sa\-east\-1 | 287228555773 | 
| me\-south\-1 | 855739686837 | 

## Configure base path mapping of an API with a custom domain name as its hostname<a name="how-to-custom-domains-mapping-console"></a>

 You can use a single custom domain name as the hostname of multiple APIs\. You achieve this by configuring the base path mappings on the custom domain name\. With the base path mappings, an API under the custom domain is accessible through the combination of the custom domain name and the associated base path\. 

For example, if you created an API named `PetStore` and another API named `PetShop` and set up a custom domain name of `api.example.com` in API Gateway, you can set the `PetStore` API's URL as `https://api.example.com` or `https://api.example.com/myPetStore`\. The `PetStore` API is associated with the base path of an empty string or `myPetStore` under the custom domain name of `api.example.com`\. Similarly, you can assign a base path of `yourPetShop` for the `PetShop` API\. The URL of `https://api.example.com/yourPetShop` is then the root URL of the `PetShop` API\. 

Before setting the base path for an API, complete the steps in [Set up an edge\-optimized custom domain name for an API Gateway API](#how-to-custom-domains-console)\.

The following procedure sets up API mappings to map paths from your custom domain name to your API stages\.

**To create API mappings name using the API Gateway console**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a custom domain name\.

1. Choose **Configure API mappings**\.

1. Choose **Add new mapping**\.

1. Specify the **API**, **Stage**, and **Path** \(optional\) for the mapping\.

1. Choose **Save**\.

In addition, you can call the API Gateway REST API, AWS CLI, or one of the AWS SDKs to set up the base path mapping of an API with a custom domain name as its hostname\. As an illustration, the following procedure outlines the steps to do so using the REST API calls\.

**To set up the base path mapping of an API using the API Gateway REST API**
+  Call [basepathmapping:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/basepathmapping-create/) on a specific custom domain name, specifying the `basePath`, `restApiId`, and a deployment `stage` property in the request payload\.

   The successful API call returns a `201 Created` response\.

For code examples of the REST API call, see [basepathmapping:create](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/basepathmapping-create/)\.

## Rotate a certificate imported into ACM<a name="how-to-rotate-custom-domain-certificate"></a>

 ACM automatically handles renewal of certificates it issues\. You do not need to rotate any ACM\-issued certificates for your custom domain names\. CloudFront handles it on your behalf\. 

 However, if you import a certificate into ACM and use it for a custom domain name, you must rotate the certificate before it expires\. This involves importing a new third\-party certificate for the domain name and rotate the existing certificate to the new one\. You need to repeat the process when the newly imported certificate expires\. Alternatively, you can request ACM to issue a new certificate for the domain name and rotate the existing one to the new ACM\-issued certificate\. After that, you can leave ACM and CloudFront to handle the certificate rotation for you automatically\. To create or import a new ACM certificate, follow the steps to [request or import a new ACM certificate](how-to-custom-domains-prerequisites.md) for the specified domain name\. 

 To rotate a certificate for a domain name, you can use the API Gateway console, the API Gateway REST API, AWS CLI, or one of the AWS SDKs\. 

**To rotate an expiring certificate imported into ACM using the API Gateway console**

1. Request or import a certificate in ACM\.

1. Go back to the API Gateway console\.

1. Choose **Custom domain names** from the API Gateway console main navigation pane\.

1. Choose a custom domain name\.

1. Choose **Edit**\.

1. Choose the desired certificate from the **ACM certificate** dropdown list\. 

1. Choose **Save** to begin rotating the certificate for the custom domain name\. 
**Note**  
It takes about 40 minutes for the process to finish\. After the rotation is done, you can choose the two\-way arrow icon next to **ACM Certificate** to roll back to the original certificate\.

To illustrate how to programmatically rotate an imported certificate for a custom domain name, we outline the steps using the API Gateway REST API\.

**Rotate an imported certificate using the API Gateway REST API**
+  Call [domainname:update](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/) action, specifying the ARN of the new ACM certificate for the specified domain name\. 

## Call your API with custom domain names<a name="how-to-custom-domains-call-api-with-sni"></a>

Calling an API with a custom domain name is the same as calling the API with its default domain name, provided that the correct URL is used\.

The following examples compare and contrast a set of default URLs and corresponding custom URLs of two APIs \(`udxjef` and `qf3duz`\) in a specified Region \(`us-east-1`\), and of a given custom domain name \(`api.example.com`\)\.


**Root URLs of APIs with default and custom domain names**  

| API ID | Stage | Default URL | Base path | Custom URL | 
| --- | --- | --- | --- | --- | 
| udxjef | pro | https://udxjef\.execute\-api\.us\-east\-1\.amazonaws\.com/pro | /petstore | https://api\.example\.com/petstore | 
| udxjef | tst | https://udxjef\.execute\-api\.us\-east\-1\.amazonaws\.com/tst | /petdepot | https://api\.example\.com/petdepot | 
| qf3duz | dev | https://qf3duz\.execute\-api\.us\-east\-1\.amazonaws\.com/dev | /bookstore | https://api\.example\.com/bookstore | 
| qf3duz | tst | https://qf3duz\.execute\-api\.us\-east\-1\.amazonaws\.com/tst | /bookstand | https://api\.example\.com/bookstand | 

 API Gateway supports custom domain names for an API by using [Server Name Indication \(SNI\)](https://en.wikipedia.org/wiki/Server_Name_Indication)\. You can invoke the API with a custom domain name using a browser or a client library that supports SNI\. 

 API Gateway enforces SNI on the CloudFront distribution\. For information on how CloudFront uses custom domain names, see [Amazon CloudFront Custom SSL](https://aws.amazon.com/cloudfront/custom-ssl-domains/)\. 