# Use the serverless developer portal to catalog your API Gateway APIs<a name="apigateway-developer-portal"></a>

A *developer portal* is an application that you use to make your APIs available to your customers\. You can use the Serverless Developer Portal to publish API Gateway managed APIs directly from API Gateway\. You can also use it to publish non\-API Gateway managed APIs by uploading OpenAPI definitions for them\. When you publish APIs in a developer portal, your customers can easily:
+ Discover which APIs are available\.
+ Browse your API documentation\.
+ Register for—and immediately receive—their own API key that can be used to build applications\.
+ Try out your APIs in the developer portal UI\.
+ Monitor their own API usage\.

Amazon API Gateway publishes a regularly updated Serverless Developer Portal application in the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) and on [GitHub](https://github.com/awslabs/aws-api-gateway-developer-portal)\. It is released under the [Apache 2\.0 license](http://www.apache.org/licenses/LICENSE-2.0.html), which lets you customize and incorporate it into your build and deployment tools\. The front end is written in React and is designed to be fully customizable\. See [https://github\.com/awslabs/aws\-api\-gateway\-developer\-portal/wiki/Customization](https://github.com/awslabs/aws-api-gateway-developer-portal/wiki/Customization)\.

For more information about the AWS Serverless Application Repository, see the [AWS Serverless Application Repository Developer Guide](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/)\.

**Tip**  
If you'd like to try out an example developer portal, see [https://developer\.exampleapigateway\.com/](https://developer.exampleapigateway.com/)\.

**Topics**
+ [Create a developer portal](#apigateway-developer-portal-create)
+ [Developer portal settings](#apigateway-developer-portal-settings)
+ [Create an admin user for your developer portal](#apigateway-developer-portal-create-admin)
+ [Manage users for your developer portal](#apigateway-developer-portal-manage-users)
+ [Publish an API Gateway managed API to your developer portal](#apigateway-developer-portal-publish)
+ [Update or delete an API Gateway managed API](#apigateway-developer-portal-update)
+ [Remove a non\-API Gateway managed API](#apigateway-developer-portal-update-generic)
+ [Publish a non\-API Gateway managed API to your developer portal](#apigateway-developer-portal-publish-generic)
+ [How your customers use your developer portal](#apigateway-developer-portal-get-api-key)
+ [Best practices for developer portals](#apigateway-developer-portal-best-practices)

## Create a developer portal<a name="apigateway-developer-portal-create"></a>

You can deploy the API Gateway Serverless Developer Portal as\-is or customize it to fit your branding\. There are two ways to deploy your developer portal:
+ By using the AWS Serverless Application Repository\. Choose the **Deploy** button to launch the API Gateway Serverless Developer Portal AWS CloudFormation stack and enter a handful of stack parameters in the Lambda console\.
+ By downloading the API Gateway Serverless Developer Portal from the AWS GitHub repository and launching the API Gateway Serverless Developer Portal AWS CloudFormation stack from the AWS SAM CLI\.

**To deploy the serverless developer portal using the AWS Serverless Application Repository**

1. Go to the [API Gateway Serverless Developer Portal](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:563878140293:applications~api-gateway-dev-portal) page in the AWS Serverless Application Repository\.

1. Choose **Deploy**\.
**Note**  
You might be prompted to sign into the AWS Lambda console\.

1. In the Lambda console, enter the required [developer portal stack parameters](#apigateway-developer-portal-settings) under **Application settings**\.
**Note**  
The S3 bucket names and the Amazon Cognito domain prefix are required; the other settings are optional\.

1. If you want to enable the **Got an opinion?** customer feedback button, you need to:
   + Enter an email address in the **DevPortalAdminEmail** box\. When a customer submits feedback, email is sent to this address\.
**Note**  
If you don't supply an email address, the **Got an opinion?** doesn't appear in the developer portal\.
   + You can optionally enter a table name in the **DevPortalFeedbackTableName** box\. The default name is **DevPortalFeedback**\. When a customer submits feedback, it is stored in a DynamoDB table with this name\.

1. Select the check box next to **I acknowledge that this app creates custom IAM roles and resource policies**\.

1. Choose **Deploy**\.

1. If you entered an email address in the **AdminEmail** stack parameter, an Amazon SNS subscription email is sent to that email address\. This email confirms your subscription to the Amazon SNS topic that you specified in the **MarketplaceSubscriptionTopicProductCode** setting\.

**To download and deploy the serverless developer portal using AWS SAM**

1. Ensure that you have the latest [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/) and [AWS SAM CLI](https://github.com/awslabs/aws-sam-cli) installed and configured\.

1. Download or clone the [API Gateway Serverless Developer Portal](https://github.com/awslabs/aws-api-gateway-developer-portal) repository\.

1. Ensure that you have a private Amazon S3 bucket where you can upload the zipped Lambda functions\. If you don't have one, you can create one using the Amazon S3 console or CLI\.

   In the AWS SAM CLI, run the following command from the project root, replacing `{your-lambda-artifacts-bucket-name}` with the name of your Amazon S3 bucket:

   ```
   sam package --template-file ./cloudformation/template.yaml \
       --output-template-file ./cloudformation/packaged.yaml \
       --s3-bucket {your-lambda-artifacts-bucket-name}
   ```

1. For this step, see [Developer portal settings](#apigateway-developer-portal-settings) for more information about parameters \(such as `CognitoDomainNameOrPrefix`\) that follow `--parameter-overrides`\.
**Note**  
Ensure that you have a private Amazon S3 bucket where you can upload the AWS SAM template\. \(AWS CloudFormation reads the template from the bucket to deploy the developer portal\.\) This can be the same bucket that you specified in the previous step for uploading the zipped Lambda functions\.

   Run the following command from the project root, replacing:
   + `{your-template-bucket-name}` with the name of your Amazon S3 bucket\.
   + `{custom-prefix}` with a prefix that is globally unique\.
   + `{cognito-domain-or-prefix}` with a unique string\.

   ```
   sam deploy --template-file ./cloudformation/packaged.yaml \
       --s3-bucket {your-template-bucket-name} \
       --stack-name "{custom-prefix}-dev-portal" \
       --capabilities CAPABILITY_NAMED_IAM \
       --parameter-overrides CognitoDomainNameOrPrefix= "{cognito-domain-or-prefix}" DevPortalSiteS3BucketName="{custom-prefix}-dev-portal-static-assets" ArtifactsS3BucketName="{custom-prefix}-dev-portal-artifacts"
   ```

After your developer portal has been fully deployed, you can get its URL as follows\.

**To get the URL for your newly created developer portal**

1. Open the AWS CloudFormation console\.

1. Choose the name of the stack \(`aws-serverless-repository-api-gateway-dev-portal` is the default stack name\)\.

1. Open the `Outputs` section\. The URL for the developer portal is specified in the `WebsiteURL` property\.

## Developer portal settings<a name="apigateway-developer-portal-settings"></a>

The following are settings you need to set during and after deployment of your developer portal:

**Note**  
Many of the settings below can be changed after you've deployed your developer portal, by updating the AWS CloudFormation stack\. For more information, see [AWS CloudFormation Stack Updates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks.html)\.

**ArtifactsS3BucketName \(REQUIRED\)**  
The deployment process creates a privately accessible Amazon S3 bucket where the catalog metadata is stored\. Specify a name to assign to the bucket\. This name must be globally unique\.

**CognitoDomainNameOrPrefix \(REQUIRED\)**  
This string is used with the Amazon Cognito hosted UI for user sign\-up and sign\-in\. Specify a unique domain name or prefix string\.

**CognitoIdentityPoolName**  
The deployment process creates an Amazon Cognito identity pool\. The default name for the identity pool is **DevPortalIdentityPool**\.

**CustomDomainNameAcmCertArn**  
If you provided a domain name associated with an ACM certificate, then you must also specify here the ACM certificate's ARN\. Leave this blank to create a developer portal without a custom domain name\.

**DevPortalAdminEmail**  
Specify an email address to enable the **Got an opinion?** customer feedback button\. When a customer submits feedback, email is sent to this address\.  
If you don't supply an email address, the **Got an opinion?** button doesn't appear in the developer portal\.

**DevPortalFeedbackTableName**  
If you specify an email address for **DevPortalAdminEmail**, the deployment process creates a DynamoDB table to store feedback entered by customers\. The default name is **DevPortalFeedback**\. You can optionally specify your own table name\.

**DevPortalCustomersTableName**  
The deployment process creates a DynamoDB table where customer accounts are stored\. You can optionally specify a name to assign to this table\. It must be unique within your account Region\. The default name of the table is **DevPortalCustomers**\.

**DevPortalSiteS3BucketName \(REQUIRED\)**  
The deployment process creates a privately accessible Amazon S3 bucket where the web application code is stored\. Specify a name to assign to this bucket\. This name must be globally unique\.

**MarketplaceSubscriptionTopicProductCode**  
This is the Amazon SNS topic suffix for subscribe/unsubscribe events\. Enter the desired value\. The default value is **DevPortalMarketplaceSubscriptionTopic**\. This setting is only relevant if you're using AWS Marketplace integration\. For more information, see [Configuring Your SaaS Product to Accept New Customers](https://docs.aws.amazon.com/marketplace/latest/userguide/configuring-your-saas-application-to-accept-new-customers.html#in-your-application)\.

**StaticAssetRebuildMode**  
By default, a static asset rebuild doesn't overwrite custom content\. Specify **overwrite\-content** to replace the custom content with your local version\.  
If you specify **overwrite\-content**, all custom changes in your Amazon S3 bucket will be lost\. Don't do this unless you know what you're doing\.

**StaticAssetRebuildToken**  
Provide a token different from the last deployment's token to re\-upload the developer portal site's static assets\. You can provide a timestamp or GUID on each deployment to always re\-upload the assets\.

**UseRoute53Nameservers**  
Only applicable if you're creating a custom domain name for your developer portal\. Specify **true** to skip creating a Route 53 HostedZone and RecordSet\. You'll need to provide your own name server hosting in place of Route 53\. Otherwise, leave this field set to **false**\.

## Create an admin user for your developer portal<a name="apigateway-developer-portal-create-admin"></a>

After you deploy your developer portal, create at least one admin user\. You do that by creating a new user and adding that user to the administrator group for the Amazon Cognito user pool that AWS CloudFormation created when you deployed the developer portal\.

**Create an admin user**

1. In your developer portal, choose **Register**\.

1. Enter an email address and password and choose **Register**\.

1. In a separate browser tab, sign in to the Amazon Cognito console\.

1. Choose **Manage User Pools**\.

1. Choose the user pool for your developer portal that you set when you deployed the developer portal\.

1. Choose the user to promote to administrator\.

1. Choose **Add to group**\.

1. From the dropdown menu, choose the ***\{stackname\}*\-dev\-portalAdminsGroup**, where *\{stackname\}* is the stack name from when you deployed the developer portal\.

1. Choose **Add to group**\.

1. In your developer portal, log out and log back in using the same credentials\. You should now see an **Admin Panel** link in the upper right corner, next to **My Dashboard**\.

## Manage users for your developer portal<a name="apigateway-developer-portal-manage-users"></a>

After you sign in as an admin user, you can use the **Accounts** section of the **Admin Panel** to manage users\. An admin user can invite new users, delete users, and promote existing users to admin status from the developer portal\.

## Publish an API Gateway managed API to your developer portal<a name="apigateway-developer-portal-publish"></a>

The following steps outline how you, as the API owner, publish an API to your developer portal so that your customers can subscribe to it\.

**Step 1: Make an API Gateway managed API available for publishing**

1. If you haven't already done so, [deploy the API to a stage](how-to-deploy-api-with-console.md)\.

1. If you haven't already done so, create a [usage plan](api-gateway-api-usage-plans.md) and associate it with the API stage for the deployed API\.
**Note**  
You don't need to associate API keys with the usage plan\. The developer portal does this for you\.

1. Sign in to the **Developer Portal** and go to the **Admin Panel**\.

1. You should see the API in the **Admin Panel** API list\. In the API list, APIs are grouped by usage plan\. The **Not Subscribable *No Usage Plan*** group lists the APIs that aren't associated with a usage plan\.

**Step 2: Make the API Gateway managed API visible in the developer portal**

1. In the **Admin Panel** API list, check the **Displayed** value for your API\. When it's first uploaded, this value is set to **False**\.

1. To make the API visible in the developer portal, choose the **False** button\. Its value changes to **True**, which indicates that the API is now visible\.
**Tip**  
To make all APIs in a usage plan visible, choose the **False** or **Partial** button in the header bar for the usage plan\.

1. Navigate to the **APIs** panel to see the developer portal as your customers see it\. You should see your API listed in the left navigation column\.

   If the API is deployed to a stage that's associated with a usage plan, you should see a **Subscribe** button for the API\. This button causes the customer's API key to be associated with the usage plan that the API is associated with\.

Now that the API Gateway managed API is displayed, you can enable SDK generation so that your customers are able to download an SDK for it\.

**Step 3: Enable SDK generation**

1. In the **Admin Panel** API list, choose the **Disabled** button\. Its value changes to **Enabled**, which indicates that SDK generation is now allowed\.

1. Navigate to the **APIs** panel and choose your API in the left navigation column\. You should now see **Download SDK** and **Export API** buttons\.

## Update or delete an API Gateway managed API<a name="apigateway-developer-portal-update"></a>

If you make changes to your API in API Gateway after you've published it, you need to redeploy it\.

**To update an API Gateway managed API**

1. Make the desired changes to the API in the API Gateway console, AWS CLI, or an SDK\.

1. Redeploy the API to the same stage as before\.

1. In the developer portal, in the **Admin Panel** API list, choose **Update**\. This updates the API that's displayed in the developer portal UI\.
**Note**  
The **Download SDK** button always gets the latest deployed version of your API, even if you don't update it in the developer portal\.

1. Navigate to the **APIs** panel and choose your API in the left navigation column\. You should see your changes\.

To stop displaying an API in the developer portal API list \(without revoking customers' access to it\):

1. In the **Admin Panel** API list, check the **Displayed** value for your API\. If the API is visible, this value is set to **True**\.

1. To make the API invisible, choose the **True** button\. Its value changes to **False**, which indicates that the API is now invisible\.

1. Navigate to the **APIs** panel\. You should no longer see your API listed in the left navigation column\.

To revoke customers' access to an API without deleting it entirely, you must do one of the following in the [API Gateway console](api-gateway-create-usage-plans-with-console.md) or the [API Gateway CLI or REST API](api-gateway-create-usage-plans-with-rest-api.md):
+ Remove the API keys from the usage plan\.
+ Remove the API stage from the usage plan\.

## Remove a non\-API Gateway managed API<a name="apigateway-developer-portal-update-generic"></a>

To stop displaying a non\-API Gateway managed API and remove customers' access to it, choose **Delete**\. This won't delete the API, but it will delete its OpenAPI specification file from the developer portal\.

## Publish a non\-API Gateway managed API to your developer portal<a name="apigateway-developer-portal-publish-generic"></a>

The following steps outline how you publish non\-API Gateway managed \(or "generic"\) APIs to your customers\. You can upload OpenAPI 2\.0 \(Swagger\) or 3\.x definitions for the APIs in `JSON`, `YAML`, or `YML` files\.

**To publish a non\-API Gateway managed API in your developer portal**

1. Sign in to the **Developer Portal** and go to the **Admin Panel**\.

1. Under **Generic APIs**, choose **Add API**\.

1. Browse to the directory where the OpenAPI files for your APIs are located, and choose the files to upload\.

1. Choose **Upload**\.
**Note**  
If any of the files can't be parsed or don't contain an API title, you'll get a warning, and those files won't be uploaded\. All other files will be uploaded\. You'll need to choose the **x** icon to dismiss the dialog\.

1. You should now see your API listed under **Generic APIs**\. If you don't, navigate away from the **Admin Panel** and back to it again to refresh the list\.

## How your customers use your developer portal<a name="apigateway-developer-portal-get-api-key"></a>

To build applications and test your APIs, your customers need to create developer accounts by registering with your developer portal\.

**To create a developer account and get an API key**

1. In the developer portal, choose **Register**\.

1. Enter an email address and password and choose **Register**\.

1. To locate the API key, choose **My Dashboard**\.

A developer account gives your customer an API key, which is typically needed to use and test your APIs\. It enables usage tracking for both you and your customers\.

When a customer first registers, their new API key won't be tied to any of your APIs\.

**To activate the API key for an API**

1. Choose **APIs**\.

1. Choose the API from the API list and choose **Subscribe**\.

   This causes the API key to be associated with the usage plan that the API is associated with\.

The customer has now subscribed to the API, and they can make calls to methods on the API\. Daily usage statistics for the API will be displayed in **MyDashboard**\.

A customer can try out API methods in the developer portal UI without subscribing\.

**Note**  
If any of the API's methods require an API key, an **Authorize** button will appear above the method list\. Methods that require an API key will have a black lock icon\. To try out methods that require an API key, choose the **Authorize** button and enter a valid API key that's associated with the usage plan for the API stage\.  
You can safely ignore the **Authorize** button when it appears on APIs that you're already subscribed to\.

Your customers can submit customer feedback through the developer portal\.

The customer's feedback is stored in the DynamoDB table for the developer portal\. The default name of this table is **DevPortalFeedback**\. Also, email is sent to the email addresses that was specified in the **DevPortalAdminEmail** field\. If no email address was specified when the developer portal was deployed, the **Got an opinion?** button does not appear\.

**Note**  
If you change the name of this table, you must use a name that is unique within your account Region\.

If you've enabled SDK generation for the API in the **Admin Panel**, the customer can download an SDK for it and export the API definition\. To learn more, see [Generating an SDK for a REST API in API Gateway](how-to-generate-sdk.md) and [Export a REST API from API Gateway](api-gateway-export-api.md)\.

## Best practices for developer portals<a name="apigateway-developer-portal-best-practices"></a>

The following are suggested best practices to follow when deploying a developer portal\.
+ In most cases, you'll only want to deploy one developer portal for all of your APIs\. In some cases, you might choose to have separate developer portals for development and production versions of your APIs\. We don't recommended using more than one developer portal for production APIs\.
+ When you create a usage plan and associate it with a stage, you don't need to associate any API keys with the usage plan\. The developer portal does this for you\.
+ Note that, if any APIs in a given usage plan are visible, *all* APIs in that usage plan are subscribable, even if you haven't made them visible to your customers\.