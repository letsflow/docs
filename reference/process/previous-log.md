# Previous log

LetsFlow will log state transitions appending log entries to the `previous` property of the process. By default, action transitions are logged and timeout transitions are not.

## Properties

### `title`

_string_

The title of the state transition. By default, this is the title of the current action.

### `description`

_string_

A description of the state transition. By default, this is the description of the current action.

### `timestamp`

_date_

The time when the transition took place.

### `actor`&#x20;

_{ key: string; id?: string }_

The key of the actor and the user ID that performed the action. Additional properties may be added.

## Update instructions

The `previous` property can be modified using update instructions. This is useful for collapsing log entries.
