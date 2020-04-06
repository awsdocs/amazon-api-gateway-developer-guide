# API Gateway API key file format<a name="api-key-file-format"></a>

API Gateway can import API keys from external files of a comma\-separated value \(CSV\) format, and then associate the imported keys with one or more usage plans\. The imported file must contain the `Name` and `Key` columns\. The column header names aren't case sensitive, and columns can be in any order, as shown in the following example: 

```
Key,name
apikey1234abcdefghij0123456789,MyFirstApiKey
```

A `Key` value must be between 30 and 128 characters\. 

An API key file can also have the `Description`, `Enabled`, or `UsagePlanIds` column, as shown in the following example: 

```
Name,key,description,Enabled,usageplanIds
MyFirstApiKey,apikey1234abcdefghij0123456789,An imported key,TRUE,c7y23b
```

When a key is associated with more than one usage plan, the `UsagePlanIds` value is a comma\-separated string of the usage plan IDs, enclosed with a pair of double or single quotes, as shown in the following example:

```
Enabled,Name,key,UsageplanIds
true,MyFirstApiKey,apikey1234abcdefghij0123456789,"c7y23b,glvrsr"
```

Unrecognized columns are permitted, but are ignored\. The default value is an empty string or a `true` Boolean value\. 

The same API key can be imported multiple times, with the most recent version overwriting the previous one\. Two API keys are identical if they have the same `key` value\. 