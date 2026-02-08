# Action event

When an action is executed, an action event is appended to the event chain. This event includes the key of the executed action, the actor who performed it, and the corresponding response.

## Properties

### `previous`

_string_

The hash of the previous event. This is included in the data of this event to form a hash chain.

### `timestamp`

_date_

The time when the action was executed

### `action`

_string_

The key of the performed action

### `actor`&#x20;

_{ key: string; id?: string }_

The key of the actor and the user ID that performed the action. Additional properties may be added.

### `response`&#x20;

_any_

The response that was given by the actor for this action.&#x20;

### `skipped`&#x20;

_boolean_

If the validation for an event fails or if there is no state transition for the action

### `errors`

array of strings

Validation error messages that caused the event to be skipped. This property is only present in skipped events.

### `hash`&#x20;

_string_

sha256 hash of the JSON serialized event using a [deterministic JSON stringify function](https://www.npmjs.com/package/fast-json-stable-stringify)
