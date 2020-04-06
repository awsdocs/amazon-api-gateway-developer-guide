# Using service\-linked roles for API Gateway<a name="using-service-linked-roles"></a>

Amazon API Gateway uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to API Gateway\. Service\-linked roles are predefined by API Gateway and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up API Gateway easier because you don't have to manually add the necessary permissions\. API Gateway defines the permissions of its service\-linked roles, and unless defined otherwise, only API Gateway can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

You can delete a service\-linked role only after first deleting the related resources\. This protects your API Gateway resources because you can't inadvertently remove permission to access the resources\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-linked role permissions for API Gateway<a name="slr-permissions"></a>

API Gateway uses the service\-linked role named **AWSServiceRoleForAPIGateway** – Allows API Gateway to access Elastic Load Balancing, Amazon Kinesis Data Firehose, and other service resources on your behalf\.

The AWSServiceRoleForAPIGateway service\-linked role trusts the following services to assume the role:
+ `ops.apigateway.amazonaws.com`

The role permissions policy allows API Gateway to complete the following actions on the specified resources:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "elasticloadbalancing:AddListenerCertificates",
                "elasticloadbalancing:RemoveListenerCertificates",
                "elasticloadbalancing:ModifyListener",
                "elasticloadbalancing:DescribeListeners",
                "elasticloadbalancing:DescribeLoadBalancers",
                "xray:PutTraceSegments",
                "xray:PutTelemetryRecords",
                "xray:GetSamplingTargets",
                "xray:GetSamplingRules",
                "logs:CreateLogDelivery",
                "logs:GetLogDelivery",
                "logs:UpdateLogDelivery",
                "logs:DeleteLogDelivery",
                "logs:ListLogDeliveries",
                "servicediscovery:DiscoverInstances"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "firehose:DescribeDeliveryStream",
                "firehose:PutRecord",
                "firehose:PutRecordBatch"
            ],
            "Resource": "arn:aws:firehose:*:*:deliverystream/amazon-apigateway-*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "acm:DescribeCertificate"
            ],
            "Resource": "arn:aws:acm:*:*:certificate/*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:CreateNetworkInterfacePermission",
            "Resource": "arn:aws:ec2:*:*:network-interface/*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "arn:aws:ec2:*:*:network-interface/*",
            "Condition": {
                "ForAllValues:StringEquals": {
                    "aws:TagKeys": [
                        "Owner",
                        "VpcLinkId"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:DeleteNetworkInterface",
                "ec2:AssignPrivateIpAddresses",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterfacePermission",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeVpcs",
                "ec2:DescribeNetworkInterfacePermissions",
                "ec2:UnassignPrivateIpAddresses",
                "ec2:DescribeSubnets",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "servicediscovery:GetNamespace",
            "Resource": "arn:aws:servicediscovery:*:*:namespace/*"
        },
        {
            "Effect": "Allow",
            "Action": "servicediscovery:GetService",
            "Resource": "arn:aws:servicediscovery:*:*:service/*"
        }
    ]
}
```

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a service\-linked role for API Gateway<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you create an API, custom domain name, or VPC link in the AWS Management Console, the AWS CLI, or the AWS API, API Gateway creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you create an API, custom domain name, or VPC link, API Gateway creates the service\-linked role for you again\. 

## Editing a service\-linked role for API Gateway<a name="edit-slr"></a>

API Gateway does not allow you to edit the AWSServiceRoleForAPIGateway service\-linked role\. After you create a service\-linked role, you can't change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a service\-linked role for API Gateway<a name="delete-slr"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don't have an unused entity that is not actively monitored or maintained\. However, you must clean up the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the API Gateway service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To delete API Gateway resources used by the AWSServiceRoleForAPIGateway**

1. Open the API Gateway console at [https://console\.aws\.amazon\.com/apigateway/](https://console.aws.amazon.com/apigateway/)\. 

1. Navigate to the API, custom domain name, or VPC link that uses the service\-linked role\.

1. Use the console to delete the resource\.

1. Repeat the procedure to delete all APIs, custom domain names, or VPC links that use the service\-linked role\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the AWSServiceRoleForAPIGateway service\-linked role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

## Supported Regions for API Gateway service\-linked roles<a name="slr-regions"></a>

API Gateway supports using service\-linked roles in all of the Regions where the service is available\. For more information, see [AWS Service Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.