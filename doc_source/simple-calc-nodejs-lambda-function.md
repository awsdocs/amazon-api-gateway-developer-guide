# Simple calculator Lambda function<a name="simple-calc-nodejs-lambda-function"></a>

As an illustration, we will use a Node\.js Lambda function that performs the binary operations of addition, subtraction, multiplication and division\. 

**Topics**
+ [Simple calculator Lambda function input format](#simple-calc-lambda-function-input-format)
+ [Simple calculator Lambda function output format](#simple-calc-lambda-function-output-format)
+ [Simple calculator Lambda function implementation](#simple-calc-lambda-function-implementation)

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
export const handler = async function (event, context) {
  console.log("Received event:", JSON.stringify(event));

  if (
    event.a === undefined ||
    event.b === undefined ||
    event.op === undefined
  ) {
    return "400 Invalid Input";
  }

  const res = {};
  res.a = Number(event.a);
  res.b = Number(event.b);
  res.op = event.op;
  if (isNaN(event.a) || isNaN(event.b)) {
    return "400 Invalid Operand";
  }
  switch (event.op) {
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
      if (res.b == 0) {
        return "400 Divide by Zero";
      } else {
        res.c = res.a / res.b;
      }
      break;
    default:
      return "400 Invalid Operator";
  }

  return res;
};
```