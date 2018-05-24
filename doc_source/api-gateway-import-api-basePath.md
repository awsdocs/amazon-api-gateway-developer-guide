# Set the Swagger `basePath` Property<a name="api-gateway-import-api-basePath"></a>

 In Swagger, you can use the `[basePath](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)` property to provide one or more path parts that precede each path defined in the paths property\. Because API Gateway has several ways to express a resource’s path, the Import API feature provides three options for interpreting the `basePath` property during an import: 

## ignore<a name="api-gateway-import-api-basePath-ignore"></a>

 If the Swagger file has a `basePath` value of `/a/b/c` and the `paths` property contains `/e` and `/f`, the following POST or PUT request: 

```
POST /restapis?mode=import&basepath=ignore
```

```
PUT /restapis/api_id?basepath=ignore
```

 will result in the following resources in the API: 
+ /
+ /e
+ /f

 The effect is to treat the `basePath` as if it was not present, and all of the declared API resources are served relative to the host\. This can be used, for example, when you have a custom domain name with an API mapping that does not include a *Base Path* and a *Stage* value that refers to your production stage\. 

**Note**  
 API Gateway will automatically create a root resource for you, even if it is not explicitly declared in your definition file\. 

 When unspecified, `basePath` takes `ignore` by default\. 

## prepend<a name="api-gateway-import-api-basePath-prepend"></a>

 If the Swagger file has a `basePath` value of `/a/b/c` and the `paths` property contains `/e` and `/f`, the following POST or PUT request: 

```
POST /restapis?mode=import&basepath=prepend
```

```
PUT /restapis/api_id?basepath=prepend
```

 will result in the following resources in the API: 
+ /
+ /a
+ /a/b
+ /a/b/c
+ /a/b/c/e
+ /a/b/c/f

 The effect is to treat the `basePath` as specifying additional resources \(without methods\) and to add them to the declared resource set\. This can be used, for example, when different teams are responsible for different parts of an API and the `basePath` could reference the path location for each team's API part\. 

**Note**  
 API Gateway will automatically create intermediate resources for you, even if they are not explicitly declared in your definition\. 

## split<a name="api-gateway-import-api-basePath-split"></a>

 If the Swagger file has a `basePath` value of `/a/b/c` and the `paths` property contains `/e` and `/f`, the following POST or PUT request: 

```
POST /restapis?mode=import&basepath=split
```

```
PUT /restapis/api_id?basepath=split
```

 will result in the following resources in the API: 
+ /
+ /b
+ /b/c
+ /b/c/e
+ /b/c/f

 The effect is to treat top\-most path part, `"/a"`, as the beginning of each resource's path, and to create additional \(no method\) resources within the API itself\. This could, for example, be used when `"a"` is a stage name that you want to expose as part of your API\. 