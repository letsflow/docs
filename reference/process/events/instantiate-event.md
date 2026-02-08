# Instantiate event

The first event of the process is an instantiate event.

## Properties

### `id`

_UUID_

The process ID

### `timestamp`

_date_

The time when the process was instantiated

### `scenario`

_UUID_

The scenario ID

### `hash`

_string_

sha256 hash of the JSON serialized event using a [deterministic JSON stringify function](https://www.npmjs.com/package/fast-json-stable-stringify)
