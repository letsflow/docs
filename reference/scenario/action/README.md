# Action

An action is something that can be performed by an actor. An action can trigger a state transition and/or may update the process variables.

{% tabs %}
{% tab title="YAML" %}
```yaml
issue:
 title: Issue new license
 actor: issuer
 if: !ref vars.license == null
 update:
   set: vars.license
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "issue": {
    "$schema": "https://specs.letsflow.io/v0.2.0/action/schema.json",
    "actor": "issuer",
    "responses": {
      "ok": {
        "title": "Issued new license",
        "update": [
          {
            "select": "assets.license"
          },
          {
            "select": "assets.available",
            "projection": "{shipments: shipments, quantity: quantity}"
          }
        ]
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

### JSON Schema

`https://schemas.letsflow.io/v1.0/action`

## Action

### `$schema`&#x20;

_URI_

The action [JSON schema](http://json-schema.org) URI that describes the JSON structure of the action. This can also be used for automation and may be used by the UI.

### `title`

_string_

A short title for the action.

### `description`

_string or_ [_function_](../data-instruction.md)

A long description of the action that is shown when the action has been performed.

### `actor`

_string,_ [_data function_](../data-instruction.md)_, or array of strings and/or data functions_

Defines the actor allowed to perform this action.

* If a single actor is permitted, specify their key.
* If multiple actors can perform the action, use an array.
* To allow all actors in the schema, use an asterisk (`*`).
* Wildcard actors (keys ending in `*`) are also supported.

To assign the action to a **service**, set the actor to `service:SERVICE_NAME`. Note that the asterisk (`*`) does **not** apply to services.

### `if`

_boolean or_ [_function_](../data-instruction.md)

This field must be `true` for the action to be allowed to be executed. Typically used in combination with a `<ref>` data function.

### `response`

[schema](../../asset.md)

Define the data type of the response data of the action using a JSON schema.

### `stub`

_any_

The response value that is used when [predicting the next states](../../process/prediction.md).

### `update`

_update instruction or array of update instructions_

Update instructions are used to update the process variables after executing an action.

{% content-ref url="update-instruction.md" %}
[update-instruction.md](update-instruction.md)
{% endcontent-ref %}

