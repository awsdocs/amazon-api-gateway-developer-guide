# Set up API keys using the API Gateway console<a name="api-gateway-setup-api-key-with-console"></a>

To set up API keys, do the following:
+ Configure API methods to require an API key\.
+ Create or import an API key for the API in a region\.

Before setting up API keys, you must have created an API and deployed it to a stage\. 

For instructions on how to create and deploy an API by using the API Gateway console, see [Creating a REST API in Amazon API Gateway](how-to-create-api.md) and [Deploying a REST API in Amazon API Gateway](how-to-deploy-api.md), respectively\.

**Topics**
+ [Require API key on a method](#api-gateway-usage-plan-configure-apikey-on-method)
+ [Create an API key](#api-gateway-usage-plan-create-apikey)
+ [Import API keys](#api-gateway-usage-pan-import-apikey)

## Require API key on a method<a name="api-gateway-usage-plan-configure-apikey-on-method"></a>

The following procedure describes how to configure an API method to require an API key\.

**To configure an API method to require an API key**

1. Sign in to the AWS Management Console and open the API Gateway console at [https://console\.aws\.amazon\.com/apigateway/](https://console.aws.amazon.com/apigateway/)\. 

1. In the API Gateway main navigation pane, choose **Resources**\.

1. Under **Resources**, create a new method or choose an existing one\.

1. Choose **Method Request**\.

1. Under the **Authorization Settings** section, choose **true** for **API Key Required**\.

1. Select the checkmark icon to save the settings\.  
![\[\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/how-to-api-key-set-requirement-on-method.png)

1. Deploy or redeploy the API for the requirement to take effect\.

If the **API Key Required** option is set to `false` and you don't execute the previous steps, any API key that's associated with an API stage isn't used for the method\.

## Create an API key<a name="api-gateway-usage-plan-create-apikey"></a>

If you've already created or imported API keys for use with usage plans, you can skip this and the next procedure\.

**To create an API key**

1. Sign in to the AWS Management Console and open the API Gateway console at [https://console\.aws\.amazon\.com/apigateway/](https://console.aws.amazon.com/apigateway/)\. 

1. In the API Gateway main navigation pane, choose **API Keys**\.

1. From the **Actions** drop\-down menu, choose **Create API key**\.  
![\[Create API keys for usage plans\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-usage-plan-keys-choose-create-api-key-from-actions-menu.png)

1. In **Create API Key**, do the following:

   1. Enter an API key name \(for example, **MyFirstKey**\) in the **Name** input field\. 

   1. Choose **Auto Generate** to have API Gateway generate the key value, or choose **Custom** to enter the key manually\. 

   1. Choose **Save**\.  
![\[Create API keys for usage plans\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-usage-plan-keys-create.png)

1. Repeat the preceding steps to create more API keys, if needed\.

## Import API keys<a name="api-gateway-usage-pan-import-apikey"></a>

The following procedure describes how to import API keys to use with usage plans\.

**To import API keys**

1. In the main navigation pane, choose **API Keys**\.

1. From the **Actions** drop\-down menu, choose **Import API keys**\. 

1. To load a comma\-separated key file, choose **Select CSV File**\. You can also enter the keys manually\. For information about the file format, see [API Gateway API key file format](api-key-file-format.md)\.  
![\[API usage plan entities\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-usage-plan-keys-import-setup.png)

1. Choose **Fail on warnings** to stop the import when there's an error, or choose **Ignore warnings** to continue to import valid key entries when there's an error\.

1. To start importing the selected API keys, choose **Import**\.

Now that you've set up the API key, you can proceed to [create and use a usage plan](api-gateway-create-usage-plans-with-console.md)\.