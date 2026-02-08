---
description: Introduction to wildcard actors and conditions
---

# Group meeting

The previous tutorial had our two actors introducing themselves. In that scenario, we've given one actor the role of initiator and another of recipient.

In this tutorial, we'll model a situation where a random number of people have a conversation. They first all need to introduce themselves after which each one can talk.

{% hint style="success" %}
* Create file `meeting.yaml` in `scenarios`.
* Create subdirectory `meeting` in `features` for the test files.
{% endhint %}

## Start action

So far, we've created scenarios in which an actor initiates the process. In this tutorial, we’ll introduce a start action, which can only be triggered by a service, such as your backend or a microservice. This is a common pattern that prevents users from starting a process directly.

In this case, a start action is necessary because the scenario involves only a wildcard actor. Instead of relying on predefined participants, the start action will dynamically create the actors participating in the process.

{% tabs %}
{% tab title="YAML" %}
```yaml
start:
  actor: service:default
  update: actors
  response:
    patternProperties:
      '^person_.*$':
        properties:
          name: string
          organization: string
        nullable: true
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "start": {
    "actor": "service:default",
    "response": {
      "patternProperties": {
        "^person_.*$": {
          "properties": {
            "name": "string",
            "organization": "string"
          },
          "nullable": true
        }
      }
    },
    "update": "actors"
  }
}
```
{% endtab %}
{% endtabs %}

## Wildcard actors

We don't exactly know how many participants the process will have. However, we do that each participant has essentially the same role. We'll number the actors when we instantiate the process.

### Test case

In the _initial_ state, everyone has the opportunity to introduce themselves. With an introduction, we stay in the _initial_ state. When someone starts to talk we transition to the _talking_ state.

{% code title="meeting/golden.feature" %}
```gherkin
Feature: Three persons introduce themselves and have a conversation

  Background:
    Given the process is created from the "meeting" scenario
    And "Bob" is the "person_1" actor
    And "Eve" is the "person_2" actor
    And "Jack" is the "person_3" actor

    When the "default" service does "start" with:
    """yaml
      person_1: ~
      person_2: ~
      person_3: ~
    """
    Then the process is in "introductions"
    And the process has actor "person_1"
    And the process has actor "person_2"
    And the process has actor "person_3"

  Scenario:
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    Then the process is in "introductions"
    And the last event is not skipped
    And actor "person_1" has "name" is "Bob"
    And actor "person_1" has "organization" is "Jasny"

    When "Eve" does "introduce" with:
      | name         | Eve      |
      | organization | Acme Inc |
    Then the process is in "introductions"
    And the last event is not skipped
    And actor "person_2" has "name" is "Eve"
    And actor "person_2" has "organization" is "Acme Inc"

    When "Jack" does "introduce" with:
      | name         | Jack        |
      | organization | LTO Network |
    Then the process is in "introductions"
    And the last event is not skipped
    And actor "person_3" has "name" is "Jack"
    And actor "person_3" has "organization" is "LTO Network"

    When "Bob" does "talk" with "What do you think about the weather?"
    Then the process is in "talking"
    And the last event is not skipped

    When "Eve" does "talk" with "It's pretty cold"
    Then the process is in "talking"
    And the last event is not skipped

    When "Bob" does "talk" with "True, but at least it's not raining"
    Then the process is in "talking"
    And the last event is not skipped

    When "Jack" does "talk" with "I like blockchain!"
    Then the process is in "talking"
    And the last event is not skipped

    Then the result is:
      """yaml
      - Bob - What do you think about the weather?
      - Eve - It's pretty cold
      - Bob - True, but at least it's not raining
      - Jack - I like blockchain!
      """
```
{% endcode %}

### Scenario

Instead of defining the actors individually, we use a wildcard. The actor schema is applied to every actor with the key `person_{number}`.

For `goto` we can use `null` (which is `~` in YAML) to indicate that we don't want to transition to another state.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  person_*:
    properties:
      name: string
      organization: string    

actions:
  start:
    actor: service:default
    update: actors
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
    on: start
    goto: introductions
  introductions:
    transitions:
      - on: introduce
        goto: ~
      - on: talk
        goto: talking
  talking:
    transitions:
      - on: talk
        goto: ~
      - on: end
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
    "person_*": {
      "properties": {
        "name": "string",
        "organization": "string"
      }
    }
  },
  "actions": {
    "start": {
      "actor": "service:default",
      "update": "actors"
    },
    "introduce": {
      "response": {
        "type": "object",
        "properties": {
          "name": "string",
          "organization": "string"
        },
        "required": ["name", "organization"]
      },
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
      "on": "start",
      "goto": "introductions"
    },
    "introductions": {
      "transitions": [
        {
          "on": "introduce",
          "goto": null
        },
        {
          "on": "talk",
          "goto": "talking"
        }
      ]
    },
    "talking": {
      "transitions": [
        {
          "on": "talk",
          "goto": null
        },
        {
          "on": "end",
          "goto": "(done)"
        }
      ]
    }
  },
  "result": {
    "type": "array",
    "items": "string"
  }
}

```
{% endtab %}
{% endtabs %}

It's bad practice to have a process without an end state. We'll add a way to end the conversation later in this tutorial.

## Everyone introduced?

In the introduction tutorial, the state machine ensured that both parties introduced themselves before starting the conversation. However, in this scenario, that requirement no longer applies. If the actors' names are already known, they don’t need to introduce themselves. Additionally, actors should not be required to introduce themselves more than once.

### Test case

{% code title="meeting/conditions.feature" %}
```gherkin
Feature: People are not committing to the social norms

  Background:
    Given the process is created from the "meeting" scenario
    And "Bob" is the "person_1" actor
    And "Eve" is the "person_2" actor
    And "Jack" is the "person_3" actor

    When the "default" service does "start" with:
    """yaml
      person_1: ~
      person_2: ~
      person_3: ~
    """
    Then the process is in "introductions"
    And the process has actor "person_1"
    And the process has actor "person_2"
    And the process has actor "person_3"

  Scenario: Person introduces himself and starts to talk
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    And "Bob" does "talk" with "What do you think about the weather?"
    Then the last event is skipped

  Scenario: Person starts talking without introducing himself
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    When "Eve" does "introduce" with:
      | name         | Eve      |
      | organization | Acme Inc |
    When "Jack" does "talk" with "I like blockchain!"
    Then the last event is skipped

  Scenario: Person does an introduction twice
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    When "Bob" does "introduce" with:
      | name         | Bob Builder |
      | organization | Jasny       |
    Then the last event is skipped

  Scenario: Person tries to talk twice
    When "Bob" does "introduce" with:
      | name         | Bob   |
      | organization | Jasny |
    When "Eve" does "introduce" with:
      | name         | Eve      |
      | organization | Acme Inc |
    When "Jack" does "introduce" with:
      | name         | Jack        |
      | organization | LTO Network |
    When "Bob" does "talk" with "What do you think about the weather?"
    Then the process is in "talking"
    When "Bob" does "talk" with "I like it"
    Then the last event is skipped
```
{% endcode %}

### Scenario

We can use `if` conditions to disallow an action or a transition. The field needs to contain a boolean value. We typically use [`<ref>` data function](../reference/scenario/data-instruction.md) for conditions. This function performs a [JMESPath](https://jmespath.org/) query against the process.

For the "_introduce_" action we specify a condition to prevent an actor from performing it if we already know its name.

We'll add a condition statement for state transition from "_initial_" to "_talking_", which validates that the name of every actor is known.

The last condition is on the "_talk_" action. We compare if the current actor is not the actor of the previous event to ensure a person doesn't talk twice.

{% tabs %}
{% tab title="YAML" %}
```yaml
actors:
  person_*:
    properties:
      name: string
      organization: string

actions:
  start:
    actor: service:default
    update: actors
  introduce:
    if: !ref current.actor.name == null
    response:
      type: object
      properties:
        name: !required string
        organization: !required string
    update:
      set: current.actor
      mode: merge
  talk:
    if: !ref current.actor.key != previous[-1].actor.key
    response: string
    update:
      set: result
      mode: append
      value: !tpl '{{ current.actor.name }} - {{ current.response }}'

states:
  initial:
    on: start
    goto: introductions
  introductions:
    transitions:
      - on: introduce
        goto: ~
      - on: talk
        if: !ref values(actors)[?name == null] | length(@) == 0
        goto: talking
  talking:
    transitions:
      - on: talk
        goto: ~
      - on: end
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
    "person_*": {
      "properties": {
        "name": "string",
        "organization": "string"
      }
    }
  },
  "actions": {
    "start": {
      "actor": "service:default",
      "update": "actors"
    },
    "introduce": {
      "if": {
        "<ref>": "current.actor.name == null"
      },
      "response": {
        "type": "object",
        "properties": {
          "name": "string",
          "organization": "string"
        },
        "required": ["name", "organization"]
      },
      "update": {
        "set": "current.actor",
        "mode": "merge"
      }
    },
    "talk": {
      "if": {
        "<ref>": "events[-1].actor.key != (events[?(!skipped && actor)] | @[-2]).actor.key"
      },
      "response": "string",
      "update": {
        "set": "result",
        "mode": "append",
        "data": {
          "<tpl>": "{{ current.actor.name }} - {{ current.response }}"
        }
      }
    },
    "end": {
      "if": {
        "<ref>": "events[-1].actor.key != (events[?(!skipped && actor)] | @[-2]).actor.key"
      }
    }
  },
  "states": {
    "initial": {
      "on": "start",
      "goto": "introductions"
    },
    "introductions": {
      "transitions": [
        {
          "on": "introduce",
          "goto": null
        },
        {
          "on": "talk",
          "if": {
            "<ref>": "values(actors)[?name == null] | length(@) == 0"
          },
          "goto": "talking"
        }
      ]
    },
    "talking": {
      "transitions": [
        {
          "on": "talk",
          "goto": null
        },
        {
          "on": "end",
          "goto": "(done)"
        }
      ]
    }
  },
  "result": {
    "type": "array",
    "items": "string"
  }
}
```
{% endtab %}
{% endtabs %}

## No introductions

When all actors are known from the start, we can skip the **"introductions"** state entirely and move directly into the **"talking"** state, streamlining the workflow.

### Test case

{% code title="meeting/no-introductions.feature" %}
```gherkin
Feature: Two persons have a conversation without an introduction

  Background:
    Given the process is created from the "meeting" scenario
    And "Bob" is the "person_1" actor
    And "Eve" is the "person_2" actor

    When the "default" service does "start" with:
    """yaml
      person_1:
        name: Bob
        organization: Jasny
      person_2:
        name: Eve
        organization: Acme Inc
    """
    Then the process is in "talking"
    And the process has actor "person_1"
    And the process has actor "person_2"

  Scenario:
    When "Bob" does "talk" with "What do you think about the weather?"
    Then the process is in "talking"
    And the last event is not skipped

    When "Eve" does "talk" with "It's pretty cold"
    Then the process is in "talking"
    And the last event is not skipped

    Then the result is:
      """yaml
      - Bob - What do you think about the weather?
      - Eve - It's pretty cold
      """
```
{% endcode %}

### Scenario

We use an `if` condition for the state transition that checks if there are any actors for which the name is unknown.

{% tabs %}
{% tab title="YAML" %}
```yaml
states:
  initial:
    transitions:
      - on: start
        goto: introductions
        if: !ref values(actors)[?name == null] | length(@) > 0
      - on: start
        goto: talking
```
{% endtab %}

{% tab title="JSON" %}
```json
"states": {
  "initial": {
    "transitions": [
      {
        "on": "start",
        "goto": "introductions",
        "if": {
          "<ref>": "values(actors)[?name == null] | length(@) > 0"
        }
      },
      {
        "on": "start",
        "goto": "talking"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

## Leaving the conversation

Instead of ending the interaction, any person can choose to leave. The remaining persons can continue the conversation. When there are less than two persons left, the process should end.

### Test case

{% code title="meeting/leave.feature" %}
```gherkin
Feature: People leave the conversation

  Background:
    Given the process is created from the "meeting" scenario
    And "Bob" is the "person_1" actor
    And "Eve" is the "person_2" actor
    And "Jack" is the "person_3" actor

    When the "default" service does "start" with:
    """yaml
      person_1:
        name: Bob
        organization: Jasny
      person_2:
        name: Eve
        organization: Acme Inc
      person_3:
        name: Jack
        organization: LTO Network
    """
    Then the process is in "talking"
    And actor "person_1" has "is_present" is true
    And actor "person_2" has "is_present" is true
    And actor "person_3" has "is_present" is true

  Scenario: Person leaves and later the second leaves
    When "Bob" does "talk" with "What do you think about the weather?"
    When "Eve" does "talk" with "It's pretty cold"
    When "Bob" does "talk" with "True, but at least it's not raining"
    When "Jack" does "talk" with "I like blockchain!"

    When "Bob" does "leave"
    Then actor "person_1" has "is_present" is false
    And the process is in "talking"

    When "Eve" does "talk" with "What is blockchain?"
    And "Jack" does "talk" with "Do you know Bitcoin?"
    And "Eve" does "talk" with "*sigh*"

    When "Eve" does "leave"
    Then actor "person_2" has "is_present" is false
    And the process ended with:
      """yaml
      - Bob - What do you think about the weather?
      - Eve - It's pretty cold
      - Bob - True, but at least it's not raining
      - Jack - I like blockchain!
      - Eve - What is blockchain?
      - Jack - Do you know Bitcoin?
      - Eve - *sigh*
      """

    Scenario: Person tries to say something after already left
      When "Bob" does "talk" with "What do you think about the weather?"
      And "Eve" does "talk" with "It's pretty cold"

      When "Bob" does "leave"
      And "Bob" does "talk" with "True, but at least it's not raining"
      Then the last event is skipped
```
{% endcode %}

### The scenario

We add an `is_present` property to the actor, which defaults to true. The "_leave_" action will set `is_present` to false for the current actor.

As long as there are two or more actors present, the "_leave_" action will not cause a state transition. If that condition is not true, the process will end. Transitions are validated in order. Therefore we only need to have a condition on the first transition of "_leave_" action in the "_talking_" state.

{% tabs %}
{% tab title="YAML" %}
{% code title="meeting.yaml" %}
```yaml
actors:
  person_*:
    properties:
      name: string
      organization: string
      is_present: !default true

actions:
  start:
    actor: service:default
    response:
      patternProperties:
        '^person_.*$':
          properties:
            name: string
            organization: string
          nullable: true
    update: actors
  introduce:
    if: !ref current.actor.name == null
    response:
      type: object
      properties:
        name: !required string
        organization: !required string
    update:
      set: current.actor
      mode: merge
  talk:
    if: !ref current.actor.is_present && current.actor.key != previous[-1].actor.key
    response: string
    update:
      set: result
      mode: append
      value: !tpl '{{ current.actor.name }} - {{ current.response }}'
  leave:
    if: !ref current.actor.is_present
    update:
      set: current.actor.is_present
      value: false

states:
  initial:
    transitions:
      - on: start
        goto: introductions
        if: !ref values(actors)[?name == null] | length(@) > 0
      - on: start
        goto: talking
  introductions:
    transitions:
      - on: introduce
        goto: ~
      - on: talk
        if: !ref values(actors)[?name == null] | length(@) == 0
        goto: talking
  talking:
    transitions:
      - on: talk
        goto: ~
      - on: leave
        if: !ref values(actors)[?is_present] | length(@) >= 2
        goto: ~
      - on: leave
        goto: (done)

result:
  type: array
  items: string

```
{% endcode %}
{% endtab %}

{% tab title="JSON" %}
```javascript
{
  "actors": {
    "person_*": {
      "properties": {
        "name": "string",
        "organization": "string",
        "is_present": {
          "default": true
        }
      }
    }
  },
  "actions": {
    "start": {
      "actor": "service:default",
      "response": {
        "patternProperties": {
          "^person_.*$": {
            "properties": {
              "name": "string",
              "organization": "string"
            },
            "nullable": true
          }
        }
      },
      "update": "actors"
    },
    "introduce": {
      "if": {
        "<ref>": "current.actor.name == null"
      },
      "response": {
        "type": "object",
        "properties": {
          "name": "string",
          "organization": "string"
        },
        "required": ["name", "organization"]
      },
      "update": {
        "set": "current.actor",
        "mode": "merge"
      }
    },
    "talk": {
      "if": {
        "<ref>": "current.actor.is_present && current.actor.key != previous[-1].actor.key"
      },
      "response": "string",
      "update": {
        "set": "result",
        "mode": "append",
        "data": {
          "<tpl>": "{{ current.actor.name }} - {{ current.response }}"
        }
      }
    },
    "leave": {
      "if": {
        "<ref>": "current.actor.is_present"
      },
      "update": {
        "set": "current.actor.is_present",
        "value": false
      }
    }
  },
  "states": {
    "initial": {
      "transitions": [
        {
          "on": "start",
          "goto": "introductions",
          "if": {
            "<ref>": "values(actors)[?name == null] | length(@) > 0"
          }
        },
        {
          "on": "start",
          "goto": "talking"
        }
      ]
    },
    "introductions": {
      "transitions": [
        {
          "on": "introduce",
          "goto": null
        },
        {
          "on": "talk",
          "if": {
            "<ref>": "values(actors)[?name == null] | length(@) == 0"
          },
          "goto": "talking"
        }
      ]
    },
    "talking": {
      "transitions": [
        {
          "on": "talk",
          "goto": null
        },
        {
          "on": "leave",
          "if": {
            "<ref>": "values(actors)[?is_present] | length(@) >= 2"
          },
          "goto": null
        },
        {
          "on": "leave",
          "goto": "(done)"
        }
      ]
    }
  },
  "result": {
    "type": "array",
    "items": "string"
  }
}
```
{% endtab %}
{% endtabs %}
