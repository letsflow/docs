---
description: The state a process that's instantiated from this scenario can be in.
---

# State

The scenario defines a finite state machine. Each state describes through which action it can transition to a new state.

## Simple state

A simple state has a single transition specified by the `on` or `after` and the `goto` property.

Optionally you can limit who can perform the action in this state using `by`. Use an array for `by` if multiple actors are allowed to do the action.

{% tabs %}
{% tab title="YAML" %}
```yaml
initial:
  on: complete
  by: client
  goto: (done)
wait:
  after: 24 hours
  goto: main
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "initial": {
    "on": "complete",
    "by": "admin",
    "goto": "(done)"
  },
  "wait": {
    "after": "24 hours",
    "goto": "main"
  }
} 
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The `on`, `after`, `by`, `goto`, and `log` properties that can be added to simple states are described in the documentation of [transitions](transition/).
{% endhint %}

## State with multiple transitions

A state can define multiple transitions. The transition depends on the action being performed in that state or by the actor who performed it.

{% tabs %}
{% tab title="YAML" %}
<pre class="language-yaml"><code class="lang-yaml"><strong>initial:
</strong><strong>  title: Waiting in the lobby
</strong><strong>  description: Participants can join until the organizer starts
</strong>  transitions:
    - on: join
      by: participant
      goto: ~
    - on: join
      by: organizer
      goto: start
    - on: cancel
      by: organizer
      goto: (canceled)
    - after: 10 days
      goto: (expired)
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "initial": {
    "transitions": [
      {
        "on": "join",
        "by": "participant",
        "goto": null
      },
      {
        "on": "join",
        "by": "organizer",
        "goto": "start"
      },
      {
        "on": "cancel",
        "by": "organizer",
        "goto": "(canceled)"
      },
      {
        "after": "10 days",
        "goto": "(expired)"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

## End states

End states are states without state transitions. Unlike normal states, they can be defined implicitly by referencing them in a `goto`.

## Properties

### `title`

_string_

A short title for the state.

### `description`

_string or_ [_data function_](../data-instruction.md)

A detailed description of the action, displayed when the state is either the current state or part of the prediction.

### `transitions`

_array of transitions_

Transitions from this state to the next.

{% content-ref url="transition/" %}
[transition](transition/)
{% endcontent-ref %}

### `notify`&#x20;

_string, notify instructions. or array of strings and notify instructions_

Specify external services that should be notified when transitioning into this state.

If `notify` is a string, it's interpreted as a service name with all other properties of the notify instructions omitted.

{% content-ref url="notify.md" %}
[notify.md](notify.md)
{% endcontent-ref %}
