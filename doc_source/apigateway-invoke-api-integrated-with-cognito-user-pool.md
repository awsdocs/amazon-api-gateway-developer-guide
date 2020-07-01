# Call a REST API integrated with an Amazon Cognito user pool<a name="apigateway-invoke-api-integrated-with-cognito-user-pool"></a>

To call a method with a user pool authorizer configured, the client must do the following: 
+ Enable the user to sign up with the user pool\.
+ Enable the user to sign in to the user pool\.
+ Obtain an identity token of the signed\-in user from the user pool\.
+ Include the identity token in the `Authorization` header \(or another header you specified when you created the authorizer\)\.

You can use one of the [AWS SDKs](https://aws.amazon.com/tools#SDK) to perform these tasks\. For example: 
+ To use the Android SDK, see [Setting up the AWS Mobile SDK for Android to Work with User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/setting-up-android-sdk.html)\.
+ To use the iOS SDK, see [Setting Up the AWS Mobile SDK for iOS to Work with User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/walkthrough-using-the-ios-sdk.html)\.
+ To use JavaScript, see [Setting up the AWS SDK for JavaScript in the Browser to Work with User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/setting-up-the-javascript-sdk.html)\.

The following procedure outlines the steps to perform these tasks\. For more information, see the blog posts on [Using Android SDK with Amazon Cognito User Pools](http://mobile.awsblog.com/post/TxNYVQQ3A2LT6Y/Using-Android-SDK-with-Amazon-Cognito-Your-User-Pools) and [Using Amazon Cognito User Pool for iOS](http://mobile.awsblog.com/post/TxGNH1AUKDRZDH/Announcing-Your-User-Pools-in-Amazon-Cognito)\.

**To call an API that's integrated with a user pool**

1. Sign up a first\-time user to a specified user pool\. 

1. Sign in a user to the user pool\.

1. Get the user's identity token\.

1. Call API methods that are configured with a user pool authorizer, and supply the unexpired token in the `Authorization` header or another header of your choosing\. 

1. When the token expires, repeat steps 2–4\. Identity tokens provisioned by Amazon Cognito expire within an hour\. 

 For code examples, see an [Android Java sample](https://github.com/awslabs/aws-sdk-android-samples/tree/main/AmazonCognitoYourUserPoolsDemo) and an [iOS Objective\-C sample](https://github.com/awslabs/aws-sdk-ios-samples/tree/main/CognitoYourUserPools-Sample/Objective-C)\. 