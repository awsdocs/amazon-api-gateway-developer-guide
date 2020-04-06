# Using AWS WAF to protect your APIs<a name="apigateway-control-access-aws-waf"></a>

AWS WAF is a web application firewall that helps protect web applications and APIs from attacks\. It enables you to configure a set of rules \(called a web access control list \(web ACL\)\) that allow, block, or count web requests based on customizable web security rules and conditions that you define\. For more information, see [How AWS WAF Works](https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html)\.

You can use AWS WAF to protect your API Gateway API from common web exploits, such as SQL injection and cross\-site scripting \(XSS\) attacks\. These could affect API availability and performance, compromise security, or consume excessive resources\. For example, you can create rules to allow or block requests from specified IP address ranges, requests from CIDR blocks, requests that originate from a specific country or region, requests that contain malicious SQL code, or requests that contain malicious script\. 

You can also create rules that match a specified string or a regular expression pattern in HTTP headers, method, query string, URI, and the request body \(limited to the first 8 KB\)\. Additionally, you can create rules to block attacks from specific user agents, bad bots, and content scrapers\. For example, you can use rate\-based rules to specify the number of web requests that are allowed by each client IP in a trailing, continuously updated, 5\-minute period\. 

**Important**  
AWS WAF is your first line of defense against web exploits\. When AWS WAF is enabled on an API, AWS WAF rules are evaluated before other access control features, such as [resource policies](apigateway-resource-policies.md), [IAM policies](permissions.md), [Lambda authorizers](apigateway-use-lambda-authorizer.md), and [Amazon Cognito authorizers](apigateway-integrate-with-cognito.md)\. For example, if AWS WAF blocks access from a CIDR block that a resource policy allows, AWS WAF takes precedence and the resource policy isn't evaluated\.

To enable AWS WAF for your API, you need to do the following:

1. Use the AWS WAF console, AWS SDK, or CLI to create a Regional web ACL that contains the desired combination of AWS WAF managed rules and your own custom rules\. For more information, see [Getting Started with AWS WAF](https://docs.aws.amazon.com/waf/latest/developerguide/getting-started.html) and [Creating and Configuring a Web Access Control List \(Web ACL\)](https://docs.aws.amazon.com/waf/latest/developerguide/web-acl.html)\.
**Important**  
API Gateway requires a Regional web ACL\.

1. Associate the AWS WAF Regional web ACL with an API stage\. You can do this by using the AWS WAF console, AWS SDK, or CLI or by using the API Gateway console, AWS SDK, or CLI\.

## To associate an AWS WAF regional Web ACL with an API Gateway API stage using the API Gateway console<a name="apigateway-control-access-aws-waf-console"></a>

To use the API Gateway console to associate an AWS WAF Regional web ACL with an existing API Gateway API stage, use the following steps:

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the **APIs** navigation pane, choose the API, and then choose **Stages**\.

1. In the **Stages** pane, choose the name of the stage\.

1. In the **Stage Editor** pane, choose the **Settings** tab\.

1. To associate a Regional web ACL with the API stage:

   1. In the AWS WAF web ACL dropdown list, choose the Regional web ACL that you want to associate with this stage\.
**Note**  
If the web ACL you need doesn't exist yet, choose **Create WebACL**\. Then choose **Go to AWS WAF**to open the AWS WAF console in a new browser tab and create a Regional web ACL\. Then return to the API Gateway console to associate the web ACL with the stage\.

1. Choose **Save Changes**\.

## Associate an AWS WAF regional Web ACL with an API Gateway API stage using the AWS CLI<a name="apigateway-control-access-aws-waf-awscli"></a>

To use the AWS CLI to associate an AWS WAF Regional web ACL with an existing API Gateway API stage, call the [https://docs.aws.amazon.com/cli/latest/reference/waf-regional/associate-web-acl.html](https://docs.aws.amazon.com/cli/latest/reference/waf-regional/associate-web-acl.html) command, as in the following example:

```
aws waf-regional associate-web-acl \
--web-acl-id 'aabc123a-fb4f-4fc6-becb-2b00831cadcf' \
--resource-arn 'arn:aws:apigateway:{region}::/restapis/4wk1k4onj3/stages/prod'
```

## Associate an AWS WAF regional web ACL with an API stage using the AWS WAF REST API<a name="apigateway-control-access-aws-waf-api"></a>

To use the AWS WAF REST API to associate an AWS WAF Regional web ACL with an existing API Gateway API stage, call the [https://docs.aws.amazon.com/waf/latest/APIReference/API_regional_AssociateWebACL.html](https://docs.aws.amazon.com/waf/latest/APIReference/API_regional_AssociateWebACL.html) command, as in the following example:

```
import boto3
 
waf = boto3.client('wafregional')
 
waf.associate_web_acl(
    WebACLId='aabc123a-fb4f-4fc6-becb-2b00831cadcf',
    ResourceArn='arn:aws:apigateway:{region}::/restapis/4wk1k4onj3/stages/prod'
)
```