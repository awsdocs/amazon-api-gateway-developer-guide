# Create and attach a policy to an IAM user<a name="api-gateway-create-and-attach-iam-policy"></a>

To enable a user to call the API managing service or the API execution service, you must create an IAM policy for an IAM user, which controls access to the API Gateway entities, and then attach the policy to the IAM user\. The following steps describe how to create your IAM policy\.

**To create your own IAM policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**, and then choose **Create Policy**\. If a **Get Started** button appears, choose it, and then choose **Create Policy**\.

1. Next to **Create Your Own Policy**, choose **Select**\.

1. For **Policy Name**, type any value that is easy for you to refer to later\. Optionally, type descriptive text in **Description**\. 

1. For **Policy Document**, type a policy statement with the following format, and then choose **Create Policy**:

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

   In this statement, substitute *action\-statement* and *resource\-statement* as needed, and add other statements to specify the API Gateway entities you want to allow the IAM user to manage, the API methods the IAM user can call, or both\. By default, the IAM user does not have permissions unless there is an explicit corresponding `Allow` statement\. 

1. To enable the policy for a user, choose **Users**\.

1. Choose the IAM user to whom you want to attach the policy\.

 You have just created an IAM policy\. It won't have any effect until you attach it to an IAM user, to an IAM group containing the user, or to an IAM role assumed by the user\. 

**To attach an IAM policy to an IAM user**

1. For the chosen user, choose the **Permissions** tab, and then choose **Attach Policy**\.

1. Under **Grant permissions**, choose **Attach existing policies directly**\.

1.  Choose the policy document just created from the displayed list and then choose **Next: Review**\.

1. Under **Permissions summary**, choose **Add permissions**\.

 Alternatively, you can add the user to an IAM group, if the user is not already a member, and attach the policy document to the group so that the attached policies are applicable to all group members\. It is helpful to manage and update policy configurations on a group of IAM users\. In the following, we highlight how to attach the policy to an IAM group, assuming that you have already created the group and added the user to the group\. 

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