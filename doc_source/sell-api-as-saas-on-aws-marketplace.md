# Sell your API Gateway APIs through AWS Marketplace<a name="sell-api-as-saas-on-aws-marketplace"></a>

After you build, test, and deploy your APIs, you can package them in an API Gateway [usage plan](api-gateway-api-usage-plans.md) and sell the plan as a Software as a Service \(SaaS\) product through AWS Marketplace\. API buyers subscribing to your product offering are billed by AWS Marketplace based on the number of requests made to the usage plan\.

To sell your APIs on AWS Marketplace, you must set up the sales channel to integrate AWS Marketplace with API Gateway\. Generally speaking, this involves listing your product on AWS Marketplace, setting up an IAM role with appropriate policies to allow API Gateway to send usage metrics to AWS Marketplace, associating an AWS Marketplace product with an API Gateway usage plan, and associating an AWS Marketplace buyer with an API Gateway API key\. Details are discussed in the following sections\.

For more information about selling your API as a SaaS product on AWS Marketplace, see the [AWS Marketplace User Guide](https://docs.aws.amazon.com/marketplace/latest/userguide/)\.

**Topics**
+ [Initialize AWS Marketplace integration with API Gateway](#sell-api-as-saas-on-aws-marketplace-inital-setup)
+ [Handle customer subscription to usage plans](#sell-api-as-saas-on-aws-marketplace-subscription-unsubscription)

## Initialize AWS Marketplace integration with API Gateway<a name="sell-api-as-saas-on-aws-marketplace-inital-setup"></a>

The following tasks are for one\-time initialization of AWS Marketplace integration with API Gateway, which enables you to sell your APIs as a SaaS product\.

### List a product on AWS Marketplace<a name="sell-api-as-saas-on-aws-marketplace-inital-setup-list-product"></a>

To list your usage plan as a SaaS product, submit a product load form through [AWS Marketplace](https://aws.amazon.com/marketplace/management/tour/)\. The product must contain a dimension named `apigateway` of the `requests` type\. This dimension defines the price\-per\-request and is used by API Gateway to meter requests to your APIs\.

### Create the metering role<a name="sell-api-as-saas-on-aws-marketplace-inital-setup-create-metering-role"></a>

Create an IAM role named `ApiGatewayMarketplaceMeteringRole` with the following execution policy and trust policy\. This role allows API Gateway to send usage metrics to AWS Marketplace on your behalf\.

#### Execution policy of the metering role<a name="sell-api-as-saas-on-aws-marketplace-inital-setup-create-metering-role-execution-policy"></a>

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "aws-marketplace:BatchMeterUsage",
        "aws-marketplace:ResolveCustomer"
      ],
      "Resource": "*",
      "Effect": "Allow"
    }
  ]
}
```

#### Trusted relationship policy of the metering role<a name="sell-api-as-saas-on-aws-marketplace-inital-setup-create-metering-role-trusted-policy"></a>

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### Associate usage plan with AWS Marketplace product<a name="sell-api-as-saas-on-aws-marketplace-associate-usage-plan-with-saas-product"></a>

When you list a product on AWS Marketplace, you receive an AWS Marketplace product code\. To integrate API Gateway with AWS Marketplace, associate your usage plan with the AWS Marketplace product code\. You enable the association by setting the API Gateway `UsagePlan`'s [https://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan/#productCode](https://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan/#productCode) field to your AWS Marketplace product code, using the API Gateway console, the API Gateway REST API, the AWS CLI for API Gateway, or AWS SDK for API Gateway\. The following code example uses the API Gateway REST API:

```
PATCH /usageplans/USAGE_PLAN_ID
Host: apigateway.region.amazonaws.com
Authorization: ...

{
    "patchOperations" : [{
        "path" : "/productCode",
        "value" : "MARKETPLACE_PRODUCT_CODE",
        "op" : "replace"
    }]
}
```

## Handle customer subscription to usage plans<a name="sell-api-as-saas-on-aws-marketplace-subscription-unsubscription"></a>

The following tasks are handled by your developer portal application\.

When a customer subscribes to your product through AWS Marketplace, AWS Marketplace forwards a `POST` request to the SaaS subscriptions URL  that you registered when listing your product on AWS Marketplace\. The `POST` request comes with an `x-amzn-marketplace-token` header parameter containing buyer information\. Follow the instructions in [Configuring Your SaaS Product to Accept New Customers](https://docs.aws.amazon.com/marketplace/latest/userguide/configuring-your-saas-application-to-accept-new-customers.html#in-your-application) to handle this redirect in your developer portal application\.

Responding to a customer's subscribing request, AWS Marketplace sends a `subscribe-success` notification to an Amazon SNS topic that you can subscribe to\. \(See [Configuring Your SaaS Product to Accept New Customers](https://docs.aws.amazon.com/marketplace/latest/userguide/configuring-your-saas-application-to-accept-new-customers.html#in-your-application)\)\. To accept the customer subscription request, you handle the `subscribe-success` notification by creating or retrieving an API Gateway API key for the customer, associating the customer's AWS Marketplace\-provisioned `customerId` with the API keys, and then adding the API key to your usage plan\.

When the customer's subscription request completes, the developer portal application should present the customer with the associated API key and inform the customer that the API key must be included in the `x-api-key` header in requests to the APIs\.

When a customer cancels a subscription to a usage plan, AWS Marketplace sends an `unsubscribe-success` notification to the SNS topic\. To complete the process of unsubscribing the customer, you handle the `unsubscribe-success` notification by removing the customer's API keys from the usage plan\.

### Authorize a customer to access a usage plan<a name="sell-api-as-saas-on-aws-marketplace-subscription-unsubscription-authorize-access-to-usage-plan"></a>

To authorize access to your usage plan for a given customer, use the API Gateway API to fetch or create an API key for the customer and add the API key to the usage plan\. 

The following example shows how to call the API Gateway REST API to create a new API key with a specific AWS Marketplace `customerId` value \(*MARKETPLACE\_CUSTOMER\_ID*\)\.

```
POST apikeys HTTP/1.1
Host: apigateway.region.amazonaws.com
Authorization: ...

{
  "name" : "my_api_key",
  "description" : "My API key",
  "enabled" : "false",
  "stageKeys" : [ {
    "restApiId" : "uycll6xg9a",
    "stageName" : "prod"
  } ],
  "customerId" : "MARKETPLACE_CUSTOMER_ID"
}
```

The following example shows how to get an API key with a specific AWS Marketplace `customerId` value \(*MARKETPLACE\_CUSTOMER\_ID*\)\.

```
GET apikeys?customerId=MARKETPLACE_CUSTOMER_ID HTTP/1.1
Host: apigateway.region.amazonaws.com
Authorization: ...
```

To add an API key to a usage plan, create a [https://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan-key/](https://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan-key/) with the API key for the relevant usage plan\. The following example shows how to accomplish this using the API Gateway REST API, where `n371pt` is the usage plan ID and `q5ugs7qjjh` is an example API `keyId` returned from the preceding examples\.

```
POST /usageplans/n371pt/keys HTTP/1.1
Host: apigateway.region.amazonaws.com
Authorization: ...

{
    "keyId": "q5ugs7qjjh",
    "keyType": "API_KEY"
}
```

### Associate a customer with an API key<a name="sell-api-as-saas-on-aws-marketplace-subscription-unsubscription-associate-marketplace"></a>

You must update the [https://docs.aws.amazon.com/apigateway/api-reference/resource/api-key/](https://docs.aws.amazon.com/apigateway/api-reference/resource/api-key/)'s `customerId` field to the AWS Marketplace customer ID of the customer\. This associates the API key with the AWS Marketplace customer, which enables metering and billing for the buyer\. The following code example calls the API Gateway REST API to do that\.

```
PATCH /apikeys/q5ugs7qjjh
Host: apigateway.region.amazonaws.com
Authorization: ...
        
{
    "patchOperations" : [{
        "path" : "/customerId",
        "value" : "MARKETPLACE_CUSTOMER_ID",
        "op" : "replace"
    }]
}
```