# Set up API with private integrations using OpenAPI<a name="set-up-api-with-vpclink-using-swagger"></a>

You can set up an API with the private integration by importing the API's OpenAPI file\. The settings are similar to the OpenAPI definitions of an API with HTTP integrations, with the following exceptions: 
+ You must explicitly set `connectionType` to `VPC_LINK`\.
+ You must explicitly set `connectionId` to the ID of a `VpcLink` or to a stage variable referencing the ID of a `VpcLink`\.
+ The `uri` parameter in the private integration points to an HTTP/HTTPS endpoint in the VPC, but is used instead to set up the integration request's `Host` header\.
+ The `uri` parameter in the private integration with an HTTPS endpoint in the VPC is used to verify the stated domain name against the one in the certificate installed on the VPC endpoint\.

 You can use a stage variable to reference the `VpcLink` ID\. Or you can assign the ID value directly to `connectionId`\. 

The following JSON\-formatted OpenAPI file shows an example of an API with a VPC link as referenced by a stage variable \(`${stageVariables.vpcLinkId}`\):

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  "info": {
    "version": "2017-11-17T04:40:23Z",
    "title": "MyApiWithVpcLink"
  },
  "host": "p3wocvip9a.execute-api.us-west-2.amazonaws.com",
  "basePath": "/test",
  "schemes": [
    "https"
  ],
  "paths": {
    "/": {
      "get": {
        "produces": [
          "application/json"
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "schema": {
              "$ref": "#/definitions/Empty"
            }
          }
        },
        "x-amazon-apigateway-integration": {
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "uri": "http://myApi.example.com",
          "passthroughBehavior": "when_no_match",
          "connectionType": "VPC_LINK",
          "connectionId": "${stageVariables.vpcLinkId}",
          "httpMethod": "GET",
          "type": "http_proxy"
        }
      }
    }
  },
  "definitions": {
    "Empty": {
      "type": "object",
      "title": "Empty Schema"
    }
  }
}
```

------