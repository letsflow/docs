# Current state

The current state is the instantiated version of the state that the process is in.

The state is instantated, meaning that all [data functions](../scenario/data-instruction.md) in the scenario state are evaluated during a state transition.

## Properties

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

### `actions`

_list of actions_

An array with all instantiated actions that may be performed in this state. The actions are determined by the `on` property of the state transitions.

Data functions are evaluated when instantiating an action.

Actions that are not allowed to be performed due to the `if` condition of the action or the state transition are omitted from this list.

The list of actors in the action is filtered based on the `by` properties of the transition(s). If no actors can perform the action, the action is omitted from the list.

Instantiated actions in the current state do not include the update instructions.

### `notify`

_array of_ [_notify instructions_](../scenario/state/notify.md)

List of services that have been (or should be) notified when transitioned into this state.

## Runtime properties

The `response`and `actor` properties of the current state are only available during a state transition. They can be accessed through data functions.

### `response`

_any_

The response that was given for the action that was executed for this state. The response type can be defined as a schema using [the `response` property](../scenario/action/#response) of the scenario action.

### `actor`

_actor_

A copy of the instantiated actor of the process that has executed the action.

If it's modified using update instructions, the modifications will persist to the process actor.

## Additional properties

Additional properties that are defined in the scenario state are added to the current state when it's instantiated.
