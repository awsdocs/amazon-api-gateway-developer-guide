# Simple calculator Lambda function<a name="simple-calc-nodejs-lambda-function"></a>

As an illustration, we will use a Node\.js Lambda function that performs the binary operations of addition, subtraction, multiplication and division\. 

**Topics**
+ [Simple calculator Lambda function input format](#simple-calc-lambda-function-input-format)
+ [Simple calculator Lambda function output format](#simple-calc-lambda-function-output-format)
+ [Simple calculator Lambda function implementation](#simple-calc-lambda-function-implementation)
+ [Create the simple calculator Lambda function](#simple-calc-lambda-function-create)

## Simple calculator Lambda function input format<a name="simple-calc-lambda-function-input-format"></a>

This function takes an input of the following format:

```
{ "a": "Number", "b": "Number", "op": "string"}
```

where `op` can be any of `(+, -, *, /, add, sub, mul, div)`\.

## Simple calculator Lambda function output format<a name="simple-calc-lambda-function-output-format"></a>

When an operation succeeds, it returns the result of the following format:

```
{ "a": "Number", "b": "Number", "op": "string", "c": "Number"}
```

where `c` holds the result of the calculation\.

## Simple calculator Lambda function implementation<a name="simple-calc-lambda-function-implementation"></a>

The implementation of the Lambda function is as follows:

```
console.log('Loading the calculator function');

exports.handler = function(event, context, callback) {
    console.log('Received event:', JSON.stringify(event, null, 2));
    if (event.a === undefined || event.b === undefined || event.op === undefined) {
        callback("400 Invalid Input");
    }
    
    var res = {};
    res.a = Number(event.a);
    res.b = Number(event.b);
    res.op = event.op;
    
    if (isNaN(event.a) || isNaN(event.b)) {
        callback("400 Invalid Operand");
    }

    switch(event.op)
    {
        case "+":
        case "add":
            res.c = res.a + res.b;
            break;
        case "-":
        case "sub":
            res.c = res.a - res.b;
            break;
        case "*":
        case "mul":
            res.c = res.a * res.b;
            break;
        case "/":
        case "div":
            res.c = res.b===0 ? NaN : Number(event.a) / Number(event.b);
            break;
        default:
            callback("400 Invalid Operator");
            break;
    }
    callback(null, res);
};
```

## Create the simple calculator Lambda function<a name="simple-calc-lambda-function-create"></a>

You can use the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/) to create the function, pasting the above code listing into the online code editor as follows\.

![\[Create the SimpleCalc Lambda function in Node.js\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/simple-calc-nodejs-lambda-function-create.png)