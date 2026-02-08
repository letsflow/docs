# Assert event

## Skipped event

Assert that an event has been skipped. In other words, ensure that the previous action is not allowed.

```gherkin
Then the last event is skipped
Then the last event of the "main" process is skipped
```

### Error message

Use `with` to specify the expected error message.

```gherkin
Then the last event is skipped with "Response is invalid: data must have required property 'email'"
Then the last event is skipped with:
  """
  Response is invalid: data must have required property 'email'
  """
```

The last event may have multiple error messages. It needs to include the expected error message.&#x20;

## Successful event

Assert that the last event has not been skipped; the last action was applied successfully.

```gherkin
Then the last event is not skipped
Then the last event of the "main" process is not skipped
```

## No skipped events

Assert that the event chain doesn't contain any skipped events; all the actions have been applied successfully.

```gherkin
Then nothing is skipped
Then nothing is skipped from the "main" process
```

## Timeout event

Assert that the previous state change was caused by a timeout.

```gherkin
When 24 hours pass
Then the last event is a timeout
Then the last event in the "main" process is a timeout
```
