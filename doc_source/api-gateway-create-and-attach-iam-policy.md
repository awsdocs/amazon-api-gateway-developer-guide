# Create and attach a policy to a user<a name="api-gateway-create-and-attach-iam-policy"></a>

To enable a user to call the API managing service or the API execution service, you must create an IAM policy which controls access to the API Gateway entities\. 

**To use the JSON policy editor to create a policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation column on the left, choose **Policies**\. 

   If this is your first time choosing **Policies**, the **Welcome to Managed Policies** page appears\. Choose **Get Started**\.

1. At the top of the page, choose **Create policy**\.

1. Choose the **JSON** tab\.

1. Enter the following JSON policy document:

   ```
   {
     "Version": "2012-10-17",
     "Statement" : [
       {
         "Effect" : "Allow",
         "Action" : [
           "action-statement"
         ],
         "Resource" : [
           "resource-statement"
         ]
       },
       {
         "Effect" : "Allow",
         "Action" : [
           "action-statement"
         ],
         "Resource" : [
           "resource-statement"
         ]
       }
     ]
   }
   ```

1. Choose **Review policy**\.
**Note**  
You can switch between the **Visual editor** and **JSON** tabs any time\. However, if you make changes or choose **Review policy** in the **Visual editor** tab, IAM might restructure your policy to optimize it for the visual editor\. For more information, see [Policy restructuring](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_policies.html#troubleshoot_viseditor-restructure) in the *IAM User Guide*\.

1. On the **Review policy** page, enter a **Name** and an optional **Description** for the policy that you are creating\. Review the policy **Summary** to see the permissions that are granted by your policy\. Then choose **Create policy** to save your work\.

In this statement, substitute *action\-statement* and *resource\-statement* as needed, and add other statements to specify the API Gateway entities you want to allow the user to manage, the API methods the user can call, or both\. By default, the user does not have permissions unless there is an explicit corresponding `Allow` statement\. 

 You have just created an IAM policy\. It won't have any effect until you attach it\. 

**To attach an IAM policy document to an IAM group**

1.  Choose **Groups** from the main navigation pane\. 

1. Choose the **Permissions** tab under the chosen group\. 

1. Choose **Attach policy**\.

1. Choose the policy document that you previously created, and then choose **Attach policy**\.

 For API Gateway to call other AWS services on your behalf, create an IAM role of the **Amazon API Gateway** type\.

**To create an Amazon API Gateway type of role**

1. Choose **Roles** from the main navigation pane\.

1. Choose **Create New Role**\.

1. Type a name for **Role name** and then choose **Next Step**\.

1. Under **Select Role Type**, in **AWS Service Roles**, choose **Select** next to **Amazon API Gateway**\.

1. Choose an available managed IAM permissions policy, for example, **AmazonAPIGatewayPushToCloudWatchLog** if you want API Gateway to log metrics in CloudWatch, under **Attach Policy** and then choose **Next Step**\.

1. Under **Trusted Entities**, verify that **apigateway\.amazonaws\.com** is listed as an entry, and then choose **Create Role**\.

1. In the newly created role, choose the **Permissions** tab and then choose **Attach Policy**\.

1. Choose the previously created custom IAM policy document and then choose **Attach Policy**\.