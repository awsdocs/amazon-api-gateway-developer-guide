# Use the Serverless Developer Portal to Catalog Your API Gateway APIs<a name="apigateway-developer-portal"></a>

A *developer portal* is an application that you use to make your API Gateway APIs available to your customers by enabling self\-service discovery of those APIs\. Your customers can use the developer portal to browse API documentation, register for – and immediately receive – their own API key that can be used to build applications, test published APIs, and monitor their own API usage\.

Amazon API Gateway publishes a regularly updated serverless developer portal in the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/) and [on GitHub](https://github.com/awslabs/aws-api-gateway-developer-portal)\. It is released under the [Apache 2\.0 license](http://www.apache.org/licenses/LICENSE-2.0.html), letting you customize and incorporate it into your build and deployment tools\.

For more information about the AWS Serverless Application Repository, see the [AWS Serverless Application Repository Developer Guide](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/)\.

## Create a developer portal<a name="apigateway-developer-portal-create"></a>

You can deploy the API Gateway developer portal as\-is or customize it to fit your branding\.

**To deploy the serverless developer portal using the AWS Serverless Application Repository**

1. Go to the [API Gateway serverless developer portal](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:563878140293:applications~api-gateway-dev-portal) page in the AWS Serverless Application Repository\.

1. Choose **Deploy**\.
**Note**  
You may be prompted to sign into the AWS Lambda console\.

1. The deployment process will create an Amazon S3 bucket the catalog metadata will be stored\. In the **ArtifactsS3BucketName** box, enter the name to assign to this bucket\. This name must be globally unique\.

1. The deployment process will also create an Amazon S3 bucket where the web application code will be stored\. In the **DevPortalSiteS3BucketName** box, enter the name to assign to this bucket\. This name must be globally unique\.

1. You should leave all the other settings as\-is unless you need to change them\. For example, you can optionally enter a custom domain name in the **CustomDomainName** field\.

1. Choose the checkbox next to **I acknowledge that this app creates custom IAM roles**\.

1. Choose **Deploy**\.

**To download and deploy the serverless developer portal using AWS SAM**

1. Ensure that you have the latest [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/) and [AWS SAM CLI](https://github.com/awslabs/aws-sam-cli) installed and configured\.

1. Download or clone the [API Gateway serverless developer portal](https://github.com/awslabs/aws-api-gateway-developer-portal) repo to a local directory\.

1. Ensure that you have an Amazon S3 bucket to upload zipped Lambda functions into\. If you don't have one, you can create one using the Amazon S3 console or CLI\.

   In the SAM CLI, run the following command, replacing `{your-lambda-artifacts-bucket-name}` with the name of your Amazon S3 bucket:

   ```
   sam package --template-file ./cloudformation/template.yaml --output-template-file ./cloudformation/packaged.yaml --s3-bucket {your-lambda-artifacts-bucket-name}
   ```

1. Run the following command, replacing `{custom-prefix}` with a prefix that is globally unique:

   ```
   sam deploy --template-file ./cloudformation/packaged.yaml --stack-name "dev-portal" --capabilities CAPABILITY_NAMED_IAM --parameter-overrides DevPortalSiteS3BucketName="{custom-prefix}-dev-portal-static-assets" ArtifactsS3BucketName="{custom-prefix}-dev-portal-artifacts"
   ```

Once your developer portal has been fully deployed, you can get its URL as follows\.

**To get the URL for your newly created developer portal**

1. Open the AWS CloudFormation management console\.

1. Choose the name of the stack \(`aws-serverless-repository-api-gateway-dev-portal` is the default stack name\)\.

1. Open the `Outputs` section\. The URL for the developer portal is specified in the `WebSiteURL` property\.

## Publish an API Gateway API to your developer portal<a name="apigateway-developer-portal-publish"></a>

The following steps outline how you, as the API owner, publish an API to your customers\.

**To publish an API in your developer portal**

1. Create a [usage plan](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html) and associate it with the API and stage you want to publish\.

1. [Export](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-export-api.html#api-gateway-export-api-download-swagger-json) the API to an OpenAPI version 2 \(Swagger\) file \(as JSON, with API Gateway extensions\)\.
**Note**  
If you're using a custom domain for your APIs, you'll need to rename the JSON file to `{apiId}_{stageName}.json`, where `{apiId}` is the API ID of your API Gateway API and `{stageName}` is the name of the stage that the API is currently deployed to \(for example, `test` or `prod`\)\.

1. Upload the OpenAPI file to the the `catalog` folder in the Amazon S3 bucket whose name you supplied in the **ArtifactsS3BucketName** box when you deployed your developer portal\.

## How your customers use your developer portal<a name="apigateway-developer-portal-get-api-key"></a>

To build applications and test your APIs, your customers will need to create developer accounts by registering with your developer portal\.

**To create a developer account and get an API key**

1. In the developer portal, choose **Register**\.

1. Enter an email address and password and choose **Register**\.

1. To locate the API key, choose **My Dashboard**\.

A developer account gives your customer an API key, which is typically needed to use and test your APIs, and enables usage tracking for both you and your customers\.

When a customer first registers, their new API key won't be tied to any of your APIs\.

**To activate the API key for an API**

1. Choose **APIs**\.

1. Choose the API from the API list and choose **Subscribe**\.

The customer has now subscribed to the API, and they can make calls to all methods on the API\. Daily usage statistics for the API will be displayed in **MyDashboard**\.