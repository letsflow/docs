# Prediction

LetsFlow can optionally predict the next states of the process by setting the `?prediction=true` query parameter when using the engine API or by calling `predict()` when using the core library.

During a prediction the predicted states are instantiated, similar to the [current state](current-state.md). This means that [data functions](../scenario/data-instruction.md) in the scenario state are evaluated.

## Action transitions

For the prediction, the action transitions of the state are tried in order. If the first transition is unavailable or not allowed, the second is tried, etc. If multiple actors are allowed to execute an action, the transition is tried for all actors, before moving to the next transition.

### Response

If the action has a response schema, the default value is determined from the schema and used during prediction. If no schema is defined the response will be undefined. You can set the [`stub` property](../scenario/action/#stub) of an action to specify a value used for the prediction instead of the default value.

## Timeout transitions

If a state doesn't have action transitions or none of the action transitions are allowed, the prediction will try the timeout transitions. In contrary to action transitions, timeout transitions are evaluated in order based on the `after` value.

## `is_prediction` flag

During a prediction, the `is_prediction` flag is added to the process. This flag can be used in `if` conditions for transitions and actions. It's useful to skip or break out of infinite loops, or to force a transition that would not be allowed.

{% tabs %}
{% tab title="YAML" %}
<pre class="language-yaml"><code class="lang-yaml"><strong>initial:
</strong><strong>  on: start
</strong><strong>  goto: main
</strong><strong>  if: !ref length(actors) >= 2 || is_prediction
</strong><strong>main:
</strong>  transitions:
    - on: next
      goto: sign
      if: !ref is_prediction
    - on: next
      goto: prepare
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
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

## Properties

The predicted states have the following properties

### `key`

_string_

The key of the current state as defined in the scenario.

### `title`

_string_

A short title for the state.

### `description`

_string_

A long description of the state.

### `instructions`

_map of actor ⇒ string_

Instructions that can be specific per actor. This is an object where the keys correspond with the actor keys.

{% tabs %}
{% tab title="YAML" %}
```yaml
instructions:
  employee: Fill out this form
  organization: Waiting for the employee to fill out the form
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "instructions": {
    "employee": "Fill out this form",
    "organization": "Waiting for the employee to fill out the form"
  }
}
```
{% endtab %}
{% endtabs %}

## Additional properties

Additional properties that are defined in the scenario state are added to the predicted state when it's instantiated.
