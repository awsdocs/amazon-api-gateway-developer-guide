# Set up a Stage Using the API Gateway Console<a name="stages"></a>

**Topics**
+ [Create a New Stage](#how-to-create-stage-console)
+ [Update Stage Settings](#how-to-stage-settings)
+ [Delete a Stage for an API](#how-to-delete-stage)

## Create a New Stage<a name="how-to-create-stage-console"></a>

 After the initial deployment, you can add more stages and associate them with existing deployments\. You can use the API Gateway console to create and use a new stage or choose an existing stage while deploying an API\. In general, you can add a new stage to an API deployment before redeploying the API\. To do so using the API Gateway console, follow the instructions below\. 

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. From the **APIs** navigation pane, choose **Stages** under an API\.

1. From the **Stages** navigation pane, choose **Create**\.

1.  Under **Create Stage**, type a stage name, e\.g\., `prod`, for **Stage name**\. 

1.  Optionally, type a stage description for **Stage description** 

1. From the **Deployment** drop\-down list, choose the date and time of the existing API deployment you want to associate with this stage\.

1. Choose **Create**\.

## Update Stage Settings<a name="how-to-stage-settings"></a>

After a successful deployment of an API, the stage is populated with default settings\. You can use the console or API Gateway REST API to change the stage settings, including API caching and logging\. In the following, we show how to do so using the **Stage Editor** of the API Gateway console\.

### Update Stage Settings Using the API Gateway Console<a name="how-to-stage-settings-console"></a>

 Make sure that you have already deployed the API and created a stage at least once before proceeding\. 

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the **APIs** pane, choose the API you want to update the stage settings, and then choose **Stages**\.

1. In the **Stages** pane, choose the name of the stage\.

1. In the **Stage Editor** pane, choose the **Settings** tab\.

1.  To enable API caching, select the **Enable API cache** option under the **Cache Settings** section\. Then, choose desired options and associated values for **Cache capacity**, **Encrypt cache data**, **Cache time\-to\-live \(TTL\)**, as well as the requirements for per\-key cache invalidation\. For more information about the stage\-level cache settings, see [Enable API Caching](api-gateway-caching.md)\. 
**Important**  
By selecting this option, your AWS account may be charged for API caching\.
**Tip**  
You can override enabled stage\-level cache settings\. To do so, expand the stage under the **Stages** secondary navigation pane and choose a method\. Then, in the stage editor, choose the **Override for this method** option for **Settings**\. In the ensuing **Cache Settings** area, clear **Enable Method Cache** or customize any other desired options, before choosing **Save Changes**\. For more information about the method\-level cache settings, see [Enable API Caching](api-gateway-caching.md)\.

1. To enable Amazon CloudWatch Logs for all of the methods associated with this stage of this API Gateway API, do the following:

   1.  Under the **CloudWatch Settings** section, select the **Enable CloudWatch Logs** option\. 
**Tip**  
 To enable method\-level CloudWatch settings, expand the stage under the **Stages** secondary navigation pane, choose each method of interest, and, back in the stage editor, choose **Override for this method** for **Settings**\. In the ensuing **CloudWatch Settings** area, make sure to select **Log to CloudWatch Logs** and any other desired options, before choosing **Save Changes**\. 
**Important**  
Your account will be charged for accessing method\-level CloudWatch metrics, but not the API\- or stage\- level metrics\.

   1. For **Log level**, choose **ERROR** to write only error\-level entries to CloudWatch Logs, or choose **INFO** to include all **ERROR** events as well as extra informational events\. 

   1. To log full API call request and response information, select **Log full requests/responses data**\. No sensitive data will be logged unless the **Log full requests/responses data** option is selected\.

   1. To have API Gateway to report to CloudWatch the API metrics of `API calls`, `Latency`, `Integration latency`, `400 errors` and `500 errors`, select the **Enable Detailed CloudWatch Metrics** option\. For more information about CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)\.

   1. Choose **Save Changes**\. The new settings will take effect after a new deployment\.
**Important**  
To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your IAM user\. To do so, choose **Settings** from the **APIs** main navigation pane\. Then type the ARN of an IAM role in the **CloudWatch log role ARN** text field\. For common application scenarios, the IAM role could attach the managed policy of `AmazonAPIGatewayPushToCloudWatchLogs`, which contains the following access policy statement:  

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
For more information about CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)\.

1.  To set the stage\-level throttle limit for all of the methods associated with this API, do the following in the **Default Method Throttling** section: 

   1.  For **Rate**, type the maximum number of stage\-level steady\-state requests per second that API Gateway can serve without returning a `429 Too Many Requests` response\. This stage\-level rate limit must not be more than the [account\-level](api-gateway-request-throttling.md#apig-request-throttling-account-level-limits) rate limit as specified in [API Gateway Limits for Configuring and Running an API](limits.md#api-gateway-execution-service-limits-table)\. 

   1.  For **Burst**, type the maximum number of stage\-level concurrent requests that API Gateway can serve without returning a `429 Too Many Requests` response\. This stage\-level burst must not be more than the [account\-level](api-gateway-request-throttling.md#apig-request-throttling-account-level-limits) burst limit as specified in [API Gateway Limits for Configuring and Running an API](limits.md#api-gateway-execution-service-limits-table)\. 

1.  To override the stage\-level throttling for individual methods, expand the stage under the **Stages** secondary navigation pane, choose a method of interest, and, back in the stage editor, choose **Override for this method** for **Settings**\. In the **Default Method Throttling** area, select appropriate options\. 

## Delete a Stage for an API<a name="how-to-delete-stage"></a>

When you no longer need a stage, you can delete it to avoid paying for unused resources\. In the following, we explain how to use the API Gateway console to delete a stage \.

**Warning**  
 Deleting a stage may cause part or all of the corresponding API to be unusable by API callers\. Deleting a stage cannot be undone, but you can recreate the stage and associate it with the same deployment\. 

### Delete a Stage with the API Gateway Console<a name="how-to-delete-stage-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the box that contains the name of the API for the stage, choose **Stages**\.

1. In the **Stages** pane, choose the stage you want to delete, and then choose **Delete Stage**\.

1. When prompted, choose **Delete**\.