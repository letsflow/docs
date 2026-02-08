---
description: An actor is a person, team, or system that participates in a process.
---

# Actor

Items in the `actors` property of the **scenario** aren't actor objects, but [JSON schemas](../asset.md) defining the properties available for the actor once instantiated in the process.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  organization:
    title: Employer
    role: manager
    properties:
      name: string
      email: !format email
  employee:
    title: Employee
    $ref: "https://schemas.example.com/actors/employee.json"
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "actors": {
    "organization": {
      "title": "Employer",
      "role": "manager",
      "properties": {
        "name": "string",
        "email": {
          "type": "string",
          "format": "email"
        }
      }
    },
    "employee": {
      "title": "Employee",
      "$ref": "https://schemas.example.com/actors/employee"
    }
  }
}
```
{% endtab %}
{% endtabs %}

The actor schema always defines an object type. This is implicit. Actors can't be a composite type (using `oneOf`, `allOf`, or `anyOf`).

## Schema

`https://schemas.letsflow.io/v1.0.0/actor`

{% hint style="info" %}
The _**actor**_ schema extends a [JSON Schema](https://json-schema.org). Other JSON Schema properties are also allowed.
{% endhint %}

## Properties

### `title`

_string_

The title of the actor. If omitted, this is created from the actor key.

### `role`

_string or array of strings_

Use for access control. **Any user** that has this role can perform as this actor. If multiple roles are specified, the user must have at least one of these roles.

```yaml
organization:
 role: manager
```

If the role of an actor is defined in the scenario, it can't be changed with start instructions or during the process.

### `properties`

_map of schemas_

By default, a process actor only has an id, title, and role. Other properties must be specified in the actor schema.

```yaml
participant:
  properties:
    name: string
    email: !format email
    age:
      type: number
      min: 16
    active: !default true
```

{% content-ref url="../asset.md" %}
[asset.md](../asset.md)
{% endcontent-ref %}

### `additionalProperties`

_boolean or schema_

By default, it's not allowed to set properties of an actor that aren't defined in the schema. In other words; the JSONSchema `additionalProperties` value default to `false`.

You may set this property to `true` to allow additional properties to be set for the process actor. Alternatively, you can specify a [schema](../asset.md) to which all additional properties need to comply.

## External schema

Instead of defining the schema within the scenario, you can reference an external schema for reusability.

The `title` and `role` property of external schema is ignored. They should be specified in the scenario.

```yaml
client:
  title: Support desk
  role: support
  $ref: "https://schemas.example.com/actors/support.json"
```

Read more about using custom schemas in the validation chapter.&#x20;

## Wildcard actors

Wildcard actors allow for defining a flexible number of participants in a process without explicitly naming or enumerating them. This is achieved by using a wildcard pattern in the actor key, such as `person_*`.  For example:

```yaml
actors:
  person_*:
    title: Participant
    properties:
      name: string
      email:
        type: string
        format: email
```

In this example, each actor with a key like `person_1`, `person_2`, and so on will share the same schema.

### Creation at runtime

At instantiation, the process will not have any actors that are defined as a wildcard. Instead, new actors can be added at runtime using **update instructions**.

```yaml
actions:
  invite:
    response:
      type: array
      items:
        properties:
          id: !format uuid
          name: string
          email: !format email
     update:
       set: actors
       mode: merge
       value: !ref current.response | to_object(zip(range(1, length(@) + 1, 'person_'), @))
```
