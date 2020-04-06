# AWS variables for OpenAPI import<a name="import-api-aws-variables"></a>

You can use the following AWS variables in OpenAPI definitions\. API Gateway resolves the variables when the API is imported\. To specify a variable, use `${variable-name}`\. 


**AWS variables**  

| Variable name | Description | 
| --- | --- | 
| AWS::AccountId | The AWS account ID that imports the API—for example, 123456789012\. | 
| AWS::Partition | The AWS partition in which the API is imported\. For standard AWS Regions, the partition is aws\. | 
| AWS::Region | The AWS Region in which the API is imported—for example, us\-east\-2\. | 

## AWS variables example<a name="import-api-aws-variables-example"></a>

The following example uses AWS variables to specify an AWS Lambda function for an integration\.

------
#### [ OpenAPI 3\.0 ]

```
openapi: "3.0.1"
info:
  title: "tasks-api"
  version: "v1.0"
paths:
  /:
    get:
      summary: List tasks
      description: Returns a list of tasks
      responses:
        200:
          description: "OK"
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/Task"
        500:
          description: "Internal Server Error"
          content: {}
      x-amazon-apigateway-integration:
        uri:
          arn:${AWS::Partition}:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/arn:${AWS::Partition}:lambda:${AWS::Region}:${AWS::AccountId}:function:LambdaFunctionName/invocations
        responses:
          default:
            statusCode: "200"
        passthroughBehavior: "when_no_match"
        httpMethod: "POST"
        contentHandling: "CONVERT_TO_TEXT"
        type: "aws_proxy"
```

------