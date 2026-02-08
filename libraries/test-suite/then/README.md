# Then

`Then` steps are used to describe an _expected_ outcome or result. They perform an assertion.

## State

* [Running state](assert-state.md#running-state) - Assert that the process is in the expected state
* [End state](assert-state.md#end-state) - Assert that the process is in the expected end state
* [State property](assert-state.md#state-property) - Assert that a property of the current state has the expected value

## Actor

* [Actor exists](assert-actor.md#actor-exists) - Assert that the actor is defined in the process
* [Actor property](assert-actor.md#actor-property) - Assert that a property of an actor has the expected value

## Variables

* [Process variables](assert-variable.md#variables) - Assert that a process variable (`vars`) has the expected value
* [Process result](assert-variable.md#result) - Assert that the process result has the expected value
* [Tags](assert-variable.md#tags) - Assert that the process has or doesn't have a specific tag

## Service

* [Notification](assert-service.md#notification) - Assert that a service has (or hasn't) been notified

## Event

* [Skipped event](assert-event.md#skipped-event) - Assert that the previous action has failed
* [Successful event](assert-event.md#successful-event) - Assert that the previous action was applied successfully
* [No skipped events](assert-event.md#no-skipped-events) - Assert that there are no failed actions
* [Timeout event](assert-event.md#timeout-event) - Assert that the previous state change was caused by a timeout
