# Data function

Data functions allow you to dynamically set properties of a state or actions when they are instantiated using data from the projected process.

Functions are applied before a state transition to the executed action and the possible state transitions.

After a state transition, functions are applied to create the [current state](../process/current-state.md) of the process. This includes the list of actions that can be executed in the state.

## `<ref>`

Apply a JMESPath query to the process. You can retrieve and transform any part of the process, including the scenario and events.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  team:
    role: team
  client:
    properties:
      name: string
      email: !format email

actions:
  update_client:
    response: !ref scenario.actors.client
    update:
      set: actors.client
      value: !ref current.response | { name: name, email: email }

states:
  initial:
    transitions:
      - on: update_client
        by: team
        goto: ~
      - on: approve
        by: client
        if: !ref actors.client.email
        goto: (approved)
      - on: reject
        by: client
        goto: (rejected)
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "actors": {
    "team": {
      "role": "team"
    },
    "client": {
      "properties": {
        "name": "string",
        "email": {
          "type": "string",
          "format": "email"
        }
      }
    }
  },
  "actions": {
    "update_client": {
      "response": {
        "<ref>": "scenario.actors.client"
      },
      "update": {
        "set": "actors.client",
        "value": {
          "<ref>": "current.response | { name: name, email: email }"
        }
      }
    }
  },
  "states": {
    "initial": {
      "transitions": [
        {
          "on": "update_client",
          "by": "team",
          "goto": null
        },
        {
          "on": "approve",
          "by": "client",
          "if": {
            "<ref>": "actors.client.email"
          },
          "goto": "(approved)"
        },
        {
          "on": "reject",
          "by": "client",
          "goto": "(rejected)"
        }
      ]
    }
  }
}

```
{% endtab %}
{% endtabs %}

It's important to know and understand how to use JMESPath expressions to unlock the full potential of LetsFlow. You can find more examples in the JMESPath documentation.

{% content-ref url="../../libraries/jmespath.md" %}
[jmespath.md](../../libraries/jmespath.md)
{% endcontent-ref %}

## `<tpl>`&#x20;

Parse text as a [Mustache](https://mustache.github.io/) template.

When a function receives a simple string, it is treated as a Mustache template, with the process itself serving as the data (also known as the Mustache view).

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  admin:
    role: admin
  client_*:
    properties:
      name: string
      approved: boolean

actions:
  send_sms:
    $schema: https://schemas.example.com/sms
    message: !tpl Hello {{ current.actor.name }}

states:
  main:
    instructions:
      admin: !tpl
        template: "{{#clients}}{{name}}: {{approved}}{{/}}"
        view: !ref "{ clients: actors.* | @[?role==null] }"
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "actors": {
    "admin": {
      "role": "admin"
    },
    "client_*": {
      "properties": {
        "name": "string",
        "approved": "boolean"
      }
    }
  },
  "actions": {
    "send_sms": {
      "$schema": "https://schemas.example.com/sms",
      "message": {
        "<tpl>": "Hello {{ current.actor.name }}"
      }
    }
  },
  "states": {
    "instructions": {
      "admin": {
        "<tpl>": {
          "template": "{{#clients}}{{name}}: {{approved}}{{/}}",
          "view": {
            "<ref>": "{ clients: actors.* | @[?role==null] }"
          }
        }
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

You can explicitly specify both the template and the view for more flexibility. This allows you to apply a JSON query using `<ref>` to filter or transform the data before rendering the template.

```yaml
!tpl
  template: string
  view: object
  partials: map of key => template
```

## `<select>`

The select data function picks one of the options based on value.

The selection value is specified as `$`. The default value is specified with the `*` key. The other properties is used as map of option ⇒ value.

{% tabs %}
{% tab title="YAML" %}
```yaml
states:
  (cancelled):
    description: !select
      $: !ref previous[-1].actor.key
      client: Cancelled by the client
      team: !tpl Could not be completed because {{reason}}
      '*': Aborted
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "states": {
    "cancelled": {
      "description": {
        "<select>": {
          "$": {
            "<ref>": "previous[-1].actor.key"
          },
          "client": "Cancelled by the client",
          "team": {
            "<tpl>": "Could not be completed because {{reason}}"
          },
          "*": "Aborted"
        }
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

The selection value must be a string, number, or boolean. For a boolean value use the keys `true` and `false`.

If no default value is specified, the selected value will be `null` if there's no match.
