# Set up an API Gateway API with private integrations using the AWS CLI<a name="set-up-api-with-vpclink-cli"></a>

Before creating an API with the private integration, you must have your VPC resource set up and a Network Load Balancer created and configured with your VPC source as the target\. If the requirements are not met, follow [Set up a Network Load Balancer for API Gateway private integrations](set-up-nlb-for-vpclink-using-console.md) to install the VPC resource, create a Network Load Balancer, and set the VPC resource as a target of the Network Load Balancer\.

**Note**  
The Network Load Balancer and API must be owned by the same AWS account\.

For you to be able to create and manage a `VpcLink`, you must also have the appropriate permissions configured\. For more information, see [Grant permissions to create a VPC link](grant-permissions-to-create-vpclink.md)\.

**Note**  
 You only need the permissions to create a `VpcLink` in your API\. You do not need the permissions to use the `VpcLink`\.

After the Network Load Balancer is created, note its ARN\. You need it to create a VPC link for the private integration\. 

**To set up an API with the private integration using AWS CLI**

1. Create a `VpcLink` targeting the specified Network Load Balancer\. 

   ```
   aws apigateway create-vpc-link \
       --name my-test-vpc-link \
       --target-arns arn:aws:elasticloadbalancing:us-east-2:123456789012:loadbalancer/net/my-vpclink-test-nlb/1234567890abcdef
   ```

   The output of this command acknowledges the receipt of the request and shows the `PENDING` status for the `VpcLink` being created\.

   ```
   {
       "status": "PENDING", 
       "targetArns": [
           "arn:aws:elasticloadbalancing:us-east-2:123456789012:loadbalancer/net/my-vpclink-test-nlb/1234567890abcdef"
       ], 
       "id": "gim7c3", 
       "name": "my-test-vpc-link"
   }
   ```

   It takes 2\-4 minutes for API Gateway to finish creating the `VpcLink`\. When the operation finishes successfully, the `status` is `AVAILABLE`\. You can verify this by calling the following CLI command:

   ```
   aws apigateway get-vpc-link --vpc-link-id gim7c3
   ```

   If the operation fails, you get a `FAILED` status, with the `statusMessage` containing the error message\. For example, if you attempt to create a `VpcLink` with a Network Load Balancer that is already associated with a VPC endpoint, you get the following on the `statusMessage` property:

   ```
   "NLB is already associated with another VPC Endpoint Service"
   ```

   After the `VpcLink` is created successfully, you can create an API and integrate it with the VPC resource through the `VpcLink`\. 

   Note the `id` value of the newly created `VpcLink` \(`gim7c3` in the preceding output\)\. You need it to set up the private integration\.

1.  Set up an API by creating an API Gateway [https://docs.aws.amazon.com/apigateway/latest/api/API_RestApi.html](https://docs.aws.amazon.com/apigateway/latest/api/API_RestApi.html) resource:

   ```
   aws apigateway create-rest-api --name 'My VPC Link Test'
   ```

   Note the `RestApi`'s `id` value in the returned result\. You need this value to perform further operations on the API\. 

   For illustration purposes, we will create an API with only a `GET` method on the root resource \(`/`\) and integrate the method with the `VpcLink`\.

1.  Set up the `GET /` method\. First get the identifier of the root resource \(`/`\):

   ```
   aws apigateway get-resources --rest-api-id  abcdef123
   ```

   In the output, note the `id` value of the `/` path\. In this example, we assume it to be `skpp60rab7`\.

   Set up the method request for the API method of `GET /`:

   ```
   aws apigateway put-method \
          --rest-api-id  abcdef123 \
          --resource-id skpp60rab7 \
          --http-method GET \
          --authorization-type "NONE"
   ```

   If you do not use the proxy integration with the `VpcLink`, you must also set up at least a method response of the `200` status code\. We will use the proxy integration here\.

1.  Set up the private integration of the `HTTP_PROXY` type and call the `put-integration` command as follows: 

   ```
   aws apigateway put-integration \
       --rest-api-id abcdef123 \
       --resource-id skpp60rab7 \
       --uri 'http://my-vpclink-test-nlb-1234567890abcdef.us-east-2.amazonaws.com' \
       --http-method GET \
       --type HTTP_PROXY \
       --integration-http-method GET \
       --connection-type VPC_LINK \
       --connection-id gim7c3
   ```

   For a private integration, set `connection-type` to `VPC_LINK` and set `connection-id` to either your `VpcLink`'s identifier or a stage variable referencing your `VpcLink` ID\. The `uri` parameter is not used for routing requests to your endpoint, but is used for setting the `Host` header and for certificate validation\.

   The command returns the following output:

   ```
   {
       "passthroughBehavior": "WHEN_NO_MATCH", 
       "timeoutInMillis": 29000, 
       "connectionId": "gim7c3", 
       "uri": "http://my-vpclink-test-nlb-1234567890abcdef.us-east-2.amazonaws.com", 
       "connectionType": "VPC_LINK", 
       "httpMethod": "GET", 
       "cacheNamespace": "skpp60rab7", 
       "type": "HTTP_PROXY", 
       "cacheKeyParameters": []
   }
   ```

   Using a stage variable, you set the `connectionId` property when creating the integration:

   ```
   aws apigateway put-integration \
       --rest-api-id abcdef123 \
       --resource-id skpp60rab7 \
       --uri 'http://my-vpclink-test-nlb-1234567890abcdef.us-east-2.amazonaws.com' \
       --http-method GET \
       --type HTTP_PROXY \
       --integration-http-method GET \
       --connection-type VPC_LINK \
       --connection-id "\${stageVariables.vpcLinkId}"
   ```

   Make sure to double\-quote the stage variable expression \(`${stageVariables.vpcLinkId}`\) and escape the `$` character\. 

   Alternatively, you can update the integration to reset the `connectionId` value with a stage variable:

   ```
    aws apigateway update-integration \
       --rest-api-id abcdef123 \
       --resource-id skpp60rab7 \
       --http-method GET \
       --patch-operations '[{"op":"replace","path":"/connectionId","value":"${stageVariables.vpcLinkId}"}]'
   ```

   Make sure to use a stringified JSON list as the `patch-operations` parameter value\.

    You can use a stage variable to integrate your API with a different VPC or Network Load Balancer by resetting the `VpcLink`s stage variable value\.

   Because we used the private proxy integration, the API is now ready for deployment and for test runs\. With the non\-proxy integration, you must also set up the method response and integration response, just as you would when setting up an [API with HTTP custom integrations](api-gateway-create-api-step-by-step.md#api-gateway-create-resource-and-methods)\.

1.  To test the API, deploy the API\. This is necessary if you have used the stage variable as a placeholder of the `VpcLink` ID\. To deploy the API with a stage variable, call the `create-deployment` command as follows:

   ```
   aws apigateway create-deployment \
       --rest-api-id abcdef123 \
       --stage-name test \
       --variables vpcLinkId=gim7c3
   ```

   To update the stage variable with a different `VpcLink` ID \(e\.g\., `asf9d7`\), call the `update-stage` command:

   ```
   aws apigateway update-stage \
       --rest-api-id abcdef123 \
       --stage-name test \
       --patch-operations op=replace,path='/variables/vpcLinkId',value='asf9d7'
   ```

   Use the following command to invoke your API:

   ```
   curl -X GET https://abcdef123.execute-api.us-east-2.amazonaws.com/test
   ```

   Alternatively, you can type the API's invoke\-URL in a web browser to view the result\.

   When you hardcode the `connection-id` property with the `VpcLink` ID literal, you can also call `test-invoke-method` to test invoking the API before it is deployed\. 