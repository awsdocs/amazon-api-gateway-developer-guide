# Maintain an API Using the API Gateway Console<a name="maintain-api-using-console"></a>

**Topics**
+ [View a List of APIs in API Gateway](#how-to-view-apis-list)
+ [Delete an API in API Gateway](#how-to-delete-api)
+ [Delete a Resource in API Gateway](#how-to-delete-resource)
+ [View a Methods List in API Gateway](#how-to-view-methods-list)
+ [Delete a Method in API Gateway](#how-to-delete-method)

## View a List of APIs in API Gateway<a name="how-to-view-apis-list"></a>

Use the API Gateway console to view a list of APIs\.

### View a List of APIs with the API Gateway Console<a name="how-to-view-apis-list-console"></a>

You must have an API available in API Gateway\. Follow the instructions in [Creating an API in Amazon API Gateway](how-to-create-api.md)\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. The list of APIs is displayed\.

## Delete an API in API Gateway<a name="how-to-delete-api"></a>

Use the API Gateway console to delete an API\.

**Warning**  
Deleting an API means that you can no longer call it\. This action cannot be undone\.

### Delete an API with the API Gateway Console<a name="how-to-delete-api-console"></a>

You must have deployed the API at least once\. Follow the instructions in [Deploying an API in Amazon API Gateway](how-to-deploy-api.md)\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the box that contains the name of the API you want to delete, choose **Resources**\.

1. Choose **Delete API**\.

1. When prompted to delete the API, choose **Ok**\.

## Delete a Resource in API Gateway<a name="how-to-delete-resource"></a>

Use the API Gateway console to delete a resource\.

**Warning**  
When you delete a resource, you also delete its child resources and methods\. Deleting a resource may cause part of the corresponding API to be unusable\. Deleting a resource cannot be undone\.

### Delete a Resource with the API Gateway Console<a name="how-to-delete-resource-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the box that contains the name of the API for the resource you want to delete, choose **Resources**\.

1. In the **Resources** pane, choose the resource, and then choose **Delete Resource**\.

1. When prompted, choose **Delete**\.

## View a Methods List in API Gateway<a name="how-to-view-methods-list"></a>

Use the API Gateway console to view a list of methods for a resource\.

### View a Methods List with the API Gateway Console<a name="how-to-view-methods-list-console"></a>

You must have methods available in API Gateway\. Follow the instructions in [Build an API with HTTP Custom Integration](api-gateway-create-api-step-by-step.md)\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the box that contains the name of the API, choose **Resources**\.

1. The list of methods is displayed in the **Resources** pane\.
**Tip**  
You may need to choose the arrow next to one or more resources to display all of the available methods\.

## Delete a Method in API Gateway<a name="how-to-delete-method"></a>

Use the API Gateway console to delete a method\.

**Warning**  
Deleting a method may cause part of the corresponding API to become unusable\. Deleting a method cannot be undone\.

### Delete a Method with the API Gateway Console<a name="how-to-delete-method-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the box that contains the name of the API for the method, choose **Resources**\.

1. In the **Resources** pane, choose the arrow next to the resource for the method\.

1. Choose the method, and then choose **Delete Method**\.

1. When prompted, choose **Delete**\.