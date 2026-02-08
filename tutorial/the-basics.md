---
description: Running your first workflow
---

# The basics

In this tutorial, we'll create a new Node.js project, install the test suite, and run our first workflow.

## Create a project&#x20;

Use npm, yarn, or pnpm to create a new project. Install [cucumber](https://cucumber.io/) and the LetsFlow test suite.

```bash
yarn init
yarn add --dev @cucumber/cucumber @letsflow/testing
```

Copy the cucumber configuration from the test suite and create directories from the scenario and test files.

```bash
cp node_modules/@letsflow/testing/cucumber.example.yaml ./cucumber.yaml
mkdir scenarios features
```

Edit `package.json` and add the `test:workflows` script.

```json
  "scripts": {
    "test:workflows": "cucumber-js --profile workflows"
  }
```

## My first scenario

The scenario describes the process we're automating. We can write a scenario in either JSON or YAML.

{% hint style="success" %}
Create file `basics.yaml` (or `basics.json`) in the `scenarios` directory.
{% endhint %}

A scenario has states. A new process will always start in the "_initial"_ state. We define that if we perform the "_complete"_ action, the process will transition to the "_(done)"_ state.

{% tabs %}
{% tab title="YAML" %}
{% code title="basics.yaml" %}
```yaml
states:
  initial:
    on: complete
    goto: (done)
```
{% endcode %}
{% endtab %}

{% tab title="JSON" %}
{% code title="basics.json" %}
```javascript
{
  "states": {
    "initial": {
      "on": "complete",
      "goto": "(done)",
    }
  }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

A state with a name in parenthesis, like "_(done)_", is an end state. There are no transitions from an end state. Unlike normal states, end states can be used in a `goto` statement without defining them explicitly.

## Running a test

The scenario can be tested by writing a BDD test in the [Gherkin](https://cucumber.io/docs/gherkin/reference) language. Gherkin uses a set of special keywords to give structure and meaning so the test can be executed.

The LetsFlow test suite comes with a predefined set of statements that can be used to test running a scenario.

{% hint style="success" %}
Create file `basics.feature` in the `features` directory.
{% endhint %}

{% code title="basics.feature" %}
```gherkin
Feature: Run a simple process that is completed in one step

  Background:
    Given the process is created from the "basics" scenario
    And "Joe" is the actor

  Scenario:
    When "Joe" does "complete"
    Then the process ended
```
{% endcode %}

Run the test from the command line

```bash
yarn test:workflows features/basics.feature
```

## Congratulations!

You've successfully created and tested your first workflow.
