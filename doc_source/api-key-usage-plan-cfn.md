# Create and configure API keys and usage plans with AWS CloudFormation<a name="api-key-usage-plan-cfn"></a>

 You can use AWS CloudFormation to require API keys on API methods and create a usage plan for an API\. The example AWS CloudFormation template does the following:
+ Creates an API Gateway API with a `GET` method\.
+ Requires an API key for the `GET` method\. This API receives keys from the `X-API-KEY` header of each incoming request\. 
+ Creates an API key\.
+ Creates a usage plan to specify a monthly quota of 1000 request each month, a throttling rate limit of 100 request each second, and a throttling burst limit of 200 request each second\. 
+ Associates the API stage and API key with the usage plan\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: An Amazon API Gateway REST API example for requiring an API key to call a resource.
Parameters:
  StageName:
    Type: String
    Default: v1
    Description: Name of API stage.
  KeyName:
    Type: String
    Default: MyKeyName
    Description: Name of an API key
Resources:
  Api:
    Type: 'AWS::ApiGateway::RestApi'
    Properties:
      Name: keys-api
      ApiKeySourceType: HEADER
  PetsResource:
    Type: 'AWS::ApiGateway::Resource'
    Properties:
      RestApiId: !Ref Api
      ParentId: !GetAtt Api.RootResourceId
      PathPart: 'pets'
  PetsMethodGet:
    Type: 'AWS::ApiGateway::Method'
    Properties:
      RestApiId: !Ref Api
      ResourceId: !Ref PetsResource
      HttpMethod: GET
      ApiKeyRequired: true
      AuthorizationType: NONE
      Integration:
        Type: HTTP_PROXY
        IntegrationHttpMethod: GET
        Uri: http://petstore-demo-endpoint.execute-api.com/petstore/pets/
  ApiDeployment:
    Type: 'AWS::ApiGateway::Deployment'
    DependsOn:
      - PetsMethodGet
    Properties:
      RestApiId: !Ref Api
      StageName: !Sub '${StageName}'
  UsagePlan:
    Type: AWS::ApiGateway::UsagePlan
    DependsOn:
      - ApiDeployment
    Properties:
      Description: Example usage plan to specify a monthly quota of 1000 requests and a rate of 100 requests per second.
      ApiStages:
        - ApiId: !Ref Api
          Stage: !Sub '${StageName}'
      Quota:
        Limit: 1000
        Period: MONTH
      Throttle:
        BurstLimit : 200
        RateLimit: 100
      UsagePlanName: "My Usage Plan"
  ApiKey:
    Type: AWS::ApiGateway::ApiKey
    Properties: 
      Description: API Key
      Name: !Sub '${KeyName}'
      Enabled: True
  UsagePlanKey:
    Type: AWS::ApiGateway::UsagePlanKey
    Properties:
      KeyId: !Ref ApiKey
      KeyType: API_KEY
      UsagePlanId: !Ref UsagePlan
Outputs:
  ApiRootUrl:
    Description: Root Url of the API
    Value: !Sub 'https://${Api}.execute-api.${AWS::Region}.amazonaws.com/${StageName}'
```