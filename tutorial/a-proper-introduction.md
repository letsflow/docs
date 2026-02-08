---
description: Introducing schemas for actors and the result
---

# A proper introduction

In the previous tutorial, two actors had a small conversation. In this tutorial, the actors will introduce themselves before starting the conversation.

{% hint style="success" %}
* Create file `introduction.yaml` in `scenarios`.
* Create subdirectory `introduction` in `features` for the test files.
{% endhint %}

## Introduce yourself

Bob, the initiator, will introduce himself stating his name and organization. Eve will respond by introducing herself also stating her name and organization. We use the response to update the information about the current actor.

### Test case

{% code title="introduction/simple.feature" %}
```gherkin
Feature: Two actors meet at a conference and exchange information

  Background:
    Given the process is created from the "introduction" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor

  Scenario:
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    Then the process is in "wait_on_introduction_recipient"
    And actor "initiator" has "name" is "Bob"
    And actor "initiator" has "organization" is "Jasny"
    
    When "Eve" does "introduce" with:
      | name         | Eve      |
      | organization | Acme Inc |
    Then the process is in "wait_on_initiator"
    And actor "recipient" has "name" is "Eve"
    And actor "recipient" has "organization" is "Acme Inc"
            
    When "Bob" does "end"
    Then the process ended
    
```
{% endcode %}

### Scenario

An actor in a scenario is defined as a schema. That means we can specify which properties can be set for an actor. For both actors, we want to know the name and organization.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
    properties:
      name: string
      organization: string    
  recipient:
    title: Recipient
    properties:
      name: string
      organization: string
        
actions:
  introduce:
    title: Introduce yourself with your name and organization
    update:
      set: current.actor
      mode: merge

states:
  initial:
    action: introduce
    by: initiator
    goto: wait_on_introduction_recipient
  wait_on_introduction_recipient:
    action: introduce
    by: recipient
    goto: wait_on_initiator
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
      "title": "Initiator",
      "properties": {
        "name": "string",
        "organization": "string"
      }
    },
    "recipient": {
      "title": "Recipient",
      "properties": {
        "name": "string",
        "organization": "string"
      }
    }
  },
  "actions": {
    "introduce": {
      "title": "Introduce yourself with your name and organization",
      "update": {
        "set": "current.actor",
        "mode": "merge"
      }
    }
  },
  "states": {
    "initial": {
      "action": "introduce",
      "by": "initiator",
      "goto": "wait_on_introduction_recipient"
    },
    "wait_on_introduction_recipient": {
      "action": "introduce",
      "by": "recipient",
      "goto": "wait_on_initiator"
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

## Using names

In the conversation scenario, we created a conversation log by appending the response of the "_talk_" action to the process result. To improve the log, we'll prepend the name of the actor to the response.

### Test case

{% code title="introduction/result.feature" %}
```gherkin
Feature: Two actors meet, exchange information, and have a conversation

  Background:
    Given the process is created using the "introduction" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor

  Scenario:
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    And "Eve" does "introduce" with:
      | name         | Eve      |
      | organization | Acme Inc |
    
    When "Bob" does "talk" with "How are you?"
    And "Eve" does "talk" with "Fine, how about you?"
    And "Bob" does "talk" with "Well, thank you"

    When "Eve" does "end"
    Then the process ended with:
      """
      - Bob - How are you?
      - Eve - Fine, how about you?
      - Bob - Well, thank you
      """
```
{% endcode %}

### Scenario

We can combine the name of the actor and the response using the [`<tpl>` data function](../reference/scenario/data-instruction.md). This function applies the process data to a [mustache](https://mustache.github.io/) template.

By default an update instruction uses the current response as the value. We can overwrite this by setting the `value` property of the update instruction.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
    properties:
      name: string
      organization: string    
  recipient:
    title: Recipient
    properties:
      name: string
      organization: string

actions:
  introduce:
    update:
      set: current.actor
      mode: merge
  talk:
    update:
      set: result
      mode: append
      value: !tpl '{{ current.actor.name }} - {{ current.response }}'

states:
  initial:
    action: introduce
    by: initiator
    goto: wait_on_introduction_recipient
  wait_on_introduction_recipient:
    action: introduce
    by: recipient
    goto: wait_on_initiator
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

result:
  type: array
  items: string
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "actors": {
    "initiator": {
      "title": "Initiator",
      "properties": {
        "name": "string",
        "organization": "string"
      }
    },
    "recipient": {
      "title": "Recipient",
      "properties": {
        "name": "string",
        "organization": "string"
      }
    }
  },
  "actions": {
    "introduce": {
      "update": {
        "set": "current.actor",
        "mode": "merge"
      }
    },
    "talk": {
      "update": {
        "set": "result",
        "mode": "append",
        "data": {
          "<tpl>": "{{ current.actor.name }} - {{ current.response }}"
        }
      },
  },
  "states": {
    "initial": {
      "action": "introduce",
      "by": "initiator",
      "goto": "wait_on_introduction_recipient"
    },
    "wait_on_introduction_recipient": {
      "action": "introduce",
      "by": "recipient",
      "goto": "wait_on_initiator"
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

We've specified that the result is an array of strings. Specifying the result schema is not required, but is a good practice If the data of an actor or the result doesn't match the given schema, the action will fail and be skipped.

## Validating the response

Adding a response schema to an action ensures that the response is always given in the correct format. If the response doesn't match the schema the action will fail.

### Test case

{% code title="introduction/validation.feature" %}
```gherkin
Feature: Actors don't give a proper introduction

  Background:
    Given the process is created using the "introduction" scenario
    And "Bob" is the "initiator" actor
    And "Eve" is the "recipient" actor

  Scenario: The initiator doesn't tell his organization
    When "Bob" does "introduce" with:
      | name         | Bob   |
    Then the last event is skipped
    
  Scenario: The recipient doesn't tell her name
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    Then the process is in "wait_on_introduction_recipient"
    
    When "Eve" does "introduce" with:
      | organization | Acme Inc |
    Then the last event is skipped

  Scenario: The initiator tells too much
    When "Bob" does "introduce" with:
      | name         | Bob     |
      | organization | Jasny   |
      | hobby        | Fishing |
      | age          | 42      |
    Then the last event is skipped
```
{% endcode %}

### Scenario

We add a `response` property with a schema for the actions.

In YAML we can use `!required` to specify that a property is required. For JSON we need to use the `required` property as defined by JSON Schema.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  initiator:
    title: Initiator
    properties:
      name: string
      organization: string    
  recipient:
    title: Recipient
    properties:
      name: string
      organization: string

actions:
  introduce:
    response:
      type: object
      properties:
        name: !required string
        organization: !required string  
    update:
      set: current.actor
      mode: merge
  talk:
    response: string
    update:
      set: result
      mode: append
      value: !tpl '{{ current.actor.name }} - {{ current.response }}'

states:
  initial:
    action: introduce
    by: initiator
    goto: wait_on_introduction_recipient
  wait_on_introduction_recipient:
    action: introduce
    by: recipient
    goto: wait_on_initiator
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

result:
  type: array
  items: string

```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "actors": {
    "initiator": {
      "title": "Initiator",
      "properties": {
        "name": "string",
        "organization": "string"
      }
    },
    "recipient": {
      "title": "Recipient",
      "properties": {
        "name": "string",
        "organization": "string"
      }
    }
  },
  "actions": {
    "introduce": {
      "response": {
        "type": "object",
        "properties": {
          "name": "string",
          "organization": "string"
        },
        "required": ["name", "organization"]
      }
      "update": {
        "set": "current.actor",
        "mode": "merge"
      }
    },
    "talk": {
      "response": "string",
      "update": {
        "set": "result",
        "mode": "append",
        "data": {
          "<tpl>": "{{ current.actor.name }} - {{ current.response }}"
        }
      }
    }
  },
  "states": {
    "initial": {
      "action": "introduce",
      "by": "initiator",
      "goto": "wait_on_introduction_recipient"
    },
    "wait_on_introduction_recipient": {
      "action": "introduce",
      "by": "recipient",
      "goto": "wait_on_initiator"
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
