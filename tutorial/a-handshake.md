---
description: Run a process with multiple actors and actions.
---

# A handshake

In the previous tutorial, we created and tested our first scenario. In that scenario, we didn't define any actors. In this tutorial, we explicitly define multiple actors to participate in the process.

## Defining actors

Each actor plays a specific role in the scenario. We'll tie the actor to a particular user (or team) when creating the process. We define two actors for the handshake 'initiator' and 'recipient' and give each actor a title.

{% hint style="success" %}
Create file `handshake.yaml` (or `handshake.json`) in the `scenarios` directory.
{% endhint %}

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
  recipient:
    title: Recipient

states:
  initial:
    on: complete
    goto: (success)
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "actors": {
        "initiator": {
            "title": "Initiator"
        },
        "recipient": {
            "title": "Recipient"
        }
    },
    "states": {
        "initial": {
            "on": "complete",
            "goto": "(success)"
        }
    }
}
```
{% endtab %}
{% endtabs %}

## Greeting each other

We want the two actors to interact in the form of a simple greeting:

> Initiator: Hi, how are you?\
> Recipient: Fine. How about you?\
> Initiator: Fine

The _initiator_ will still _complete_ the process, but from the _initial_ state, it will first do a _greeting_, expecting a _reply_. We'll add these 2 states where these can be performed. First, the _initiator_ waits on the _recipient_ and then visa versa.

We use the `by` property to specify which actor can perform the action in that state.

### Scenario

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
  recipient:
    title: Recipient

states:
  initial:
    on: greet
    by: initiator
    goto: wait_on_recipient
  wait_on_recipient:
    on: reply
    by: recipient
    goto: wait_on_initiator
  wait_on_initiator:
    on: complete
    goto: (success)
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "actors": {
    "initiator": {
      "title": "Initiator"
    },
    "recipient": {
      "title": "Recipient"
    }
  },
  "states": {
    "initial": {
      "on": "greet",
      "by": "initiator",
      "goto": "wait_on_recipient"
    },
    "wait_on_recipient": {
      "on": "reply",
      "by": "recipient",
      "goto": "wait_on_initiator"
    },
    "wait_on_initiator": {
      "on": "complete",
      "goto": "(success)"
    }
  }
}
```
{% endtab %}
{% endtabs %}

We see that we transition from the "_initial"_ state to "_wait\_on\_recipient"_, then wait _on "initiator"_ and finally to successful completion of the process.

### Test case

{% hint style="success" %}
Create file `handshake.feature` in the `features` directory.
{% endhint %}

{% code title="handshake.feature" %}
```gherkin
Feature: Two actors greet each other

  Background:
    Given the process is created from the "handshake" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor

  Scenario:
    When "Bob" does "greet"
    Then the process is in "wait_on_recipient"
    When "Eve" does "reply"
    Then the process is in "wait_on_initiator"
    When "Bob" does "complete"
    Then the process ended
```
{% endcode %}

## Choose to ignore

Let's give the recipient a choice to either reply or ignore the greeting. If he chooses to ignore the greeting the process will have failed.

### Scenario

For the "_wait\_on\_recipient_" state we define two transitions. Both can only be performed by the "_recipient_" actor.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
  recipient:
    title: Recipient

states:
  initial:
    on: greet
    by: initiator
    goto: wait_on_recipient
  wait_on_recipient:
    transitions:
      - on: reply
        by: recipient
        goto: wait_on_initiator
      - on: ignore
        by: recipient
        goto: (failed)
  wait_on_initiator:
    on: complete
    by: initiator
    goto: (success)
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "actors": {
        "initiator": {
            "title": "Initiator"
        },
        "recipient": {
            "title": "Recipient"
        }
    },
    "states": {
        "initial": {
            "on": "greet",
            "by": "initiator",
            "goto": "wait_on_recipient"
        },
        "wait_on_recipient": {
            "transitions": [
                {
                    "on": "reply",
                    "by": "recipient",
                    "goto": "wait_on_initiator"
                },
                {
                    "on": "ignore",
                    "by": "recipient",
                    "goto": "(cancelled)"
                }
            ]
        },
        "wait_on_initiator": {
            "on": "complete",
            "by": "recipient",
            "goto": "(success)"
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Test case

We can add a new `Scenario` section in our test file to test the path in the process where the _recipient_ "Jane" ignores the greeting.

{% code title="handshake.feature" %}
```gherkin
Feature: Two actors greet each other

  Background:
    Given the process is created from the "handshake" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor

  Scenario:
    When "Bob" does "greet"
    Then the process is in "wait_on_recipient"
    When "Eve" does "reply"
    Then the process is in "wait_on_initiator"
    When "Bob" does "complete"
    Then the process ended in "success"

  Scenario:
    When "Bob" does "greet"
    Then the process is in "wait_on_recipient"
    When "Eve" does "ignore"
    Then the process ended in "failed"
```
{% endcode %}

You can have multiple `Scenario` sections. The `Background` is run before each `Scenario`.
