---
description: Simplified version of JSON Schema
---

# Schema

In a scenario, the variables, process result, and actors are defined using JSON Schema. JSON Schema is a standard for defining the structure, format, and validation rules of JSON data.

To simplify usage, LetsFlow supports both standard and **simplified schemas**.

{% tabs %}
{% tab title="YAML" %}
```yaml
vars:
  message: string
  contract:
    properties:
      id: !format uri
      name: !required string
      date: !format date-time
      signed: !default false
  books:
    items:
      properties:
        id: !format uuid
        title: string
        isbn: !pattern: "^\\d{13}$"
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "vars": {
    "message": "string",
    "contract": {
      "properties": {
        "id": {
          "type": "string",
          "format": "uri"
        },
        "name": {
          "type": "string",
          "required": true
        },
        "date": {
          "type": "string",
          "format": "date-time"
        },
        "signed": {
          "type": "boolean",
          "default": false
        }
      }
    },
    "books": {
      "items": {
        "properties": {
          "id": {
            "type": "string",
            "format": "uuid"
          },
          "title": "string",
          "isbn": {
            "type": "string",
            "pattern": "^\\d{13}$"
          }
        }
      }
    }
  }
}

```
{% endtab %}
{% endtabs %}

For more information about JSON Schema, please visit:

{% embed url="https://json-schema.org/" %}

## Schema

`https://schemas.letsflow.io/v1.0/schema`

## Normalization

Simplified schemas are converted into their full JSON Schema equivalent before use. This is done when a scenario or a standalone schema is normalized.

Simplified schemas supersede full JSON Schema, thus any valid JSON Schema is also a valid LetsFlow schema.

## Simplification features

### Simple types

Basic types like `string`, `number`, or `boolean` can be specified directly without wrapping in a `type` field.

```yaml
name: string
age: integer
```

normalizes to

```yaml
name:
  type: string
age:
  type: integer
```

### **Simple References**

In JSON Schema, you can reference an external schema or subsection of the current schema using `$ref`.&#x20;

In LetsFlow's simplified schema, references can be written directly as strings. Any string starting with `http://`, `https://`, or `#` is seen as a reference, rather than a type.

```yaml
client: https://schema.example.com/actors/client
```

normalizes to

```yaml
client:
  $ref: https://schema.example.com/actors/client
```

### Yaml tags

YAML tags in LetsFlow provide a concise way to define additional metadata or constraints in your schema.

These tags are not available when defining a schema in JSON.

#### **`!const`**

Defines a constant value:

```yaml
status: !const "active"
```

#### **`!enum`**

Defines an enumerated set of values:

```yaml
role: !enum ["admin", "user", "guest"]
```

The type is determined based on the values of the enum.

#### **`!format`**

Specifies a data format:

```yaml
email: !format email
```

The type is always set to `string`.

#### **`!pattern`**&#x20;

Defines a regular expression for validation:

```yaml
phone: !pattern "^\\+?[0-9]{10,15}$"
```

The type is always set to `string`.

#### **`!default`**&#x20;

Provides a default value:

```yaml
enabled: !default true
```

The type is determined based on the default value.

**`!required`**&#x20;

Marks a property as required:

```yaml
properties:
  name: !required string
  age: !required
    type: integer
    minimum: 16
    maximum: 120
```

The `!required` tag should only be used on properties, as the property is automatically appended to the `required` field of the parent object.

### Implicit Types

If `type` is omitted, it may be automatically determined based on the structure:

* The presence of `properties` implies an object.
* The presence of `items` implies an array.
* For `const` or `enum` the type is determined based on the const or enum value.

## Additional properties

Properties that aren't defined don't exist and can't be set unless `additionalProperties` is set in the definition.

In other words; `additionalProperties` defaults to `false` in contrary to the default behavior of JSON Schema.

## Immutability

{% hint style="warning" %}
**For schema files;** LetsFlow assumes that schemas are **immutable** to be deterministic. It's highly recommended that the URI of the schema ID includes a version number. Modifying a published schema can lead to unexpected behavior.
{% endhint %}
