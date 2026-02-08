# Notify

Automation in LetsFlow is handled by your backend or microservices. When a state transition occurs, the service needs to be notified. This is achieved through messaging.

If you're using the workflow engine, notifications are sent via a **messaging queue** or **webhooks**. However, if you're integrating the core library directly into your backend, you can leverage the event system provided by your framework to handle these notifications efficiently.

## Implicit notify

For any action available for a service, a notify instruction is automatically appended to that state. The service can be referenced in the `by` property of a state transition or the `actor` property of an action.

If there's exactly one action that the service can perform, the `trigger` property is automatically set to that action. If multiple actions are available the `trigger` property is set to `null`.

{% tabs %}
{% tab title="YAML" %}
```yaml
initial:
  on: create_document
  by: service:cms
  goto: main
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "initial": {
    "on": "create_document",
    "by": "service:cms",
    "goto": "main"
  }
}
```
{% endtab %}
{% endtabs %}

## Properties

### `service`

_string (required)_

The service to message

### `after`

_time_

Delay notifying the service.

The time should be in the form of 'amount unit' in English, eg '10 minutes', '12 hours\`, or \`1 week\`.

### `if`

_boolean or_ [_data function_](../data-instruction.md)

If condition

### `trigger`

_string,_ [_data function_](../data-instruction.md)_, or null_

The action that the service has performed when giving a response.

If the service provides a response and multiple actions are available to the service, you should use a data function to determine which action has been executed. The data function is evaluated after receiving a response and not when the state is instantiated.

{% tabs %}
{% tab title="YAML" %}
```yaml
initial:
  transitions:
    - on: continue
      by: service:agent
      goto: main
    - on: cancel
      by: service:agent
      goto: (cancelled)
  notify:
    service: agent
    trigger: !ref current.response.action
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "initial": {
    "transitions": [
      {
        "on": "continue",
        "by": "service:agent",
        "goto": "main"
      },
      {
        "on": "cancel",
        "by": "service:agent",
        "goto": "(cancelled)"
      }
    ],
    "notify": {
      "service": "agent",
      "trigger": {
        "<ref>": "current.response.action"
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

### `message`

_string, object, or_ [_data function_](../data-instruction.md)

Custom message to send the service.

## Standard message

If `message` is omitted, the standard message is sent. This is;

### `process`&#x20;

### `actions`

_array of instantiated actions_

The actions that the service is allowed to perform in the current state.

### `instructions`

_string_

Instructions for the service as defined in the scenario for this state.

{% tabs %}
{% tab title="YAML" %}
```yaml
wait_on_agent:
  on: respond
  by: service:agent
  goto: wait_on_user
  instructions:
    service:agent: |
      Based on the conversation answer ...
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "wait_on_agent": {
    "on": "respond",
    "by": "service:agent",
    "goto": "wait_on_user",
    "instructions": {
      "service:agent": "Based on the conversation answer ..."
    }
  }
}
```
{% endtab %}
{% endtabs %}

Instructions are mostly useful when the service is an AI agent.
