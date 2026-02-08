# Update instruction

Update instructions are used to update the process information after executing an action. The process title, tags actors, variables, and the result can be modified at runtime.

You may set `current.actor` to target the actor that performed the action.

{% tabs %}
{% tab title="YAML" %}
```yaml
actions:
  set_feeling:
    response: string
    update: current.actor.feeling
  generate_name_tag:
    response:
      id: string
      body: string
    update:
      set: vars.document
      mode: replace
      if: !ref current.actor.needs_name_tag
      value:
        url: !tpl https://docs.example.com/name-tags/{{ current.response.id }}
        content: !ref current.response.body
        media_type: text/html
      stub:
        url: https://docs/example.com/name-tags/dummy
        content: Dummy content
        media_type: text/html
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "actions": {
    "set_feeling": {
      "response": "string",
      "update": "current.actor.feeling"
    },
    "generate_name_tag": {
      "response": {
        "id": "string",
        "body": "string"
      },
      "update": {
        "set": "vars.document",
        "mode": "replace",
        "if": {
          "<ref>": "current.actor.needs_name_tag"
        },
        "value": {
          "url": {
            "<tpl>": "https://docs.example.com/name-tags/{{ current.response.id }}"
          },
          "content": {
            "<ref>": "current.response.body"
          },
          "media_type": "text/html"
        },
        "stub": {
          "url": "https://docs/example.com/name-tags/dummy",
          "content": "Dummy content",
          "media_type": "text/html"
        }
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

## Simplified

A simple update instruction can be set with a single string. This will be normalized to be the `set` property. The `value` will default the `current.response`.

## Properties

### `set`

_string (required)_

A reference to the target item in the process that should be updated.

Note that reference must be a simple dot notation for object and array access (eg `assets.stock.items[3]`). It can't be a full JMESPath expression.

### `value`&#x20;

_any or_ [_function_](../data-instruction.md)

The value to which the target should be set. By default, this is the value of the response (`current.response`).

The value is typically set using a [data function](../data-instruction.md).

### `stub`&#x20;

_any or_ [_function_](../data-instruction.md)

During prediction, the default response, determined by the action response schema, is used for the update instructions. Alternatively, you may set a `stub` to force a value to be used when predicting the next states.

### `mode`

_'replace' (default), 'merge', or 'append'_

* replace - Replace the target with the value
* merge - If both the target and value are objects or if both are arrays, merge them. If the types do not match, default back to `replace`.
* append - Append the value to the target. The target is expected to be an array. If it's not, it's first set to an empty array before appending the value.

### if

_boolean or_ [_function_](../data-instruction.md)

This field must be `true` for the action to be allowed to be executed. Typically used in combination with a `<ref>` data function.
