# OpenAPI definitions of a sample API as a Kinesis proxy<a name="api-as-kinesis-proxy-export-swagger-with-extensions"></a>

Following are OpenAPI definitions for the sample API as a Kinesis proxy used in this tutorial\. 

------
#### [ OpenAPI 3\.0 ]

```
{
   "openapi": "3.0.0",
   "info": {
      "version": "2016-03-31T18:25:32Z",
      "title": "KinesisProxy"
   },
   "paths": {
      "/streams": {
         "get": {
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/ListStreams",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            }
         }
      },
      "/streams/{stream-name}": {
         "get": {
            "parameters": [
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/DescribeStream",
               "httpMethod": "POST",
               "type": "aws"
            }
         },
         "post": {
            "parameters": [
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n    \"ShardCount\": 5,\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/CreateStream",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            }
         },
         "delete": {
            "parameters": [
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "headers": {
                     "Content-Type": {
                        "schema": {
                           "type": "string"
                        }
                     }
                  },
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               },
               "400": {
                  "description": "400 response",
                  "headers": {
                     "Content-Type": {
                        "schema": {
                           "type": "string"
                        }
                     }
                  }
               },
               "500": {
                  "description": "500 response",
                  "headers": {
                     "Content-Type": {
                        "schema": {
                           "type": "string"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "4\\d{2}": {
                     "statusCode": "400",
                     "responseParameters": {
                        "method.response.header.Content-Type": "integration.response.header.Content-Type"
                     }
                  },
                  "default": {
                     "statusCode": "200",
                     "responseParameters": {
                        "method.response.header.Content-Type": "integration.response.header.Content-Type"
                     }
                  },
                  "5\\d{2}": {
                     "statusCode": "500",
                     "responseParameters": {
                        "method.response.header.Content-Type": "integration.response.header.Content-Type"
                     }
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/DeleteStream",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            }
         }
      },
      "/streams/{stream-name}/record": {
         "put": {
            "parameters": [
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                 "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\",\n    \"Records\": [\n    #foreach($elem in $input.path('$.records'))\n    {\n    \"Data\": \"$util.base64Encode($elem.data)\",\n    \"PartitionKey\": \"$elem.partition-key\"\n}"    }#if($foreach.hasNext),#end\n    #end\n    ]\n
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/PutRecord",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            }
         }
      },
      "/streams/{stream-name}/records": {
         "get": {
            "parameters": [
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               },
               {
                  "name": "Shard-Iterator",
                  "in": "header",
                  "required": false,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n    \"ShardIterator\": \"$input.params('Shard-Iterator')\"\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/GetRecords",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            }
         },
         "put": {
            "parameters": [
               {
                  "name": "Content-Type",
                  "in": "header",
                  "required": false,
                  "schema": {
                     "type": "string"
                  }
               },
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\",\n    \"Records\": [\n          {\n            \"Data\": \"$util.base64Encode($elem.data)\",\n            \"PartitionKey\": \"$elem.partition-key\"\n          }#if($foreach.hasNext),#end\n    ]\n}",
                  "application/x-amz-json-1.1": "{\n  \"StreamName\": \"$input.params('stream-name')\",\n  \"records\" : [\n    {\n        \"Data\" : \"$elem.data\",\n        \"PartitionKey\" : \"$elem.partition-key\"\n    }#if($foreach.hasNext),#end\n  ]\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/PutRecords",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            },
            "requestBody": {
               "content": {
                  "application/json": {
                     "schema": {
                        "$ref": "#/components/schemas/PutRecordsMethodRequestPayload"
                     }
                  },
                  "application/x-amz-json-1.1": {
                     "schema": {
                        "$ref": "#/components/schemas/PutRecordsMethodRequestPayload"
                     }
                  }
               },
               "required": true
            }
         }
      },
      "/streams/{stream-name}/sharditerator": {
         "get": {
            "parameters": [
               {
                  "name": "stream-name",
                  "in": "path",
                  "required": true,
                  "schema": {
                     "type": "string"
                  }
               },
               {
                  "name": "shard-id",
                  "in": "query",
                  "required": false,
                  "schema": {
                     "type": "string"
                  }
               }
            ],
            "responses": {
               "200": {
                  "description": "200 response",
                  "content": {
                     "application/json": {
                        "schema": {
                           "$ref": "#/components/schemas/Empty"
                        }
                     }
                  }
               }
            },
            "x-amazon-apigateway-integration": {
               "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
               "responses": {
                  "default": {
                     "statusCode": "200"
                  }
               },
               "requestTemplates": {
                  "application/json": "{\n    \"ShardId\": \"$input.params('shard-id')\",\n    \"ShardIteratorType\": \"TRIM_HORIZON\",\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
               },
               "uri": "arn:aws:apigateway:us-east-1:kinesis:action/GetShardIterator",
               "httpMethod": "POST",
               "requestParameters": {
                  "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
               },
               "type": "aws"
            }
         }
      }
   },
   "servers": [
      {
         "url": "https://wd4zclrobb.execute-api.us-east-1.amazonaws.com/{basePath}",
         "variables": {
            "basePath": {
              "default": "/test"
            }
         }
      }
   ],
   "components": {
      "schemas": {
         "PutRecordsMethodRequestPayload": {
            "type": "object",
            "properties": {
               "records": {
                  "type": "array",
                  "items": {
                     "type": "object",
                     "properties": {
                        "data": {
                           "type": "string"
                        },
                        "partition-key": {
                           "type": "string"
                        }
                     }
                  }
               }
            }
         },
         "Empty": {
            "type": "object"
         }
      }
   }
}
```

------
#### [ OpenAPI 2\.0 ]

```
{
  "swagger": "2.0",
  "info": {
    "version": "2016-03-31T18:25:32Z",
    "title": "KinesisProxy"
  },
  "host": "wd4zclrobb.execute-api.us-east-1.amazonaws.com",
  "basePath": "/test",
  "schemes": [
    "https"
  ],
  "paths": {
    "/streams": {
      "get": {
        "consumes": [
          "application/json"
        ],
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/ListStreams",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      }
    },
    "/streams/{stream-name}": {
      "get": {
        "consumes": [
          "application/json"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          }
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/DescribeStream",
          "httpMethod": "POST",
          "type": "aws"
        }
      },
      "post": {
        "consumes": [
          "application/json"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          }
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"ShardCount\": 5,\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/CreateStream",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      },
      "delete": {
        "consumes": [
          "application/json"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          }
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "schema": {
              "$ref": "#/definitions/Empty"
            },
            "headers": {
              "Content-Type": {
                "type": "string"
              }
            }
          },
          "400": {
            "description": "400 response",
            "headers": {
              "Content-Type": {
                "type": "string"
              }
            }
          },
          "500": {
            "description": "500 response",
            "headers": {
              "Content-Type": {
                "type": "string"
              }
            }
          }
        },
        "x-amazon-apigateway-integration": {
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "4\\d{2}": {
              "statusCode": "400",
              "responseParameters": {
                "method.response.header.Content-Type": "integration.response.header.Content-Type"
              }
            },
            "default": {
              "statusCode": "200",
              "responseParameters": {
                "method.response.header.Content-Type": "integration.response.header.Content-Type"
              }
            },
            "5\\d{2}": {
              "statusCode": "500",
              "responseParameters": {
                "method.response.header.Content-Type": "integration.response.header.Content-Type"
              }
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/DeleteStream",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      }
    },
    "/streams/{stream-name}/record": {
      "put": {
        "consumes": [
          "application/json"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          }
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\",\n    \"Records\": [\n    #foreach($elem in $input.path('$.records'))\n    {\n    \"Data\": \"$util.base64Encode($elem.data)\",\n    \"PartitionKey\": \"$elem.partition-key\"\n}"    }#if($foreach.hasNext),#end\n    #end\n    ]\n
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/PutRecord",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      }
    },
    "/streams/{stream-name}/records": {
      "get": {
        "consumes": [
          "application/json"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          },
          {
            "name": "Shard-Iterator",
            "in": "header",
            "required": false,
            "type": "string"
          }
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"ShardIterator\": \"$input.params('Shard-Iterator')\"\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/GetRecords",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      },
      "put": {
        "consumes": [
          "application/json",
          "application/x-amz-json-1.1"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "Content-Type",
            "in": "header",
            "required": false,
            "type": "string"
          },
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          },
          {
            "in": "body",
            "name": "PutRecordsMethodRequestPayload",
            "required": true,
            "schema": {
              "$ref": "#/definitions/PutRecordsMethodRequestPayload"
            }
          }
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"StreamName\": \"$input.params('stream-name')\",\n    \"Records\": [\n       #foreach($elem in $input.path('$.records'))\n          {\n            \"Data\": \"$util.base64Encode($elem.data)\",\n            \"PartitionKey\": \"$elem.partition-key\"\n          }#if($foreach.hasNext),#end\n        #end\n    ]\n}",
            "application/x-amz-json-1.1": "#set($inputRoot = $input.path('$'))\n{\n  \"StreamName\": \"$input.params('stream-name')\",\n  \"records\" : [\n    #foreach($elem in $inputRoot.records)\n    {\n        \"Data\" : \"$elem.data\",\n        \"PartitionKey\" : \"$elem.partition-key\"\n    }#if($foreach.hasNext),#end\n    #end\n  ]\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/PutRecords",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      }
    },
    "/streams/{stream-name}/sharditerator": {
      "get": {
        "consumes": [
          "application/json"
        ],
        "produces": [
          "application/json"
        ],
        "parameters": [
          {
            "name": "stream-name",
            "in": "path",
            "required": true,
            "type": "string"
          },
          {
            "name": "shard-id",
            "in": "query",
            "required": false,
            "type": "string"
          }
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
          "credentials": "arn:aws:iam::123456789012:role/apigAwsProxyRole",
          "responses": {
            "default": {
              "statusCode": "200"
            }
          },
          "requestTemplates": {
            "application/json": "{\n    \"ShardId\": \"$input.params('shard-id')\",\n    \"ShardIteratorType\": \"TRIM_HORIZON\",\n    \"StreamName\": \"$input.params('stream-name')\"\n}"
          },
          "uri": "arn:aws:apigateway:us-east-1:kinesis:action/GetShardIterator",
          "httpMethod": "POST",
          "requestParameters": {
            "integration.request.header.Content-Type": "'application/x-amz-json-1.1'"
          },
          "type": "aws"
        }
      }
    }
  },
  "definitions": {
    "PutRecordsMethodRequestPayload": {
      "type": "object",
      "properties": {
        "records": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "data": {
                "type": "string"
              },
              "partition-key": {
                "type": "string"
              }
            }
          }
        }
      }
    },
    "Empty": {
      "type": "object"
    }
  }
}
```

------