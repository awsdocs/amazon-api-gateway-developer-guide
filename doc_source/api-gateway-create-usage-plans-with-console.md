# Create, Configure, and Test Usage Plans with the API Gateway Console<a name="api-gateway-create-usage-plans-with-console"></a>

Before creating a usage plan, make sure that you've set up the desired API keys\. For more information, see [Set Up API Keys Using the API Gateway Console](api-gateway-setup-api-key-with-console.md)\.

This section describes how to create and use a usage plan by using the API Gateway console\.

**Topics**
+ [Migrate Your API to Default Usage Plans \(If Needed\)](#api-gateway-usage-plan-migrate-to-default)
+ [Create Usage Plans](#api-gateway-usage-plan-create)
+ [Test a Usage Plan](#api-gateway-usage-plan-test-console)
+ [Maintain Plan Usage](#api-gateway-usage-plan-manage-usage)

## Migrate Your API to Default Usage Plans \(If Needed\)<a name="api-gateway-usage-plan-migrate-to-default"></a>

If you started to use API Gateway *after* the usage plans feature was rolled out on August 11, 2016, you will automatically have usage plans enabled for you in all supported regions\.

If you started to use API Gateway before that date, you may need to migrate to default usage plans\. You'll be prompted with the **Enable Usage Plans** option before using usage plans for the first time in the selected region\. When you enable this option, you have default usage plans created for every unique API stage that's associated with existing API keys\. In the default usage plan, no throttle or quota limits are set initially, and the associations between the API keys and API stages are copied to the usage plans\. The API behaves the same as before\. However, you must use the [UsagePlan](http://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan/) `apiStages` property to associate specified API stage values \(`apiId` and `stage`\) with included API keys \(via [UsagePlanKey](http://docs.aws.amazon.com/apigateway/api-reference/resource/usage-plan-key/)\), instead of using the [ApiKey](http://docs.aws.amazon.com/apigateway/api-reference/resource/api-key/) `stageKeys` property\.

To check whether you've already migrated to default usage plans, use the [get\-account]( https://docs.aws.amazon.com/cli/latest/reference/apigateway/get-account.html) CLI command\. In the command output, the `features` list will include an entry of `"UsagePlans"` when usage plans are enabled\.

You can also migrate your APIs to default usage plans by using the AWS CLI as follows:

**To migrate to default usage plans using the AWS CLI**

1. Call this CLI command: [update\-account](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-account.html)\.

1. For the `cli-input-json` parameter, use the following JSON:

   ```
   [
       {
           "op": "add",
           "path": "/features",
           "value": "UsagePlans"
       }
   ]
   ```

## Create Usage Plans<a name="api-gateway-usage-plan-create"></a>

The following procedure describes how to create a usage plan\.

**To create a usage plan**

1. In the Amazon API Gateway main navigation pane, choose **Usage Plans**, and then choose **Create**\.

1. Under **Create Usage Plan**, do the following:

   1. For **Name**, type a name for your plan \(for example, **Plan\_A**\)\. 

   1. For **Description**, type a description for your plan\. 

   1. Select **Enable throttling**, and set **Rate** \(for example, **100**\) and **Burst** \(for example, **200**\)\. 

   1. Choose **Enable quota**, and set its limit \(for example, **5000**\) for a selected time interval \(for example, **Month**\)\.

   1.  Choose **Save**\.  
![\[API usage plan entities\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-usage-plan-keys-create-setup.png)

1. To add a stage to the plan, do the following in the **Associated API Stages** pane: 

   1. Choose **Add API Stage**\. 

   1. Choose an API \(for example, **PetStore**\) from the **API** drop\-down list\. 

   1. Choose a stage \(for example, **Stage\_1**\) from the **Stage** drop\-down list\. 

   1. Choose the checkmark icon to save\.

   1. Choose **Next**\.  
![\[API usage plan entities\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-usage-plan-keys-create-add-stage.png)

1. To add a key to the plan, do the following in the **Usage Plan API Keys** pane:

   1. To use an existing key, choose **Add API Key to Usage Plan**\. 

   1. For **Name**, type a name for the key you want to add \(for example, **MyFirstKey**\)\.

   1. Choose the checkmark icon to save\.

   1. As needed, repeat the preceding steps to add other existing API keys to this usage plan\.  
![\[API usage plan entities\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-usage-plan-keys-create-add-key.png)
**Note**  
To add a new API key to the usage plan, choose **Create API Key and add to Usage Plan** and follow the instructions\.

1. To finish creating the usage plan, choose **Done**\.

1. If you want to add more API stages to the usage plan, choose **Add API Stage** to repeat the preceding steps\.

## Test a Usage Plan<a name="api-gateway-usage-plan-test-console"></a>

To test the usage plan, you can use an AWS SDK, AWS CLI, or a REST API client like Postman\. For an example of using [Postman](https://www.getpostman.com/) to test the usage plan, see [Test Usage Plans](api-gateway-create-usage-plans-with-rest-api.md#api-gateway-usage-plan-test-with-postman)\.

## Maintain Plan Usage<a name="api-gateway-usage-plan-manage-usage"></a>

Maintaining a usage plan involves monitoring the used and remaining quotas over a given time period and extending the remaining quotas by a specified amount\. The following procedures describe how to monitor and extend quotas\.

**To monitor used and remaining quotas**

1. In the API Gateway main navigation pane, choose **Usage Plans**\.

1. Choose a usage plan from the list of the usage plans in the secondary navigation pane in the middle\.

1. From within the specified plan, choose **API Keys**\.

1. Choose an API key, and then choose **Usage** to view **Subscriber's Traffic** from the plan you're monitoring\.

1. Optionally, choose **Export**, choose a **From** date and a **To** date, choose **JSON** or **CSV** for the exported data format, and then choose **Export**\.

   The following example shows an exported file\. 

   ```
   {
     "thisPeriod": {
       "px1KW6...qBazOJH": [
         [
           0,
           5000
         ],
         [
           0,
           5000
         ],
         [
           0,
           10
         ]
       ]
     },
     "startDate": "2016-08-01",
     "endDate": "2016-08-03"
   }
   ```

   The usage data in the example shows the daily usage data for an API client, as identified by the API key \(`px1KW6...qBazOJH`\), between August 1, 2016 and August 3, 2016\. Each daily usage data shows used and remaining quotas\. In this example, the subscriber hasn't used any allotted quotas yet, and the API owner or administrator has reduced the remaining quota from 5000 to 10 on the third day\.

**To extend the remaining quotas**

1. Repeat steps 1–3 of the previous procedure\.

1. On the usage plan page, choose **Extension** from the usage plan window\.

1. Type a number for the **Remaining** request quotas\.

1. Choose **Save**\.