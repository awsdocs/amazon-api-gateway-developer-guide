# Enable CORS on a resource using the API Gateway import API<a name="enable-cors-for-resource-using-swagger-importer-tool"></a>

If you are using the [API Gateway Import API](api-gateway-import-api.md), you can set up CORS support using an OpenAPI file\. You must first define an `OPTIONS` method in your resource that returns the required headers\.

**Note**  
Web browsers expect Access\-Control\-Allow\-Headers, and Access\-Control\-Allow\-Origin headers to be set up in each API method that accepts CORS requests\. In addition, some browsers first make an HTTP request to an `OPTIONS` method in the same resource, and then expect to receive the same headers\.

The following example creates an `OPTIONS` method for a mock integration\.

------
#### [ OpenAPI 3\.0 ]

```
/users:
  options:
    summary: CORS support
    description: |
      Enable CORS by returning correct headers
    tags:
    - CORS
    responses:
      200:
        description: Default response for CORS method
        headers:
          Access-Control-Allow-Origin:
            schema:
              type: string
          Access-Control-Allow-Methods:
            schema:
              type: string
          Access-Control-Allow-Headers:
            schema:
              type: string
        content: {}
    x-amazon-apigateway-integration:
      type: mock
      requestTemplates:
        application/json: |
          {
            "statusCode" : 200
          }
      responses:
        default:
          statusCode: "200"
          responseParameters:
            method.response.header.Access-Control-Allow-Headers: '''Content-Type,X-Amz-Date,Authorization,X-Api-Key'''
            method.response.header.Access-Control-Allow-Methods: '''*'''
            method.response.header.Access-Control-Allow-Origin: '''*'''
          responseTemplates:
            application/json: |
              {}
```

------
#### [ OpenAPI 2\.0 ]

```
/users: 
   options:
      summary: CORS support
      description: |
        Enable CORS by returning correct headers
      consumes:
        - application/json
      produces:
        - application/json
      tags:
        - CORS
      x-amazon-apigateway-integration:
        type: mock
        requestTemplates:
          application/json: |
            {
              "statusCode" : 200
            }
        responses:
          "default":
            statusCode: "200"
            responseParameters:
              method.response.header.Access-Control-Allow-Headers : "'Content-Type,X-Amz-Date,Authorization,X-Api-Key'"
              method.response.header.Access-Control-Allow-Methods : "'*'"
              method.response.header.Access-Control-Allow-Origin : "'*'"
            responseTemplates:
              application/json: |
                {}
      responses:
        200:
          description: Default response for CORS method
          headers:
            Access-Control-Allow-Headers:
              type: "string"
            Access-Control-Allow-Methods:
              type: "string"
            Access-Control-Allow-Origin:
              type: "string"
```

------

Once you have configured the `OPTIONS` method for your resource, you can add the required headers to the other methods in the same resource that need to accept CORS requests\.

1. Declare the **Access\-Control\-Allow\-Origin** and **Headers** to the response types\.

------
#### [ OpenAPI 3\.0 ]

   ```
       responses:
         200:
           description: Default response for CORS method
           headers:
             Access-Control-Allow-Origin:
               schema:
                 type: string
             Access-Control-Allow-Methods:
               schema:
                 type: string
             Access-Control-Allow-Headers:
               schema:
                 type: string
           content: {}
   ```

------
#### [ OpenAPI 2\.0 ]

   ```
         responses:
           200:
             description: Default response for CORS method
             headers:
               Access-Control-Allow-Headers:
                 type: "string"
               Access-Control-Allow-Methods:
                 type: "string"
               Access-Control-Allow-Origin:
                 type: "string"
   ```

------

1. In the `x-amazon-apigateway-integration` tag, set up the mapping for those headers to your static values:

------
#### [ OpenAPI 3\.0 ]

   ```
       responses:
           default:
             statusCode: "200"
             responseParameters:
               method.response.header.Access-Control-Allow-Headers: '''Content-Type,X-Amz-Date,Authorization,X-Api-Key'''
               method.response.header.Access-Control-Allow-Methods: '''*'''
               method.response.header.Access-Control-Allow-Origin: '''*'''
             responseTemplates:
               application/json: |
                 {}
   ```

------
#### [ OpenAPI 2\.0 ]

   ```
           responses:
             "default":
               statusCode: "200"
               responseParameters:
                 method.response.header.Access-Control-Allow-Headers : "'Content-Type,X-Amz-Date,Authorization,X-Api-Key'"
                 method.response.header.Access-Control-Allow-Methods : "'*'"
                 method.response.header.Access-Control-Allow-Origin : "'*'"
   ```

------