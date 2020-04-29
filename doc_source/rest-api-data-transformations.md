# Setting up data transformations for REST APIs<a name="rest-api-data-transformations"></a>

 In API Gateway, an API's method request can take a payload in a different format from the corresponding integration request payload, as required in the backend\. Similarly, the backend may return an integration response payload different from the method response payload, as expected by the frontend\. API Gateway lets you use mapping templates to map the payload from a method request to the corresponding integration request and from an integration response to the corresponding method response\. 

 A *mapping template* is a script expressed in [Velocity Template Language \(VTL\)](https://velocity.apache.org/engine/devel/vtl-reference.html) and applied to the payload using [JSONPath expressions](http://goessner.net/articles/JsonPath/)\. 

The payload can have a *data model* according to the [JSON schema draft 4](https://tools.ietf.org/html/draft-zyp-json-schema-04)\. You must define the model in order to have API Gateway to generate a SDK or to enable basic request validation for your API\. You don't have to define any model to create a mapping template\. However, a model can help you create a template because API Gateway will generate a template blueprint based on a provided model\. 

This section explains how to map the API request and response payload using models and mapping templates\.

## Models<a name="models-mappings-models"></a>

In API Gateway, a model defines the data structure of a payload\. In API Gateway models are defined using the [JSON schema draft 4](https://tools.ietf.org/html/draft-zyp-json-schema-04)\. 

The following JSON object describes sample data that describes the fruit or vegetable inventory in the produce department of a likely supermarket\.

 Suppose we have an API for managing fruit and vegetable inventory in the produce department of a supermarket\. When a manager queries the backend for the current inventory, the server sends back the following response payload: 

```
{
  "department": "produce",
  "categories": [
    "fruit",
    "vegetables"
  ],
  "bins": [
    {
      "category": "fruit",
      "type": "apples",
      "price": 1.99,
      "unit": "pound",
      "quantity": 232
    },
    {
      "category": "fruit",
      "type": "bananas",
      "price": 0.19,
      "unit": "each",
      "quantity": 112
    },
    {
      "category": "vegetables",
      "type": "carrots",
      "price": 1.29,
      "unit": "bag",
      "quantity": 57
    }
  ]
}
```

The JSON object has three properties:
+ The `department` property has a string value \(`produce`\)\.
+ The `categories` property is an array of two strings: `fruit` and `vegetables`\.
+ The `bins` property is an array of objects that have the string or number properties of `category`, `type`, `price`, `unit`, and `quantity`\.

We can use the following JSON schema to define the model for the preceding data:

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "GroceryStoreInputModel",
  "type": "object",
  "properties": {
    "department": { "type": "string" },
    "categories": {
      "type": "array",
      "items": { "type": "string" }
    },
    "bins": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "category": { "type": "string" },
          "type": { "type": "string" },
          "price": { "type": "number" },
          "unit": { "type": "string" },
          "quantity": { "type": "integer" }
        }
      }
    }
  }
}
```

In the preceding example model:
+ The `$schema` object represents a valid JSON Schema version identifier\. In this example, it refers to JSON Schema, draft v4\.
+ The `title` object is a human\-readable identifier for the model\. In this example, it is `GroceryStoreInputModel`\.
+ The top\-level, or root, construct in the JSON data is an object\.
+ The root object in the JSON data contains `department`, `categories`, and `bins` properties\.
+ The `department` property is a string object in the JSON data\.
+ The `categories` property is an array in the JSON data\. The array contains string values in the JSON data\.
+ The `bins` property is an array in the JSON data\. The array contains objects in the JSON data\. Each of these objects in the JSON data contains a `category` string, a `type` string, a `price` number, a `unit` string, and a `quantity` integer \(a number without a fraction or exponent part\)\.

 Alternatively, you could include part of this schema \(for example, the item definition of the `bins` array\) in a separate section of the same file, and use the `$ref` primitive to reference this reusable definition in other parts of the schema\. Using `$ref`, the preceding model definition file can be expressed as follows: 

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "GroceryStoreInputModel",
  "type": "object",
  "properties": {
    "department": { "type": "string" },
    "categories": {
      "type": "array",
      "items": { "type": "string" }
    },
    "bins": {
      "type": "array",
      "items": {
        "$ref": "#/definitions/Bin"
      }
    }
  },
  "definitions": { 
    "Bin" : {
      "type": "object",
      "properties": {
        "category": { "type": "string" },
        "type": { "type": "string" },
        "price": { "type": "number" },
        "unit": { "type": "string" },
        "quantity": { "type": "integer" }
      }
    }
  }
}
```

The `definitions` section contains the schema definition of the `Bin` item that is referenced in the `bins` array with `"$ref": "#/definitions/Bin"`\. Using reusable definitions this way makes your model definition easier to read\.

 In addition, you can also reference another model schema defined in an external model file by setting that model's URL as the value of the `$ref` property: `"$ref": "https://apigateway.amazonaws.com/restapis/{restapi_id}/models/{model_name}"`\. For example, suppose you have the following full\-fledged model named `Bin` that is created under an API with an identifier of `fugvjdxtri`: 

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "GroceryStoreInputModel",
  "type": "object",
  "properties": {
      "Bin" : {
        "type": "object",
        "properties": {
            "category": { "type": "string" },
            "type": { "type": "string" },
            "price": { "type": "number" },
            "unit": { "type": "string" },
            "quantity": { "type": "integer" }
        }
      }
   }  
}
```

You can then reference it from the `GroceryStoreInputModel` from the same API, as follows:

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "GroceryStoreInputModel",
  "type": "object",
  "properties": {
    "department": { "type": "string" },
    "categories": {
      "type": "array",
      "items": { "type": "string" }
    },
    "bins": {
      "type": "array",
      "items": {
        "$ref": "https://apigateway.amazonaws.com/restapis/fugvjdxtri/models/Bin2"
      }
    }
  }
}
```

The referencing and referenced models must be from the same API\.

The examples don't use advanced JSON schema features, such as specifying required items, minimums and maximums \(for allowed string lengths, numeric values, and array item lengths\), and regular expressions\. For more information, see [Introducing JSON](http://json.org) and [JSON schema draft 4](https://tools.ietf.org/html/draft-zyp-json-schema-04)\.

For more complex JSON data formats and their models, see the following examples:
+ [Input model \(photos example\)](example-photos.md#example-photos-input-model) and [Output model \(photos example\)](example-photos.md#example-photos-output-model) in the [Photos example](example-photos.md)
+ [Input model \(news article example\)](example-news-article.md#example-news-article-input-model) and [Output model \(news article example\)](example-news-article.md#example-news-article-output-model) in the [News article example](example-news-article.md)
+ [Input model \(sales invoice example\)](example-invoice.md#example-invoice-input-model) and [Output model \(sales invoice example\)](example-invoice.md#example-invoice-output-model) in the [Sales invoice example](example-invoice.md)
+ [Input model \(employee record example\)](example-employee.md#example-employee-input-model) and [Output model \(employee record example\)](example-employee.md#example-employee-output-model) in the [Employee record example](example-employee.md)

To experiment with models in API Gateway, follow the instructions in [Map response payload](api-gateway-create-api-step-by-step.md#getting-started-models), specifically [Step 1: Create models](api-gateway-create-api-step-by-step.md#getting-started-models-add-models)\.

## Mapping templates<a name="models-mappings-mappings"></a>

 When the backend returns the query results \(shown in the [Models](#models-mappings-models) section\), the manager of the produce department might be interested in reading them, as follows: 

```
{
  "choices": [
    {
      "kind": "apples",
      "suggestedPrice": "1.99 per pound",
      "available": 232
    },
    {
      "kind": "bananas",
      "suggestedPrice": "0.19 per each",
      "available": 112
    },
    {
      "kind": "carrots",
      "suggestedPrice": "1.29 per bag",
      "available": 57
    }
  ]
}
```

 To enable this, we need to provide API Gateway with a mapping template to translate the data from the backend format\. The following mapping template does that\. 

```
#set($inputRoot = $input.path('$'))
{
  "choices": [
#foreach($elem in $inputRoot.bins)
    {
      "kind": "$elem.type",
      "suggestedPrice": "$elem.price per $elem.unit",
      "available": $elem.quantity
    }#if($foreach.hasNext),#end
            
#end
  ]
}
```

Let us now examine some details of the preceding output mapping template:
+ The `$inputRoot` variable represents the root object in the original JSON data from the previous section\. The variables in an output mapping template map to the original JSON data, not the desired transformed JSON data schema\.
+ The `choices` array in the output mapping template is mapped from the `bins` array with the root object in the original JSON data \(`$inputRoot.bins`\)\.
+ In the output mapping template, each of the objects in the `choices` array \(represented by `$elem`\) are mapped from the corresponding objects in the `bins` array within the root object in the original JSON data\.
+ In the output mapping template, for each of objects in the `choices` object, the values of the `kind` and `available` objects \(represented by `$elem.type` and `$elem.quantity`\) are mapped from the corresponding values of the `type` and `value` objects in each of the objects in the original JSON data's `bins` array, respectively\. 
+ In the output mapping template, for each of objects in the `choices` object, the value of the `suggestedPrice` object is a concatenation of the corresponding value of the `price` and `unit` objects in each of the objects in the original JSON data, respectively, with each value separated by the word `per`\. 

For more information about the Velocity Template Language, see [Apache Velocity \- VTL Reference](https://velocity.apache.org/engine/devel/vtl-reference.html)\. For more information about JSONPath, see [JSONPath \- XPath for JSON](http://goessner.net/articles/JsonPath)\.

 The mapping template assumes that the underlying data is of a JSON object\. It does not require that a model be defined for the data\. As an API developer, you know the data formats at both the front and backends\. That knowledge can guide you to define the necessary mappings without ambiguity\. 

 To have an SDK generated for the API, the preceding data is returned as a language\-specific object\. For strongly typed languages, such as Java, Objective\-C, or Swift, the object corresponds to a user\-defined data type \(UDT\)\. API Gateway creates such a UDT if you provide it with a data model\. For the preceding method response example, you can define the following payload model in the integration response: 

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "GroceryStoreOutputModel",
  "type": "object",
  "properties": {
    "choices": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "kind": { "type": "string" },
          "suggestedPrice": { "type": "string" },
          "available": { "type": "integer" }
        }
      }
    }
  }
}
```

In this model, the JSON schema is expressed as follows:
+ The `$schema` object represents a valid JSON schema version identifier\. In this example, it refers to JSON schema, draft v4\.
+ The `title` object is a human\-readable identifier for the model\. In this example, it is `GroceryStoreOutputModel`\.
+ The top\-level, or root, construct in the JSON data is an object\.
+ The root object in the JSON data contains an array of objects\.
+ Each object in the array of objects contains a `kind` string, a `suggestedPrice` string, and an `available` integer \(a number without a fraction or exponent part\)\.

With this model, you can call an SDK to retrieve the `kind`, `suggestedPrice`, and `available` property values by reading the `GroceryStoreOutputModel[i].kind`, `GroceryStoreOutputModel[i].suggestedPrice`, and `GroceryStoreOutputModel[i].available` properties, respectively\. If no model is provided, API Gateway uses the empty model to create a default UDT\. In this case, you aren't able to read these properties using a strongly\-typed SDK\.

To explore more complex mapping templates, see the following examples:
+ [Input mapping template \(photos example\)](example-photos.md#example-photos-input-mapping) and [Output mapping template \(photos example\)](example-photos.md#example-photos-output-mapping) in the [Photos example](example-photos.md)
+ [Input mapping template \(news article example\)](example-news-article.md#example-news-article-input-mapping) and [Output mapping template \(news article example\)](example-news-article.md#example-news-article-output-mapping) in the [News article example](example-news-article.md)
+ [Input mapping template \(sales invoice example\)](example-invoice.md#example-invoice-input-mapping) and [Output mapping template \(sales invoice example\)](example-invoice.md#example-invoice-output-mapping) in the [Sales invoice example](example-invoice.md)
+ [Input mapping template \(employee record example\)](example-employee.md#example-employee-input-mapping) and [Output mapping template \(employee record example\)](example-employee.md#example-employee-output-mapping) in the [Employee record example](example-employee.md)

To experiment with mapping templates in API Gateway, follow the instructions in [Map response payload](api-gateway-create-api-step-by-step.md#getting-started-models), specifically [Step 5: Set up and test the methods](api-gateway-create-api-step-by-step.md#getting-started-models-set-methods)\.

## Next steps<a name="models-mappings-tasks"></a>

For additional things you can do with models and mapping templates, see the following:
+ [Working with models and mapping templates](models-mappings.md)
+ [Models and mapping template examples](rest-api-develop-models-mapping-examples.md)
+ [Amazon API Gateway API request and response data mapping reference](request-response-data-mappings.md)