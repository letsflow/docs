# Test suite

The LetsFlow test-suite allows you to **validate LetsFlow scenarios** (defined in YAML) through **behaviour tests** using Cucumber. It ensures that:

* Scenarios are correctly **written and valid** (schema-compliant).
* The LetsFlow engine **interprets and executes** them as expected.
* Tests simulate real-world actor inputs and decisions in a **controlled way**.

This helps scenario authors verify workflows behave correctly across different conditions without needing to deploy or manually test them.

{% embed url="https://github.com/letsflow/testing" %}

## Installation

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

Run the tests

```bash
yarn test:workflows
```

## Gherkin

Workflow tests are written in [Gherkin](https://cucumber.io/docs/gherkin/), a simple language for describing behavior in a readable way.

Gherkin provides a human-readable way to describe the steps and outcomes of your workflows using the **Given-When-Then** structure.

### Example

```gherkin
Feature: Run the example scenario
  Background:
    Given the process is created from the "example" scenario
    And "Alice" is the "client" actor 

  Scenario: Actor completes the process
    When "Alice" does "complete"
    Then the process has ended.
```

Scenarios can be loaded from YAML files in the `scenarios` directory. The tests are loaded from the `.feature` files in the `features` directory.

### Steps

The test suite provides a number of `Given`, `When` , and `Then` steps that allow you to set up a new process, step through it, and assert the outcome.

`Given` steps in gherkin are used to describe the initial context of the system. In the LetsFlow test suite, the `Given` steps are used to define the processes and introduce the actors.

{% content-ref url="given.md" %}
[given.md](given.md)
{% endcontent-ref %}

`When` steps are used to describe an _action_. This can be a actor interacting with the system, or it can be an event triggered by another system.

{% content-ref url="when.md" %}
[when.md](when.md)
{% endcontent-ref %}

`Then` steps are used to describe an _expected_ outcome or result. They perform an assertion.

{% content-ref url="then/" %}
[then](then/)
{% endcontent-ref %}
