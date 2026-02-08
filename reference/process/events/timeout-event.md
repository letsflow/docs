# Timeout event

A timeout occurs when a timed state transition takes place. The time is set using the `after` property of the transition.

The event is never skipped. Process variables can't be modified on a timeout, so no validation occurs. No timeout event is added to the event chain if there's no state transition.

Which timeout occurred can be determined by subtracting the timestamp of the previous event from the timeout of this event.

## Properties

### `previous`

_string_

The hash of the previous event. This is included in the data of this event to form a hash chain.

### `timestamp`

_date_

The time when the transition took place

### `hash`&#x20;

_string_

sha256 hash of the JSON serialized event using a [deterministic JSON stringify function](https://www.npmjs.com/package/fast-json-stable-stringify)
