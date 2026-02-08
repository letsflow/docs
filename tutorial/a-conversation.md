---
description: A circular process and actions with responses
---

# A conversation

In the previous scenario, we defined a handshake process where the _recipient_ can ignore or reply to the greeting.

In this tutorial, we'll move from a handshake to a conversation where Bob (_the initiator_) greets Eve (_the recipient_). We'll give Jane some different ways to respond.

{% hint style="info" %}
Until now, we've created the scenario first and added a test later. It's better to take a [TDD](https://en.wikipedia.org/wiki/Test-driven_development)-style approach, starting with a test and then writing or modifying the scenario.

Additionally, with more complex workflows, we're creating multiple test files per scenario.
{% endhint %}

## Keeping the conversation going

We'll imagine that the conversation starts with Bob asking Jane "How are you?". Eve might answer with "Fine" and Bob ends the interaction.

Alternatively, Eve could answer with "Could be better" and strike up a conversation. Bob could answer with "What's the matter?" continuing the conversation.

In the scenario. we abstract this as 2 distinct actions "_talk_" and "_end_". An actor can't end the conversation right after he talks. The conversation continues until one of the actors ends.

### Test case

Let's create a test with 3 scenarios. The first case is the shortest conversation possible. Next, we have a longer conversation that is ended by Joe. Last, we'll test a conversation ended by Jane.

{% hint style="success" %}
Create directory `conversation` in `features` and add file `steps.feature`.
{% endhint %}

<pre class="language-gherkin" data-title="conversation/steps.feature"><code class="lang-gherkin"><strong>Feature: Two actors meet and have a conversation
</strong>
  Background:
    Given the process is created from the "conversation" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor
    
  Scenario: The recipient immediately ends the conversation
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"
    When "Eve" does "end"
    Then the process ended

  Scenario: The conversation is ended by the initiator
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"  
    When "Eve" does "talk"
    Then the process is in "wait_on_initiator"
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"
    When "Eve" does "talk"
    Then the process is in "wait_on_initiator"
    When "Bob" does "end"
    Then the process ended

  Scenario: The conversation is ended by the recipient
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"  
    When "Eve" does "talk"
    Then the process is in "wait_on_initiator"
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"
    When "Eve" does "end"
    Then the process ended
</code></pre>

### Scenario

{% hint style="success" %}
Add file `conversatoin.yaml` to the `scenarios` directory.
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
    on: talk
    by: initiator
    goto: wait_on_recipient
  wait_on_recipient:
    transitions:
      - on: talk
        by: recipient
        goto: wait_on_initiator
      - on: end
        by: recipient
        goto: (done)
  wait_on_initiator:
    transitions:
      - on: talk
        by: initiator
        goto: wait_on_recipient
      - on: end
        by: initiator
        goto: (done)
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
      "on": "talk",
      "by": "initiator",
      "goto": "wait_on_recipient"
    },
    "wait_on_recipient": {
      "transitions": [
        {
          "on": "talk",
          "by": "recipient",
          "goto": "wait_on_initiator"
        },
        {
          "on": "end",
          "by": "recipient",
          "goto": "(done)"
        }
      ]
    },
    "wait_on_initiator": {
      "transitions": [
        {
          "on": "talk",
          "by": "initiator",
          "goto": "wait_on_recipient"
        },
        {
          "on": "end",
          "by": "initiator",
          "goto": "(done)"
        }
      ]
    }
  }
}
```
{% endtab %}
{% endtabs %}

## Wait your turn!

The actors should not be able to act out of turn. We'll create a test case to ensure this is not allowed. If an actor tries to do something that's not allowed, the action will be skipped and the process will stay in the current state.

### Test case

{% hint style="success" %}
&#x20; Add `out-of-turn.features` to the `features/conversation` directory.
{% endhint %}

<pre class="language-gherkin" data-title="conversation/out-of-turn.feature"><code class="lang-gherkin"><strong>Feature: Actors try to act out of turn
</strong>
  Background:
    Given the process is created from the "conversation" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor
    
  Scenario: The initiator tries to talk twice
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"
    And the last event is skipped

  Scenario: The recipient tries to end the conversation after talking
    When "Bob" does "talk"
    Then the process is in "wait_on_recipient"  
    When "Eve" does "talk"
    Then the process is in "wait_on_initiator"
    When "Eve" does "end"
    Then the process is in "wait_on_initiator"
    And the last event is skipped
</code></pre>

## A conversation log

An action can have a response. In this scenario, the things that are said are the response to each action. We want to take each response to build up a conversation log.

### Test case

We've tested the state transitions in the `steps.feature`  test case. In this feature, we'll assume that this works correctly and focus on validating the process result.

{% hint style="success" %}
Add file `response.feature` to the `features/conversation` directory.
{% endhint %}

<pre class="language-gherkin" data-title="conversation/result.feature"><code class="lang-gherkin"><strong>Feature: Two actors have a conversation, which is logged.
</strong>
  Background:
    Given the process is created from the "conversation" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor

  Scenario:
    When "Bob" does "talk" with "How are you?"
    And "Eve" does "talk" with "Could be better"
    And "Bob" does "talk" with "What's the matter?"
    And "Eve" does "talk" with "My cat is stealing my boyfriend"
    And "Bob" does "talk" with "Why do think that?"
    And "Eve" does "talk" with "He cuddles her more than me"
    And "Bob" does "talk" with "Sorry to hear that"
    And "Eve" does "end"
    Then the process ended with:
      """
      - How are you?
      - Could be better
      - What's the matter?
      - My cat is stealing my boyfriend
      - Why do think that?
      - He cuddles her more than me
      - Sorry to hear that
      """
</code></pre>

### Scenario

We need to explicitly define the actions to use the response. For each action, we'll use an update instruction to append the response to the process result.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
  recipient:
    title: Recipient

actions:
  talk:
    update:
      set: result
      mode: append

states:
  initial:
    on: talk
    by: initiator
    goto: wait_on_recipient
  wait_on_recipient:
    transitions:
      - on: talk
        by: recipient
        goto: wait_on_initiator
      - on: end
        by: recipient
        goto: (done)
  wait_on_initiator:
    transitions:
      - on: talk
        by: initiator
        goto: wait_on_recipient
      - on: end
        by: initiator
        goto: (done)
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
  "actions": {
    "talk": {
      "update": {
        "set": "result",
        "mode": "append"
      }
    }
  },
  "states": {
    "initial": {
      "on": "talk",
      "by": "initiator",
      "goto": "wait_on_recipient"
    },
    "wait_on_recipient": {
      "transitions": [
        {
          "on": "talk",
          "by": "recipient",
          "goto": "wait_on_initiator"
        },
        {
          "on": "end",
          "by": "recipient",
          "goto": "(done)"
        }
      ]
    },
    "wait_on_initiator": {
      "transitions": [
        {
          "on": "talk",
          "by": "initiator",
          "goto": "wait_on_recipient"
        },
        {
          "on": "end",
          "by": "initiator",
          "goto": "(done)"
        }
      ]
    }
  }
}
```
{% endtab %}
{% endtabs %}
