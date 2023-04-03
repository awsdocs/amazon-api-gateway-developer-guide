# Configure custom health checks for DNS failover<a name="dns-failover"></a>

You can use Amazon Route 53 health checks to control DNS failover from an API Gateway API in a primary AWS Region to one in a secondary Region\. This can help mitigate impacts in the event of a Regional issue\. If you use a custom domain, you can perform failover without requiring clients to change API endpoints\.

When you choose [Evaluate Target Health](https://docs.aws.amazon.com/Route53/latest/APIReference/API_AliasTarget.html#Route53-Type-AliasTarget-EvaluateTargetHealth>Evaluate Target Health) for an alias record, those records fail only when the API Gateway service is unavailable in the Region\. In some cases, your own API Gateway APIs can experience interruption before that time\. To control DNS failover directly, configure custom Route 53 health checks for your API Gateway APIs\. For this example, you use a CloudWatch alarm that helps operators control DNS failover\. For more examples and other considerations when you configure failover, see [Creating Disaster Recovery Mechanisms Using Route 53](http://aws.amazon.com/blogs/networking-and-content-delivery/creating-disaster-recovery-mechanisms-using-amazon-route-53/) and [Performing Route 53 health checks on private resources in a VPC with AWS Lambda and CloudWatch](http://aws.amazon.com/blogs/networking-and-content-delivery/performing-route-53-health-checks-on-private-resources-in-a-vpc-with-aws-lambda-and-amazon-cloudwatch)\.

**Topics**
+ [Prerequisites](#dns-failover-prereqs)
+ [Step 1: Set up resources](#dns-failover-intial-setup)
+ [Step 2: Initiate failover to the secondary Region](#dns-failover-initiate)
+ [Step 3: Test the failover](#dns-failover-test)
+ [Step 4: Return to the primary region](#dns-failover-return)
+ [Next steps: Customize and test regularly](#dns-failover-next-steps)

## Prerequisites<a name="dns-failover-prereqs"></a>

To complete this procedure, you must create and configure the following resources:
+ A domain name that you own\.
+ An ACM certificate for that domain name in two AWS Regions\. For more info, see [Getting certificates ready in AWS Certificate Manager](how-to-custom-domains-prerequisites.md)\.
+ A Route 53 hosted zone for your domain name\. For more information, see [Working with hosted zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-working-with.html) in the Amazon Route 53 Developer Guide\.

## Step 1: Set up resources<a name="dns-failover-intial-setup"></a>

In this example, you create the following resources to configure DNS failover for your domain name:
+ API Gateway APIs in two AWS Regions
+ API Gateway custom domain names with the same name in two AWS Regions
+ API Gateway API mappings that connect your API Gateway APIs to the custom domain names
+ Route 53 failover DNS records for the domain names
+ A CloudWatch alarm in the secondary Region
+ A Route 53 health check based on the CloudWatch alarm in the secondary Region

First, make sure that you have all of the required resources in the primary and secondary Regions\. The secondary Region should contain the alarm and health check\. This way, you don't depend on the primary Region to perform failover\. For example AWS CloudFormation templates that create these resources, see [samples/primary.zip](samples/primary.zip) and [samples/secondary.zip](samples/secondary.zip)\.

**Important**  
Before failover to the secondary Region, make sure that all required resources are available\. Otherwise, your API won't be ready for traffic in the secondary Region\. 

## Step 2: Initiate failover to the secondary Region<a name="dns-failover-initiate"></a>

In the following example, the standby Region receives a CloudWatch metric and initiates failover\. We use a custom metric that requires operator intervention to initiate failover\.

```
aws cloudwatch put-metric-data \
    --metric-name Failover \
    --namespace HealthCheck \
    --unit Count \
    --value 1 \
    --region us-west-1
```

## Step 3: Test the failover<a name="dns-failover-test"></a>

Invoke your API and verify that you get a response from the secondary Region\. If you used the example templates in step 1, the response changes from `{"message": "Hello from the primary Region!"}` to `{"message": "Hello from the secondary Region!"}` after failover\.

```
curl https://my-api.example.com

{"message": "Hello from the secondary Region!"}
```

## Step 4: Return to the primary region<a name="dns-failover-return"></a>

To return to the primary Region, send a CloudWatch metric that causes the health check to pass\.

```
aws cloudwatch put-metric-data \
    --metric-name Failover \
    --namespace HealthCheck \
    --unit Count \
    --value 0 \
    --region us-west-1
```

Invoke your API and verify that you get a response from the primary Region\. If you used the example templates in step 1, the response changes from `{"message": "Hello from the secondary Region!"}` to `{"message": "Hello from the primary Region!"}`\.

```
curl https://my-api.example.com

{"message": "Hello from the primary Region!"}
```

## Next steps: Customize and test regularly<a name="dns-failover-next-steps"></a>

This example demonstrates one way to configure DNS failover\. You can use a variety of CloudWatch metrics or HTTP endpoints for the health checks that manage failover\. Regularly test your failover mechanisms to make sure that they work as expected, and that operators are familiar with your failover procedures\.