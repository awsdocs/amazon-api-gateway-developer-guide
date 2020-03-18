# Photos Example \(API Gateway Models and Mapping Templates\)<a name="example-photos"></a>

The following sections provide examples of models and mapping templates that could be used for a sample photo API in API Gateway\. For more information about models and mapping templates in API Gateway, see [Working with Models and Mapping Templates](models-mappings.md)\.

**Topics**
+ [Original Data \(Photos Example\)](#example-photos-original-data)
+ [Input Model \(Photos Example\)](#example-photos-input-model)
+ [Input Mapping Template \(Photos Example\)](#example-photos-input-mapping)
+ [Transformed Data \(Photos Example\)](#example-photos-transformed-data)
+ [Output Model \(Photos Example\)](#example-photos-output-model)
+ [Output Mapping Template \(Photos Example\)](#example-photos-output-mapping)

## Original Data \(Photos Example\)<a name="example-photos-original-data"></a>

The following is the original JSON data for the photos example:

```
{
  "photos": {
    "page": 1,
    "pages": "1234",
    "perpage": 100,
    "total": "123398",
    "photo": [
      {
        "id": "12345678901",
        "owner": "23456789@A12",
        "secret": "abc123d456",
        "server": "1234",
        "farm": 1,
        "title": "Sample photo 1",
        "ispublic": 1,
        "isfriend": 0,
        "isfamily": 0
      },
      {
        "id": "23456789012",
        "owner": "34567890@B23",
        "secret": "bcd234e567",
        "server": "2345",
        "farm": 2,
        "title": "Sample photo 2",
        "ispublic": 1,
        "isfriend": 0,
        "isfamily": 0
      }
    ]
  }
}
```

## Input Model \(Photos Example\)<a name="example-photos-input-model"></a>

The following is the input model that corresponds to the original JSON data for the photos example:

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "PhotosInputModel",
  "type": "object",
  "properties": {
    "photos": {
      "type": "object",
      "properties": {
        "page": { "type": "integer" },
        "pages": { "type": "string" },
        "perpage": { "type": "integer" },
        "total": { "type": "string" },
        "photo": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "id": { "type": "string" },
              "owner": { "type": "string" },
              "secret": { "type": "string" },
              "server": { "type": "string" },
              "farm": { "type": "integer" },
              "title": { "type": "string" },
              "ispublic": { "type": "integer" },
              "isfriend": { "type": "integer" },
              "isfamily": { "type": "integer" }
            }
          }
        }
      }
    }
  }
}
```

## Input Mapping Template \(Photos Example\)<a name="example-photos-input-mapping"></a>

The following is the input mapping template that corresponds to the original JSON data for the photos example:

```
#set($inputRoot = $input.path('$'))
{
  "photos": {
    "page": $inputRoot.photos.page,
    "pages": "$inputRoot.photos.pages",
    "perpage": $inputRoot.photos.perpage,
    "total": "$inputRoot.photos.total",
    "photo": [
#foreach($elem in $inputRoot.photos.photo)
      {
        "id": "$elem.id",
        "owner": "$elem.owner",
        "secret": "$elem.secret",
        "server": "$elem.server",
        "farm": $elem.farm,
        "title": "$elem.title",
        "ispublic": $elem.ispublic,
        "isfriend": $elem.isfriend,
        "isfamily": $elem.isfamily
      }#if($foreach.hasNext),#end
			
#end
    ]
  }
}
```

## Transformed Data \(Photos Example\)<a name="example-photos-transformed-data"></a>

The following is one example of how the original photos example JSON data could be transformed for output:

```
{
  "photos": [
    {
      "id": "12345678901",
      "owner": "23456789@A12",
      "title": "Sample photo 1",
      "ispublic": 1,
      "isfriend": 0,
      "isfamily": 0
    },
    {
      "id": "23456789012",
      "owner": "34567890@B23",
      "title": "Sample photo 2",
      "ispublic": 1,
      "isfriend": 0,
      "isfamily": 0
    }
  ]
}
```

## Output Model \(Photos Example\)<a name="example-photos-output-model"></a>

The following is the output model that corresponds to the transformed JSON data format:

```
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "PhotosOutputModel",
  "type": "object",
  "properties": {
    "photos": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "id": { "type": "string" },
          "owner": { "type": "string" },
          "title": { "type": "string" },
          "ispublic": { "type": "integer" },
          "isfriend": { "type": "integer" },
          "isfamily": { "type": "integer" }
        }
      }
    }
  }
}
```

## Output Mapping Template \(Photos Example\)<a name="example-photos-output-mapping"></a>

The following is the output mapping template that corresponds to the transformed JSON data format\. The template variables here are based on the original, not transformed, JSON data format:

```
#set($inputRoot = $input.path('$'))
{
  "photos": [
#foreach($elem in $inputRoot.photos.photo)
    {
      "id": "$elem.id",
      "owner": "$elem.owner",
      "title": "$elem.title",
      "ispublic": $elem.ispublic,
      "isfriend": $elem.isfriend,
      "isfamily": $elem.isfamily
    }#if($foreach.hasNext),#end
		
#end
  ]
}
```