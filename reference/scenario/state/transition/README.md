# Transition

A transition defines the change from one state to the next.

## Action transitions

Typically a state transition is triggered after an action is executed. Action transtitions have an `on` property that specifies the action that triggers the transition.

Optionally they can have a `by` property to specify which actors can trigger the transitions. Multiple transitions can have the same action but with different actors.

Action transitions are evaluated in order. If multiple transitions apply, only the first one is used.

{% tabs %}
{% tab title="YAML" %}
<pre class="language-yaml"><code class="lang-yaml"><strong>initial:
</strong>  transitions:
    - on: join
      goto: ~
    - on: start
      by: organizer
      goto: start
      if: !ref length(actors) >= 2
    - on: cancel
      by: organizer
      goto: (canceled)
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
        "goto": "start",
        "if": { "<ref>": "length(actors) >= 2" }
      },
      {
        "on": "cancel",
        "by": "organizer",
        "goto": "(canceled)"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

## Timeout transitions

Alternatively, state transitions can happen after a specific time. In this case, the transition has `after` property instead of `on`.

If multiple timeout transitions are available, the one with the lowest value for `after` will be used.

{% tabs %}
{% tab title="YAML" %}
<pre class="language-yaml"><code class="lang-yaml"><strong>initial:
</strong>  after: 10 days
  goto: (expired)
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "initial": {
    "after": "10 days",
    "goto": "(expired)"
  }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
If a state transition uses `goto: ~`, the process stays in the same state and the time for a timeout is not reset. In cases `goto` contains the name of the current state, the time is reset.
{% endhint %}

## Properties

### `on`

_string_

Key of the action that must be performed for this transition to be selected.

### `by`

_string or array of string_

The key of an actor or multiple actors as an array. Only select this transition if the action is performed by this actor / one of these actors.

### `after`&#x20;

_time_

A time after which the transition should be executed. If you specify a timeout using `after` do not use the `on` and `by` property.

The time should be in the form of 'amount unit' in English, eg '10 minutes', '12 hours\`, or \`1 week\`.

### `goto`&#x20;

_string_

The key of the state where to transition to.

### `if`

_boolean or_ [_function_](../../data-instruction.md)

A boolean that must be true for the transition to be selected. This is typically a `<ref>` [data instruction](../../data-instruction.md).

### `log`

_log definition_

Defines the title and description of the log entry added during a state transition.

{% content-ref url="log.md" %}
[log.md](log.md)
{% endcontent-ref %}
