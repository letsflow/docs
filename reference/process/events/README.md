# Events

LetsFlow uses an event-sourcing model. Every action is recorded as an event, regardless of whether it triggers a state transition. The current state of a process can always be reconstructed by replaying the sequence of events on the scenario.

Each event is hashed and the hash of the previous event is added to the event data, creating a hash chain.

## Instantiate event

The first event of the process is an instantiate event.&#x20;

{% content-ref url="instantiate-event.md" %}
[instantiate-event.md](instantiate-event.md)
{% endcontent-ref %}

## Action event

Whenever an action is executed, an action event is added to the event chain. The event contains the key of the executed action, the actor that executed the action, and the given response.

{% content-ref url="action-event.md" %}
[action-event.md](action-event.md)
{% endcontent-ref %}

## Timeout event

When a timeout transition is triggered, a timeout event is added to the event chain.

{% content-ref url="timeout-event.md" %}
[timeout-event.md](timeout-event.md)
{% endcontent-ref %}
