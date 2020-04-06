# Document an API using the API Gateway console<a name="api-gateway-documenting-api-quick-start-with-console"></a>

In this section, we describe how to create and maintain documentation parts of an API using the API Gateway console\. 

A prerequisite for creating and editing the documentation of an API is that you must have already created the API\. In this section, we use the [PetStore](http://petstore-demo-endpoint.execute-api.com/petstore/pets) API as an example\. To create an API using the API Gateway console, follow the instructions in [Tutorial: Create a REST API by importing an example](api-gateway-create-api-from-example.md)\. 

**Topics**
+ [Document the `API` entity](#api-gateway-document-api-add-document-part-for-api-entity-with-console)
+ [Document a `RESOURCE` entity](#api-gateway-document-api-add-document-part-for-resource-entity-with-console)
+ [Document a `METHOD` entity](#api-gateway-document-api-add-document-part-for-method-entity-with-console)
+ [Document a `QUERY_PARAMETER` entity](#api-gateway-document-api-add-document-part-for-request-query-entity-with-console)
+ [Document a `PATH_PARAMETER` entity](#api-gateway-document-api-add-document-part-for-path-parameter-entity-with-console)
+ [Document a `REQUEST_HEADER` entity](#api-gateway-document-api-add-document-part-for-request-header-entity-with-console)
+ [Document a `REQUEST_BODY` entity](#api-gateway-document-api-add-document-part-for-request-body-entity-with-console)
+ [Document a `RESPONSE` entity](#api-gateway-document-api-add-document-part-for-response-with-console)
+ [Document a `RESPONSE_HEADER` entity](#api-gateway-document-api-add-document-part-for-response-header-entity-with-console)
+ [Document a `RESPONSE_BODY` entity](#api-gateway-document-api-add-document-part-for-response-body-entity-with-console)
+ [Document a `MODEL` entity](#api-gateway-document-api-add-document-part-for-model-entity-with-console)
+ [Document an `AUTHORIZER` entity](#api-gateway-document-api-add-document-part-for-authorizer-entity-with-console)

## Document the `API` entity<a name="api-gateway-document-api-add-document-part-for-api-entity-with-console"></a>

 To add a documentation part for the `API` entity, choose **Resources** from the **PetStore** API\. Choose the **Actions → Edit API Documentation** menu item\. 

![\[Edit documentation for the API entity in the API Gateway console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/document-api-entity-using-console.png)

If a documentation part was not created for the `API`, you get the documentation part's `properties` map editor\. Type the following `properties` map in the text editor and then choose **Save** to create the documentation part\. 

```
{
  "info": {
    "description": "Your first API Gateway API.",
    "contact": {
        "name": "John Doe",
        "email": "john.doe@api.com"
    }
  }
}
```

**Note**  
 You do not need to encode the `properties` map into a JSON string\. The API Gateway console stringifies the JSON object for you\. 

![\[Edit documentation properties map for the API entity in the API Gateway console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/document-api-entity-edit-properties-map-using-console.png)

If a documentation part has already been created, you first get the `properties` map viewer, as shown in the following\. 

![\[View documentation properties map for the API entity in the API Gateway console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/document-api-entity-view-properties-map-using-console.png)

 Choosing **Edit** brings up the `properties` map editor as shown previously\. 

## Document a `RESOURCE` entity<a name="api-gateway-document-api-add-document-part-for-resource-entity-with-console"></a>

To add or edit the documentation part for the API's root resource, choose **/** under the **Resource** tree, and then choose the **Actions → Edit Resource Documentation** menu item\. 

 If no documentation part was created for this entity, you get the **Documentation** window\. Type a valid `properties` map in the editor\. Then choose **Save** and **Close**\. 

```
{
    "description": "The PetStore's root resource."
}
```

If a documentation part has already been defined for the `RESOURCE` entity, you get the documentation viewer\. Choose **Edit** to open the **Documentation** editor\. Modify the existing `properties` map\. Choose **Save** and then choose **Close**\. 

 If necessary, repeat these steps to add a documentation part to other `RESOURCE` entities\. 

## Document a `METHOD` entity<a name="api-gateway-document-api-add-document-part-for-method-entity-with-console"></a>

To add or edit documentation for a `METHOD` entity, using the `GET` method on the root resource as an example, choose **GET** under the **/** resource and the choose the **Actions → Edit Method Documentation** menu item\. 

 For the new documentation part, type the following `properties` map in the **Documentation** editor in the **Documentation** window\. Then choose **Save** and **Close**\. 

```
{
  "tags" : [ "pets" ],
  "description" : "PetStore HTML web page containing API usage information"
}
```

For the existing documentation, choose **Edit** from the **Documentation** viewer\. Edit the documentation content in the **Documentation** editor and choose **Save**\. Choose **Close**\. 

From the **Documentation** viewer, you can also delete the documentation part\.

If necessary, repeat these steps to add a documentation part to other methods\.

## Document a `QUERY_PARAMETER` entity<a name="api-gateway-document-api-add-document-part-for-request-query-entity-with-console"></a>

 To add or edit a documentation part for a request query parameter, using the `GET /pets?type=...&page=...` method as an example, choose **GET** under **/pets** from the **Resources** tree\. Choose **Method Request** in the **Method Execution** window\. Expand the **URL Query String Parameters** section\. Choose the **page** query parameter, for example, and choose the book icon to open the **Documentation** viewer or editor\. 

![\[Edit documentation for the API entity on the API Gateway console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/document-query-parameter-via-method-request-using-console.png)

 Alternatively, you can choose **Documentation** under the **PetStore** API from the main navigation pane\. Then choose `Query Parameter` for **Type**\. For the PetStore example API, this shows the documentation parts for the `page` and `type` query parameters\. 

![\[Edit documentation for the API entity in the API Gateway console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/document-request-query-entity-using-console.png)

 For an API with query parameters defined for other methods, you can filter your selection by specifying the `path` of the affected resource for **Path**, choosing the desired HTTP method from **Method**, or typing the query parameter name in **Name**\.

 For example, choose the `page` query parameter\. Choose **Edit** to modify the existing documentation\. Choose **Save** to save the change\. 

 To add a new documentation part for a `QUERY_PARAMETER` entity, choose **Create Documentation Part**\. Choose `Query Parameter` for **Type**\. Type a resource path \(e\.g\., `/pets`\) in **Path**\. Choose an HTTP verb \(e\.g\., `GET`\) for **Method**\. Type a `properties` description in the text editor\. Then choose **Save**\. 

If necessary, repeat these steps to add a documentation part to other request query parameters\.

## Document a `PATH_PARAMETER` entity<a name="api-gateway-document-api-add-document-part-for-path-parameter-entity-with-console"></a>

 To add or edit documentation for a path parameter, go to **Method Request** of the method on the resource specified by the path parameter\. Expand the **Request Paths** section\. Choose the book icon for the path parameter to open the **Documentation** viewer or editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the **PetStore** API from the main navigation pane\. Choose `Path Parameter` for **Type**\. Choose **Edit** on a path parameter from the list\. Modify the content and then choose **Save**\. 

To add documentation for a path parameter not listed, choose **Create Documentation Part**\. Choose **Path Parameter** for **Type**\. Set a resource path in **Path**, choose a method from **Method**, and set a path parameter name in **Name**\. Add the documentation's properties and choose **Save**\. 

If required, repeat these steps to add or edit a documentation part to other path parameters\.

## Document a `REQUEST_HEADER` entity<a name="api-gateway-document-api-add-document-part-for-request-header-entity-with-console"></a>

 To add or edit documentation for a request header, go to **Method Request** of the method with the header parameter\. Expand the **HTTP Request Headers** section\. Choose the book icon for the header to open the **Documentation** viewer or editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Request Header` for **Type**\. Choose **Edit** on a listed request header to change the documentation\. To add documentation for an unlisted request header, choose **Create Documentation Part**\. Choose **Request Header** for **Type**\. Specify a resource path in **Path**\. Choose a method for **Method**\. Type a header name in **Name**\. Then add and save a `properties` map\. 

 If required, repeat these steps to add or edit a documentation part to other request headers\. 

## Document a `REQUEST_BODY` entity<a name="api-gateway-document-api-add-document-part-for-request-body-entity-with-console"></a>

 To add or edit documentation for a request body, go to **Method Request** for a method\. Choose the book icon for **Request Body** to open the **Documentation** viewer and then editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Request Body` for **Type**\. Choose **Edit** on a listed request body to change the documentation\. To add documentation for an unlisted request body, choose **Create Documentation Part**\. Choose `Request Body` for **Type**\. Set a resource path in **Path**\. Choose an HTTP verb for **Method**\. Then add and save a `properties` map\. 

 If required, repeat these steps to add or edit a documentation part to other request bodies\. 

## Document a `RESPONSE` entity<a name="api-gateway-document-api-add-document-part-for-response-with-console"></a>

 To add or edit documentation for a response, go to **Method Response** of a method\. Choose the book icon for **Method Response** to open the **Documentation** viewer and then editor\. Add or modify the properties of the documentation part\. 

![\[Edit documentation for the RESPONSE entity on the API Gateway console\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/document-response-via-method-response-using-console.png)

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Response (status code)` for **Type**\. Choose **Edit** on a listed response of a specified HTTP status code to change the documentation\. To add documentation for an unlisted response body, choose **Create Documentation Part**\. Choose **Response \(status code\)** for **Type**\. Set a resource path in **Path**\. Choose an HTTP verb for **Method**\. Type an HTTP status code in **Status Code**\. Then add and save the documentation part properties\. 

 If required, repeat these steps to add or edit a documentation part to other responses\. 

## Document a `RESPONSE_HEADER` entity<a name="api-gateway-document-api-add-document-part-for-response-header-entity-with-console"></a>

 To add or edit documentation for a response header, go to **Method Response** of a method\. Expand a response section of a given HTTP status\. Choose the book icon for a response header under **Response Headers for *StatusCode*** to open the **Documentation** viewer and then editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Response Header` for **Type**\. Choose **Edit** on a listed response header to change the documentation\. To add documentation for an unlisted response header, choose **Create Documentation Part**\. Choose **Response Header** for **Type**\. Set a resource path in **Path**\. Choose an HTTP verb for **Method**\. Type an HTTP status code in **Status Code**\. Type the response header name in **Name**\. Then add and save the documentation part properties\. 

 If required, repeat these steps to add or edit a documentation part to other response headers\. 

## Document a `RESPONSE_BODY` entity<a name="api-gateway-document-api-add-document-part-for-response-body-entity-with-console"></a>

 To add or edit documentation for a response body, go to **Method Response** of a method\. Expand the response section of a given HTTP status\. Choose the book icon for **Response Body for *StatusCode*** to open the **Documentation** viewer and then editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Response Body` for **Type**\. Choose **Edit** on a listed response body to change the documentation\. To add documentation for an unlisted response body, choose **Create Documentation Part**\. Choose **Response Body** for **Type**\. Set a resource path in **Path**\. Choose an HTTP verb for **Method**\. Type an HTTP status code in **Status Code**\. Then add and save the documentation part properties\. 

 If required, repeat these steps to add or edit a documentation part to other response bodies\. 

## Document a `MODEL` entity<a name="api-gateway-document-api-add-document-part-for-model-entity-with-console"></a>

Documenting a `MODEL` entity involves creating and managing `DocumentPart` instances for the model and each of the model's `properties`'\. For example, for the `Error` model that comes with every API by default has the following schema definition,

```
{
  "$schema" : "http://json-schema.org/draft-04/schema#",
  "title" : "Error Schema",
  "type" : "object",
  "properties" : {
    "message" : { "type" : "string" }
  }
}
```

 and requires two `DocumentationPart` instances, one for the `Model` and the other for its `message` property:

```
{
  "location": {
    "type": "MODEL",
    "name": "Error"
  },
  "properties": {
    "title": "Error Schema",
    "description": "A description of the Error model"
  }
}
```

and

```
{
  "location": {
    "type": "MODEL",
    "name": "Error.message"
  },
  "properties": {
    "description": "An error message."
  }
}
```

When the API is exported, the `DocumentationPart`'s properties will override the values in the original schema\.

 To add or edit documentation for a model, go to **Models** of the API in the main navigation pane\. Choose the book icon for the name of a listed model to open the **Documentation** viewer and then editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Model` for **Type**\. Choose **Edit** on a listed model to change the documentation\. To add documentation for an unlisted model, choose **Create Documentation Part**\. Choose **Model** for **Type**\. Give a name to the model in **Name**\. Then add and save the documentation part properties\. 

 If required, repeat these steps to add or edit a documentation part to other models\. 

## Document an `AUTHORIZER` entity<a name="api-gateway-document-api-add-document-part-for-authorizer-entity-with-console"></a>

 To add or edit documentation for an authorizer, go to **Authorizers** for the API in the main navigation pane\. Choose the book icon for the listed authorizer to open the **Documentation** viewer and then editor\. Add or modify the properties of the documentation part\. 

Alternatively, choose **Documentation** under the API from the main navigation pane\. Then choose `Authorizer` for **Type**\. Choose **Edit** on a listed authorizer to change the documentation\. To add documentation for an unlisted authorizer, choose **Create Documentation Part**\. Choose **Authorizer** for **Type**\. Give a name to the authorizer in **Name**\. Then add and save the documentation part properties\. 

 If required, repeat these steps to add or edit a documentation part to other authorizers\. 

 To add a documentation part for an authorizer, choose **Create Documentation Part**\. Choose **Authorizer** for **Type**\. Specify a value for the valid `location` field of **Name** for the authorizer\. 

 Add and save the documentation content in the `properties` map editor\. 

If required, repeat these steps to add a documentation part to another authorizer\.