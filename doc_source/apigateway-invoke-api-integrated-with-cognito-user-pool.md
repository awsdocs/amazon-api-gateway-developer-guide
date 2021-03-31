# Call a REST API integrated with an Amazon Cognito user pool<a name="apigateway-invoke-api-integrated-with-cognito-user-pool"></a>

To call a method with a user pool authorizer configured, the client must do the following: 
+ Enable the user to sign up with the user pool\.
+ Enable the user to sign in to the user pool\.
+ Obtain an identity token of the signed\-in user from the user pool\.
+ Include the identity token in the `Authorization` header \(or another header you specified when you created the authorizer\)\.

You can use [AWS Amplify](https://docs.aws.amazon.com/amplify/) to perform these tasks\. See [Integrating Amazon Cognito With Web and Mobile Apps](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-integrate-apps.html) for more information\.
+ For Android, see [Getting Started with Amplify for Android](https://docs.amplify.aws/lib/auth/getting-started/q/platform/android)\.
+ To use iOS see [Getting started with Amplify for iOS](https://docs.amplify.aws/lib/auth/getting-started/q/platform/ios)\.
+ To use JavaScript, see [Getting Started with Amplify for Javascript](https://docs.amplify.aws/lib/auth/getting-started/q/platform/js)\.