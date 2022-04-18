# Tutorial: Build a CRUD API with Lambda and DynamoDB<a name="http-api-dynamo-db"></a>

In this tutorial, you create a serverless API that creates, reads, updates, and deletes items from a DynamoDB table\. DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability\. This tutorial takes approximately 30 minutes to complete, and you can do it within the [AWS Free Tier](http://aws.amazon.com/free)\.

First, you create a [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) table using the DynamoDB console\. Then you create a [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function using the AWS Lambda console\. Next, you create an HTTP API using the API Gateway console\. Lastly, you test your API\.

When you invoke your HTTP API, API Gateway routes the request to your Lambda function\. The Lambda function interacts with DynamoDB, and returns a response to API Gateway\. API Gateway then returns a response to you\.

![\[Architectural overview of the API that you create in this tutorial. Clients use an API Gateway HTTP API to invoke a Lambda function. The Lambda function interacts with DynamoDB and then returns a response to clients.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/ddb-crud.png)

To complete this exercise, you need an AWS account and an AWS Identity and Access Management user with console access\. For more information, see [Prerequisites for getting started with API Gateway](setting-up.md)\.

In this tutorial, you use the AWS Management Console\. For an AWS SAM template that creates this API and all related resources, see [samples/http-dynamo-tutorial.zip](samples/http-dynamo-tutorial.zip)\.

**Topics**
+ [Step 1: Create a DynamoDB table](#http-api-dynamo-db-create-table)
+ [Step 2: Create a Lambda function](#http-api-dynamo-db-create-function)
+ [Step 3: Create an HTTP API](#http-api-dynamo-db-create-api)
+ [Step 4: Create routes](#http-api-dynamo-db-create-routes)
+ [Step 5: Create an integration](#http-api-dynamo-db-create-integration)
+ [Step 6: Attach your integration to routes](#http-api-dynamo-db-attach-integrations)
+ [Step 7: Test your API](#http-api-dynamo-db-invoke-api)
+ [Step 8: Clean up](#http-api-dynamo-db-cleanup)
+ [Next steps: Automate with AWS SAM or AWS CloudFormation](#http-api-dynamo-db-next-steps)

## Step 1: Create a DynamoDB table<a name="http-api-dynamo-db-create-table"></a>

You use a [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) table to store data for your API\. 

Each item has a unique ID, which we use as the [partition key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html#HowItWorks.CoreComponents.PrimaryKey) for the table\.

**To create a DynamoDB table**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create table**\.

1. For **Table name**, enter **http\-crud\-tutorial\-items**\.

1. For **Primary key**, enter **id**\.

1. Choose **Create**\. 

## Step 2: Create a Lambda function<a name="http-api-dynamo-db-create-function"></a>

You create a [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function for the backend of your API\. This Lambda function creates, reads, updates, and deletes items from DynamoDB\. The function uses [ events from API Gateway](http-api-develop-integrations-lambda.md#http-api-develop-integrations-lambda.proxy-format) to determine how to interact with DynamoDB\. For simplicity this tutorial uses a single Lambda function\. As a best practice, you should create separate functions for each route\.

**To create a Lambda function**

1. Sign in to the Lambda console at [https://console\.aws\.amazon\.com/lambda](https://console.aws.amazon.com/lambda)\.

1. Choose **Create function**\.

1. For **Function name**, enter **http\-crud\-tutorial\-function**\.

1. Under **Permissions** choose **Change default execution role**\.

1. Select **Create a new role from AWS policy templates**\.

1. For **Role name**, enter **http\-crud\-tutorial\-role**\.

1. For **Policy templates**, choose **Simple microservice permissions**\. This policy grants the Lambda function permission to interact with DynamoDB\.
**Note**  
This tutorial uses a managed policy for simplicity\. As a best practice, you should create your own IAM policy to grant the minimum permissions required\.

1. Choose **Create function**\.

1. Open `index.js` in the console's code editor, and replace its contents with the following code\. Choose **Deploy** to update your function\.

```
const AWS = require("aws-sdk");

const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event, context) => {
  let body;
  let statusCode = 200;
  const headers = {
    "Content-Type": "application/json"
  };

  try {
    switch (event.routeKey) {
      case "DELETE /items/{id}":
        await dynamo
          .delete({
            TableName: "http-crud-tutorial-items",
            Key: {
              id: event.pathParameters.id
            }
          })
          .promise();
        body = `Deleted item ${event.pathParameters.id}`;
        break;
      case "GET /items/{id}":
        body = await dynamo
          .get({
            TableName: "http-crud-tutorial-items",
            Key: {
              id: event.pathParameters.id
            }
          })
          .promise();
        break;
      case "GET /items":
        body = await dynamo.scan({ TableName: "http-crud-tutorial-items" }).promise();
        break;
      case "PUT /items":
        let requestJSON = JSON.parse(event.body);
        await dynamo
          .put({
            TableName: "http-crud-tutorial-items",
            Item: {
              id: requestJSON.id,
              price: requestJSON.price,
              name: requestJSON.name
            }
          })
          .promise();
        body = `Put item ${requestJSON.id}`;
        break;
      default:
        throw new Error(`Unsupported route: "${event.routeKey}"`);
    }
  } catch (err) {
    statusCode = 400;
    body = err.message;
  } finally {
    body = JSON.stringify(body);
  }

  return {
    statusCode,
    body,
    headers
  };
};
```

## Step 3: Create an HTTP API<a name="http-api-dynamo-db-create-api"></a>

The HTTP API provides an HTTP endpoint for your Lambda function\. In this step, you create an empty API\. In the following steps, you configure routes and integrations to connect your API and your Lambda function\.



**To create an HTTP API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **Create API**, and then for **HTTP API**, choose **Build**\.

1. For **API name**, enter **http\-crud\-tutorial\-api**\.

1. Choose **Next**\.

1. For **Configure routes**, choose **Next** to skip route creation\. You create routes later\.

1. Review the stage that API Gateway creates for you, and then choose **Next**\.

1. Choose **Create**\.

## Step 4: Create routes<a name="http-api-dynamo-db-create-routes"></a>

Routes are a way to send incoming API requests to backend resources\. Routes consist of two parts: an HTTP method and a resource path, for example, `GET /items`\. For this example API, we create four routes:
+ `GET /items/{id}`
+ `GET /items`
+ `PUT /items`
+ `DELETE /items/{id}`

**To create routes**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Routes**\.

1. Choose **Create**\.

1. For **Method**, choose **GET**\.

1. For the path, enter **/items/\{id\}**\. The `{id}` at the end of the path is a path parameter that API Gateway retrieves from the request path when a client makes a request\.

1. Choose **Create table**\.

1. Repeat steps 4\-7 for `GET /items`, `DELETE /items/{id}`, and `PUT /items`\.

![\[Your API has routes for GET /items, GET /items/{id},DELETE /items/{id}, and PUT /items.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/ddb-create-routes.png)

## Step 5: Create an integration<a name="http-api-dynamo-db-create-integration"></a>

You create an integration to connect a route to backend resources\. For this example API, you create one Lambda integration that you use for all routes\.

**To create an integration**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Integrations**\.

1. Choose **Manage integrations** and then choose **Create**\.

1. Skip **Attach this integration to a route**\. You complete that in a later step\.

1. For **Integration type**, choose **Lambda function**\.

1. For **Lambda function**, enter **http\-crud\-tutorial\-function**\.

1. Choose **Create**\.

## Step 6: Attach your integration to routes<a name="http-api-dynamo-db-attach-integrations"></a>

For this example API, you use the same Lambda integration for all routes\. After you attach the integration to all of the API's routes, your Lambda function is invoked when a client calls any of your routes\.



**To attach integrations to routes**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Integrations**\.

1. Choose a route\.

1. Under **Choose an existing integration**, choose **http\-crud\-tutorial\-function**\.

1. Choose **Attach integration**\.

1. Repeat steps 4\-6 for all routes\. 

All routes show that an AWS Lambda integration is attached\.

![\[The console shows AWS Lambda on all routes to indicate that your integration is attached.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/ddb-attach-integrations.png)

Now that you have an HTTP API with routes and integrations, you can test your API\.

## Step 7: Test your API<a name="http-api-dynamo-db-invoke-api"></a>

To make sure that your API is working, you use [curl](https://curl.se)\.

**To get the URL to invoke your API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Note your API's invoke URL\. It appears under **Invoke URL** on the **Details** page\.  
![\[After you create your API, the console shows your API's invoke URL.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/ddb-invoke-url.png)

1. Copy your API's invoke URL\. 

   The full URL looks like `https://abcdef123.execute-api.us-west-2.amazonaws.com`\. 

**To create or update an item**
+ Use the following command to create or update an item\. The command includes a request body with the item's ID, price, and name\.

  ```
  curl -v -X "PUT" -H "Content-Type: application/json" -d "{\"id\": \"abcdef234\", \"price\": 12345, \"name\": \"myitem\"}" https://abcdef123.execute-api.us-west-2.amazonaws.com/items
  ```

**To get all items**
+ Use the following command to list all items\.

  ```
  curl -v https://abcdef123.execute-api.us-west-2.amazonaws.com/items
  ```

**To get an item**
+ Use the following command to get an item by its ID\.

  ```
  curl -v https://abcdef123.execute-api.us-west-2.amazonaws.com/items/abcdef234
  ```

**To delete an item**

1. Use the following command to delete an item\.

   ```
   curl -v -X "DELETE" https://abcdef123.execute-api.us-west-2.amazonaws.com/items/abcdef234
   ```

1. Get all items to verify that the item was deleted\.

   ```
   curl -v https://abcdef123.execute-api.us-west-2.amazonaws.com/items
   ```

## Step 8: Clean up<a name="http-api-dynamo-db-cleanup"></a>

To prevent unnecessary costs, delete the resources that you created as part of this getting started exercise\. The following steps delete your HTTP API, your Lambda function, and associated resources\.

**To delete a DynamoDB table**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Select your table\.

1. Choose **Delete table**\.

1. Confirm your choice, and choose **Delete**\.

**To delete an HTTP API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. On the **APIs** page, select an API\. Choose **Actions**, and then choose **Delete**\.

1. Choose **Delete**\.

**To delete a Lambda function**

1. Sign in to the Lambda console at [https://console\.aws\.amazon\.com/lambda](https://console.aws.amazon.com/lambda)\.

1. On the **Functions** page, select a function\. Choose **Actions**, and then choose **Delete**\.

1. Choose **Delete**\.

**To delete a Lambda function's log group**

1. In the Amazon CloudWatch console, open the [Log groups page](https://console.aws.amazon.com/cloudwatch/home#logs:)\.

1. On the **Log groups** page, select the function's log group \(`/aws/lambda/http-crud-tutorial-function`\)\. Choose **Actions**, and then choose **Delete log group**\.

1. Choose **Delete**\.

**To delete a Lambda function's execution role**

1. In the AWS Identity and Access Management console, open the [Roles page](https://console.aws.amazon.com/iam/home?#/roles)\.

1. Select the function's role, for example, `http-crud-tutorial-role`\.

1. Choose **Delete role**\.

1. Choose **Yes, delete**\.

## Next steps: Automate with AWS SAM or AWS CloudFormation<a name="http-api-dynamo-db-next-steps"></a>

You can automate the creation and cleanup of AWS resources by using AWS CloudFormation or AWS SAM\. For an example AWS SAM template for this tutorial, see [samples/http-dynamo-tutorial.zip](samples/http-dynamo-tutorial.zip)\.

For example AWS CloudFormation templates, see [example AWS CloudFormation templates](https://github.com/awsdocs/amazon-api-gateway-developer-guide/tree/main/cloudformation-templates)\.
