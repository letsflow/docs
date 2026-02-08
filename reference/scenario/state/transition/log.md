# Log

LetsFlow will log state transitions appending log entries to the `previous` property of the process. By default, action transitions are logged and timeout transitions are not.

A log entry contains a title, description, timestamp, and actor. The title and description are determined by the `log` property of the state transition in the scenario.

Data functions in the `log` property are evaluated after applying the update instructions.

## Properties

### `title`

_string or_ [_data function_](../../data-instruction.md)

Defaults to the title of the current action.

### description

_string or_ [_data function_](../../data-instruction.md)

Defaults to the description of the current action.

### if

_boolean or_ [_data function_](../../data-instruction.md)

Whether a log entry is appended to the `previous` array for this state transition. Defaults to `true`.

## Additional properties

You may add custom properties to the log definition. These can be any value or [data function](../../data-instruction.md). These properties will be included in the log entry.
