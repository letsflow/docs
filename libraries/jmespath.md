# JMESPath

[@letsflow/jmespath](https://www.npmjs.com/package/@letsflow/jmespath) is a TypeScript implementation of the [JMESPath](https://jmespath.org/) spec with additional functionality for LetsFlow workflows.

JMESPath is a query language for JSON. It takes a JSON document as input and transforms it into another JSON document given a JMESPath expression.

{% hint style="success" %}
It's important to know and understand how to use JMESPath expressions to unlock the full potential of LetsFlow. It's highly recommended that you follow the **JMESPath Tutorial**.
{% endhint %}

{% embed url="https://jmespath.org/tutorial.html" %}

***

This LetsFlow fork of JMESPath extends the original specs, adding the following functionality;

* [Custom functions](jmespath.md#custom-functions)
* [Root value access](jmespath.md#root-value-access)
* [Number literals](jmespath.md#number-literals)

Additionally, it adds the following functions:

* [if](jmespath.md#if) - Conditional expression
* [range](jmespath.md#range) - Generate a range of numbers or prefixed strings
* [to\_object](jmespath.md#to_object) - Convert an array of key-value pairs into an object
* [json\_serialize](jmespath.md#json_serialize) - Serialize a JSON value to a string
* [json\_parse](jmespath.md#json_parse) - Parse a JSON string into a JSON object
* [sha256](jmespath.md#sha256) - Calculate the SHA-256 hash of a string
* [sha512](jmespath.md#sha512) - Calculate the SHA-512 hash of a string
* [uuid](jmespath.md#uuid) - Generate a UUID
* [regex\_test](jmespath.md#regex_test) - Test if a string matches a regular expression
* [regex\_match](jmespath.md#regex_match) - Return the first match of a regular expression in a string
* [regex\_match\_all](jmespath.md#regex_match_all) - Return all matches of a regular expression in a string
* [regex\_replace](jmespath.md#regex_replace) - Replace parts of a string matching a regular expression with a replacement string

## Installation

```bash
yarn add @letsflow/jmespath
```

## Usage

#### `search(data: JSONValue, expression: string): JSONValue`

```javascript
import { search } from '@letsflow/jmespath';

search(
  { foo: { bar: { baz: [0, 1, 2, 3, 4] } } },
  "foo.bar.baz[2]"
);

// OUTPUTS: 2
```

## Extension to the original specs

The LetsFlow fork of JMESPath adds the following functionality, which is not in the original specs.

### Custom functions

**`registerFunction(functionName: string, customFunction: RuntimeFunction, signature: InputSignature[]): void`**

Extend the list of built-in JMESpath expressions with your own functions.

```javascript
import {search, registerFunction, TYPE_NUMBER} from '@letsflow/jmespath'

search({ foo: 60, bar: 10 }, 'divide(foo, bar)')
// THROWS ERROR: Error: Unknown function: divide()

registerFunction(
  'divide', // FUNCTION NAME
  (resolvedArgs) => {   // CUSTOM FUNCTION
    const [dividend, divisor] = resolvedArgs;
    return dividend / divisor;
  },
  [{ types: [TYPE_NUMBER] }, { types: [TYPE_NUMBER] }] //SIGNATURE
);

search({ foo: 60, bar: 10 }, 'divide(foo, bar)');

// OUTPUTS: 6
```

Optional arguments are supported by setting `{..., optional: true}` in argument signatures

```javascript
  registerFunction(
    'divide',
    (resolvedArgs) => {
      const [dividend, divisor] = resolvedArgs;
      return dividend / divisor ?? 1; //OPTIONAL DIVISOR THAT DEFAULTS TO 1
    },
    [{ types: [TYPE_NUMBER] }, { types: [TYPE_NUMBER], optional: true }] //SIGNATURE
  );

  search({ foo: 60, bar: 10 }, 'divide(foo)');
  
  // OUTPUTS: 60
```

### Root value access

Use `$` to access the document root.

```javascript
search({foo: { bar: 999 }, baz: [1, 2, 3]}, '$.baz[*].[@, $.foo.bar]')
// [ [ 1, 999 ], [ 2, 999 ], [ 3, 999 ] ]
```

### Number literals

Numbers in the root scope are treated as number literals. This means that you don't need to quote numbers with backticks.

```javascript
search([{"bar": 1}, {"bar": 10}], '[?bar==10]')
// [{"bar": 10}]
```

You can also use numbers in arithmetic operations

```javascript
search({}, '16 + 26'); // 42
```

## Additional Functions

### `if`

**Syntax**:

```javascript
if(condition, thenValue, elseValue?)
```

**Description**:\
Returns `thenValue` if `condition` is true, otherwise returns `elseValue`. If `elseValue` is not provided, it defaults to `null`.

**Example**:

```javascript
if(@ > 10, "large", "small")
```

### `range`

**Syntax**:

```javascript
range(start, end, prefix?)
```

**Description**:\
Generates an array of numbers or prefixed strings from `start` to `end - 1`. If `prefix` is provided, each number is prefixed.

**Example**:

```javascript
range(5)                // [0, 1, 2, 3, 4]
range(1, 5)             // [1, 2, 3, 4]
range(1, 5, 'item_')    // ["item_1", "item_2", "item_3", "item_4"]
```

### `to_object`

**Syntax**:

```javascript
to_object(entries)
```

**Description**:\
Converts an array of key-value pairs into an object.

**Example**:

```javascript
to_object([['key1', 'value1'], ['key2', 'value2']])
// { "key1": "value1", "key2": "value2" }

[ 'value1', 'value2'] | to_object(zip(range(1, length(@) + 1, 'key'), @))
// { "key1": "value1", "key2": "value2" }
```

### `json_serialize`

**Syntax**:

```javascript
json_serialize(value)
```

_Uses a deterministic version of JSON.stringify to serialize the value._

**Description**:\
Serializes a JSON value to a string.

**Example**:

```javascript
json_serialize({ key: 'value' })
// "{\"key\":\"value\"}"
```

### `json_parse`

**Syntax**:

```javascript
json_parse(string)
```

**Description**:\
Parses a JSON string into a JSON object.

**Example**:

```javascript
json_parse("{\"key\":\"value\"}")
// { "key": "value" }
```

### `sha256`

**Syntax**:

```javascript
sha256(string)
```

**Description**:\
Calculates the SHA-256 hash of a string and returns it as a hexadecimal string.

**Example**:

```javascript
sha256('hello')
// "2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824"
```

### `sha512`

**Syntax**:

```javascript
sha512(string)
```

**Description**:\
Calculates the SHA-512 hash of a string and returns it as a hexadecimal string.

**Example**:

```javascript
sha512('hello')
// "9b71d224bd62f3785d96d46ad3ea3d73319b0c44e59b202205c5d235a0a6caa5a3b36f8c0ab9d45df9215bf07d4d1552c0b1f8bd2671c8a7a3d126f457d79d72"
```

### `uuid`

**Syntax**:

```javascript
uuid(name, namespace?)
```

**Description**:\
Generates a UUID. If `name` and (optionally) `namespace` are provided, generates a version 5 UUID; otherwise, generates a version 4 UUID.

**Example**:

```javascript
uuid('example') // v5 UUID
uuid('example', '6ba7b810-9dad-11d1-80b4-00c04fd430c8') // v5 UUID with namespace
```

`name` must be a string. Use `json_serialize()` to convert a JSON object to a string.

`namespace` must be a UUID string. By default, it uses the NIL UUID.

The [UUID RFC](https://datatracker.ietf.org/doc/html/rfc4122) pre-defines four namespaces

* NameSpace\_DNS: `6ba7b810-9dad-11d1-80b4-00c04fd430c8`
* NameSpace\_URL: `6ba7b811-9dad-11d1-80b4-00c04fd430c8`
* NameSpace\_OID: `6ba7b812-9dad-11d1-80b4-00c04fd430c8`
* NameSpace\_X500: `6ba7b814-9dad-11d1-80b4-00c04fd430c8`

You might use a UUID for a URL to create a custom namespace. For example; LetsFlow uses the following logic to create the UUID for processes.

```javascript
uuid(
  json_serialize(scenario),
  uuid('https://schemas.letsflow.io/v1.0/scenario', '6ba7b811-9dad-11d1-80b4-00c04fd430c8')
) 
```

### `regex_test`

**Syntax**:

```javascript
regex_test(regex, string)
```

**Description**:\
Tests if a string matches a given regular expression.

**Example**:

```javascript
regex_test('/^hello/', 'hello world') // true

regex_test('/^hello/', 'HELLO world') // false
regex_test('/^hello/i', 'HELLO world') // true
```

### `regex_match`

**Syntax**:

```javascript
regex_match(regex, string)
```

**Description**:\
Returns the first match of a regular expression in a string as an array.

**Example**:

```javascript
regex_match('/hello (\\w+)/', 'hello world')
// ["hello world", "world"]

regex_match('/\\w+/g', 'hello world')
// ["hello", "world"]
```

### `regex_match_all`

**Syntax**:

```javascript
regex_match_all(regex, string)
```

**Description**:\
Returns all matches of a regular expression in a string as an array of arrays.

**Example**:

```javascript
regex_match_all('/(\\w+)=(\d+)/g', 'foo=24 bar=99')
// [["foo=24", "foo", "24"], ["bar=99", "bar", "99"]]
```

### `regex_replace`

**Syntax**:

```javascript
regex_replace(regex, replacement, string)
```

**Description**:\
Replaces parts of a string matching a regular expression with a replacement string.

**Example**:

```javascript
regex_replace('/world/', 'universe', 'hello world')
// "hello universe"
```
