# Getting Started with Amazon API Gateway<a name="getting-started"></a>

 With Amazon API Gateway, you can provide your clients with a consistent and scalable programming interface to access three types of endpoints in the backend: invoking AWS Lambda functions, calling other AWS services, and accessing an HTTP website or webpage\. To do this, you create an API Gateway API to integrate each API method with a backend endpoint\. Each backend endpoint is associated with an integration type\. For details about the API integration types in API Gateway, see [Choose an API Gateway API Integration Type](api-gateway-api-integration-types.md)\. 

 To get started using Amazon API Gateway, we present the following hands\-on walkthroughs for creating, deploying, and testing simple APIs integrated with some commonly used backends\. The example APIs used in the walkthroughs demonstrate what is involved to implement each of the supported integration types\. 

**Topics**
+ [Get Ready to Build an API Gateway API](#setting-up)
+ [Build an API Gateway API from an Example](api-gateway-create-api-from-example.md)
+ [Build an API Gateway API with Lambda Integration](getting-started-with-lambda-integration.md)
+ [Build an API Gateway API with HTTP Integrations](getting-started-http-integrations.md)
+ [Build an API with API Gateway Private Integration](getting-started-with-private-integration.md)
+ [Build an API Gateway API with AWS Integration](getting-started-aws-proxy.md)

 Before you start, use the following procedures to set up your development environment\. 

## Get Ready to Build an API Gateway API<a name="setting-up"></a>

**Topics**
+ [Sign up for an AWS Account](#setting-up-sign-up)
+ [Create IAM Users, Groups, Roles, and Policies in Your AWS Account](#setting-up-iam)
+ [Create IAM Policies to Configure API Gateway Resources and to Call a Deployed API](#setting-up-permissions)
+ [Next Step](#setting-up-next-step)

 Before using Amazon API Gateway for the first time, you must have an AWS account\. 

### Sign up for an AWS Account<a name="setting-up-sign-up"></a>

If you do not have an AWS account, use the following procedure to create one\.

**To sign up for AWS**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/) and choose **Create an AWS Account**\.

1. Follow the online instructions\.

 To create, configure, and deploy an API in API Gateway, you must have an appropriate AWS Identity and Access Management policy provisioned\. The policy must have access permissions for manipulating the API Gateway [resources](https://docs.aws.amazon.com/apigateway/api-reference/resource/) and [link relations](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/)\. In addition, you can set IAM permissions to allow your API clients to call your API in API Gateway\. To do so, create IAM roles and policies and, optionally, users or groups in your AWS account, and set the IAM roles and policies on a specified IAM user or group\. 

### Create IAM Users, Groups, Roles, and Policies in Your AWS Account<a name="setting-up-iam"></a>

 For better security practices, you should create a new AWS Identity and Access Management \(IAM\) user or use an existing one in your AWS account\. You then access API Gateway with that IAM user's credentials, instead of using your AWS root account\. 

 To manage access for a user, create an IAM user and grant the user API Gateway access permissions\. To create a new IAM user, see [Creating an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html#Using_CreateUser_console)\. 

 To manage access for a group of users, create an IAM group, grant the group API Gateway access permissions, and then add one or more IAM users to the group\. To create an IAM group, see [Creating IAM Groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups_create.html)\. 

 To delegate access to specific users, apps, or services, create an IAM role, add the specified users or groups to the role, and grant the users or groups API Gateway access permissions\. To create an IAM role, see [Creating IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)\. 

 When setting up your API, specify the ARN of an IAM role to control access the API's methods\. This ARN must be ready when creating an API\. 

### Create IAM Policies to Configure API Gateway Resources and to Call a Deployed API<a name="setting-up-permissions"></a>

 In AWS, access permissions are stated as [IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html)\. AWS provides a set of pre\-configured IAM policies, known as AWS managed policies, for individual AWS services\. Individual IAM users can create customized IAM policies, known as customer managed policies\. 

 You can create an IAM policy, role, user, or group in the IAM console or by using the AWS CLI or an AWS SDK\. Once created, IAM policies are referenced by their ARNs\. The ARN of a policy that is managed by AWS is of the `arn:aws:iam::aws:policy/PolicyName` format\. The ARN of a customer managed policy is of the `arn:aws:iam::123456789012:policy/PolicyName` format\. 

 For example, the following is the AWS managed policy, named **AmazonAPIGatewayAdministrator** \(`arn:aws:iam::aws:policy/AmazonAPIGatewayAdministrator`\)\. It grants full access to create, configure, and deploy an API in API Gateway: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "apigateway:*"
      ],
      "Resource": "arn:aws:apigateway:*::/*"
    }
  ]
}
```

 To grant the permissions to a user, attach the policy to the user or a group containing the user\. For more information, see [Attaching Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console)\. 

 Attaching the preceding policy to an IAM user allows \(`"Effect":"Allow"`\) the user to act with any API Gateway actions \(`"Action":["apigateway:*"]`\) on any API Gateway resources \(`arn:aws:apigateway:*::/*`\) that are associated with the user's AWS account\. 

 To restrict the IAM user to read and create documentation of created APIs, you can change the `Action` property value from `"Action": ["apigateway:*"]` to `"Action":["apigateway:GET", "apigateway:POST"]` and change the `Resource` property value from `"arn:aws:apigateway:*::/*"` to `"arn:aws:apigateway::123456789012:/restapis/*/documentation/*"`\. For more information, see [Control Access to an API with IAM Permissions](permissions.md)\. 

To control how an API is invoked, the following AWS managed IAM policy of **AmazonAPIGatewayInvokeFullAccess** \(` arn:aws:iam::aws:policy/AmazonAPIGatewayInvokeFullAccess`\) provides full access to invoke any part of an API in API Gateway: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "execute-api:Invoke"
      ],
      "Resource": "arn:aws:execute-api:*:*:*"
    }
  ]
}
```

 To learn how to restrict IAM users to call a specified set of API parts, see [Control Access to an API with IAM Permissions](permissions.md)\. 

 To grant the stated permissions to a user, attach the policy to the user or a group containing the user\. To attach a policy, see [Attaching Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console)\. 

 In this documentation, we use managed policies whenever possible\. To create and use customer managed IAM policies, see [Working with Customer Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html)\. 

**Note**  
To complete the preceding steps, you must have permissions to create the IAM policy and attach it to the IAM user\. 

 When API Gateway is integrated with AWS Lambda or another AWS service, such as Amazon Simple Storage Service or Amazon Kinesis, you must also enable API Gateway as a trusted entity to invoke an AWS service in the backend\. To do so, create an IAM role and attach a service\-specific access policy to the role\. This is demonstrated in the following example for invoking a Lambda function: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "*"
        }
    ]
}
```

Next, add the following trust policy to allow API Gateway to call the backend Lambda function on behalf of the attached user who is assigned the IAM role\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

 Without specifying this trust relationship, API Gateway is denied the right to call the backend on behalf of the user, even when the user has been granted permissions to access the backend directly\. 

 When an API Gateway API is set up with IAM roles and policies to control client access, the client must sign API requests with [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html)\. Alternatively, you can use the AWS CLI or one of the AWS SDKs to handle request signing for you\. For more information, see [Invoking an API in Amazon API Gateway](how-to-call-api.md)\. 

### Next Step<a name="setting-up-next-step"></a>

Having signed up for an AWS account and created the required IAM roles and policies, you are now ready to start using API Gateway\. To create your first simple API by using an example in the API Gateway console, see [Build an API Gateway API from an Example](api-gateway-create-api-from-example.md)\.