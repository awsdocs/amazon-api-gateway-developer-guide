# Setting up a stage using the API Gateway console<a name="stages"></a>

**Topics**
+ [Create a new stage](#how-to-create-stage-console)
+ [Update stage settings](#how-to-stage-settings)
+ [Delete a stage](#how-to-delete-stage)

## Create a new stage<a name="how-to-create-stage-console"></a>

 After the initial deployment, you can add more stages and associate them with existing deployments\. You can use the API Gateway console to create a new stage, or you can choose an existing stage while deploying an API\. In general, you can add a new stage to an API deployment before redeploying the API\. To create a new stage using the API Gateway console, follow these steps:\. 

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. From the **APIs** navigation pane, choose **Stages** under an API\.

1. From the **Stages** navigation pane, choose **Create**\.

1.  Under **Create Stage**, enter a stage name, for example, **prod**, for **Stage name**\. 
**Note**  
Stage names can only contain alphanumeric characters, hyphens, and underscores\. Maximum length is 128 characters\.

1.  Optionally, enter a stage description for **Stage description**\. 

1. From the **Deployment** drop\-down list, choose the date and time of the existing API deployment you want to associate with this stage\.

1. Choose **Create**\.

## Update stage settings<a name="how-to-stage-settings"></a>

After a successful deployment of an API, the stage is populated with default settings\. You can use the console or the API Gateway REST API to change the stage settings, including API caching and logging\. The following steps show you how to do so using the **Stage Editor** of the API Gateway console\.

### Update stage settings using the API Gateway console<a name="how-to-stage-settings-console"></a>

These steps assume that you've already deployed the API to a stage\. 

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the **APIs** pane, choose the API, and then choose **Stages**\.

1. In the **Stages** pane, choose the name of the stage\.

1. In the **Stage Editor** pane, choose the **Settings** tab\.

1. To enable API caching for the stage, select the **Enable API cache** option under the **Cache Settings** section\. Then choose the desired options and associated values for **Cache capacity**, **Encrypt cache data**, **Cache time\-to\-live \(TTL\)**, as well as any requirements for per\-key cache invalidation\.

   For more information about stage\-level cache settings, see [Enabling API caching to enhance responsiveness](api-gateway-caching.md)\.
**Important**  
If you enable API caching for an API stage, your AWS account might be charged for API caching\. Caching isn't eligible for the AWS Free Tier\.
**Tip**  
You can also override enabled stage\-level cache settings for individual methods\. To do so, expand the stage under the **Stages** secondary navigation pane, and choose a method\. Then, in the stage editor, choose the **Override for this method** option for **Settings**\. In the **Cache Settings** area, you can set or clear **Enable Method Cache**, or customize any other desired options\. For more information about the method\-level cache settings, see [Enabling API caching to enhance responsiveness](api-gateway-caching.md)\.

1. To enable Amazon CloudWatch Logs for all of the methods associated with this stage of this API Gateway API, do the following:

   1.  Under the **CloudWatch Settings** section, select the **Enable CloudWatch Logs** option\. 
**Tip**  
 To enable method\-level CloudWatch settings, expand the stage under the **Stages** secondary navigation pane, and choose each method of interest\. Back in the stage editor, choose **Override for this method** for **Settings**\. In the **CloudWatch Settings** area, make sure to select **Log to CloudWatch Logs** and any other desired options before choosing **Save Changes**\. 
**Important**  
Your account is charged for accessing method\-level CloudWatch metrics, but not the API\-level or stage\-level metrics\.

   1. For **Log level**, choose **ERROR** to write only error\-level entries to CloudWatch Logs, or choose **INFO** to include all **ERROR** events, as well as extra informational events\. 

   1. To log full API call request and response information, select **Log full requests/responses data**\. No sensitive data is logged unless the **Log full requests/responses data** option is selected\.
**Important**  
Setting logs to **ERROR** and then choosing **Log full requests/responses data** results in every request being logged in detail\. This is the intended behavior\.

   1. To have API Gateway report to CloudWatch the API metrics of `API calls`, `Latency`, `Integration latency`, `400 errors`, and `500 errors`, choose the **Enable Detailed CloudWatch Metrics** option\. For more information about CloudWatch, see the *[Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)*\.

   1. Choose **Save Changes**\. The new settings take effect after a new deployment\.
**Important**  
To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your IAM user\. To do so, choose **Settings** from the **APIs** main navigation pane\. Then enter the ARN of an IAM role in the **CloudWatch log role ARN** text field\.   
For common application scenarios, the IAM role could attach the managed policy of `AmazonAPIGatewayPushToCloudWatchLogs`, which contains the following access policy statement:  

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "logs:CreateLogGroup",
                      "logs:CreateLogStream",
                      "logs:DescribeLogGroups",
                      "logs:DescribeLogStreams",
                      "logs:PutLogEvents",
                      "logs:GetLogEvents",
                      "logs:FilterLogEvents"
                  ],
                  "Resource": "*"
              }
          ]
      }
      ```
The IAM role must also contain the following trust relationship statement:  

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
For more information about CloudWatch, see the *[Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)*\.

1. To enable access logging to a Kinesis Data Firehose delivery stream:

   1. Under **Custom Access Logging**, choose **Enable Access Logging**\.

   1. In **Access Log Destination ARN**, enter the ARN of a Kinesis Data Firehose delivery stream\. The ARN format is `arn:aws:firehose:{region}:{account-id}:deliverystream/amazon-apigateway-{your-delivery-stream-name}`\.
**Note**  
The name of your Kinesis Data Firehose delivery stream must be `amazon-apigateway-{your-delivery-stream-name}`\.

   1. In **Log Format**, enter a log format\. To use one of the provided examples as a guide, you can choose **CLF**, **JSON**, **XML**, or **CSV**\.

   1. Choose **Save Changes**\.

1. To set stage\-level throttling limits for all of the methods associated with this API, do the following in the **Default Method Throttling** section: 

   1. Choose **Enable throttling**\.

   1. For **Rate**, enter the maximum number of stage\-level steady\-state requests per second that API Gateway can serve without returning a `429 Too Many Requests` response\. This stage\-level rate limit must not be more than the [account\-level](api-gateway-request-throttling.md#apig-request-throttling-account-level-limits) rate limit as specified in [API Gateway quotas for configuring and running a REST API](limits.md#api-gateway-execution-service-limits-table)\. 

   1. For **Burst**, enter the maximum number of stage\-level concurrent requests that API Gateway can serve without returning a `429 Too Many Requests` response\. This stage\-level burst must not be more than the [account\-level](api-gateway-request-throttling.md#apig-request-throttling-account-level-limits) burst limit as specified in [API Gateway quotas for configuring and running a REST API](limits.md#api-gateway-execution-service-limits-table)\. 

1.  To override the stage\-level throttling for an individual method, expand the stage in the **Stages** secondary navigation pane, choose a method, and choose **Override for this method** for **Settings**\. In the **Method Throttling** section, select the appropriate options\. 

1. To associate an AWS WAF web ACL with the stage, choose a web ACL from the **Web ACL** dropdown list\.
**Note**  
If needed, choose **Create Web ACL** to open the AWS WAF console in a new browser tab, create the web ACL, and return to the API Gateway console to associate the web ACL with the stage\.

1. If desired, choose **Block API Request if WebACL cannot be evaluated \(Fail\- Close\)**\.

1. To enable [AWS X\-Ray](https://docs.aws.amazon.com/xray/latest/devguide/xray-services-apigateway.html) tracing for the API stage:

   1. In the **Stage Editor** pane, choose the **Logs/Tracing** tab\.

   1. To enable X\-Ray tracing, choose **Enable X\-Ray Tracing** under **X\-Ray Tracing**\.

   1. To set sampling rules in the X\-Ray console, choose **Set X\-Ray Sampling Rules**\.

   1. If desired, choose **Set X\-Ray Sampling Rules** and go to the X\-Ray console to [configure sampling rules](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)\.

   For more information, see [Tracing user requests to REST APIs using X\-Ray](apigateway-xray.md)\.

1. Choose **Save Changes**\. The new settings take effect after you redeploy the API to the stage\.

## Delete a stage<a name="how-to-delete-stage"></a>

When you no longer need a stage, you can delete it to avoid paying for unused resources\. The following steps show you how to use the API Gateway console to delete a stage\.

**Warning**  
 Deleting a stage might cause part or all of the corresponding API to be unusable by API callers\. Deleting a stage cannot be undone, but you can recreate the stage and associate it with the same deployment\. 

### Delete a stage using the API Gateway console<a name="how-to-delete-stage-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose a REST API\.

1. Choose **Stages**\.

1. In the **Stages** pane, choose the stage you want to delete, and then choose **Delete Stage**\.

1. When you're prompted, choose **Delete**\.