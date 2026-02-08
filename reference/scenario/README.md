# Scenario

The scenario is the blueprint of a process. It models a workflow as a fine-state machine. Within the scenario, we define all possible states, the state transitions and actions that trigger them, and the actors participating in the process.

The _scenario_ models a _process_ as a fine-state machine. The _actors_ are persons, organizations, or systems that can participate in the process by performing _actions_. Which actions can be performed depends on the current _state_ of the process. By executing an action, the process can transition to a different state.

{% hint style="info" %}
The scenario may be defined in YAML or JSON.
{% endhint %}

## Schema

`https://schemas.letsflow.io/v1.0/scenario`&#x20;

## Properties

### `id`

_UUID_

The scenario ID is generated based on the sha256 hash of JSON representation. Modifying a scenario will result in a different id.

{% hint style="warning" %}
The engine automatically sets this field. You may omit it from your YAML or JSON file.

If you _do_ need to set this manually, use the `uuid()` function from the core library to generate the correct ID. Using a different UUID may lead to unexpected results.
{% endhint %}

### `name`

string

A consistent name for the scenario. Scenarios can be referenced by name to get different versions. Each version will have a unique ID.

### `version`

_string_

A [semantic version](https://semver.org/) of the scenario. Used in combination with the name.

### `title`&#x20;

_string_

A short description of the workflow. This is copied to the process during instantiation.

### `description`&#x20;

_string_

A short description of the workflow.

### `tags`

array of strings

Tags can be used to filter a list of scenarios. Scenario tags are copied to the process, to be used to filter a list of processes.

### `actors`

_map of actor schemas_

Actors that can participate in the process. The actor schema defines the properties of each actor.

{% content-ref url="actor.md" %}
[actor.md](actor.md)
{% endcontent-ref %}

{% hint style="info" %}
If the `actors` property is omitted, the process will have a single actor named `actor` with only the default properties.
{% endhint %}

### `actions`

_map of actions_

Actions can be implicitly defined through state transitions. However, to extend their functionality—such as adding update instructions, conditions, or properties for frontend integration—they must be explicitly defined.

{% content-ref url="action/" %}
[action](action/)
{% endcontent-ref %}

### `states`

_map of states **(required)**_

The scenario models a workflow as a finite state machine, which is comprised of states and state transitions. A scenario must at least define the `initial` state.

{% content-ref url="state/" %}
[state](state/)
{% endcontent-ref %}

### `vars`

_map of schemas_

Each schema defines a variable that is available in the process. The key of the map is used as the variable name.

### `result`

schema

The result defines the schema for a special variable that represents the process's output. While primarily intended as the final result, this variable can be built up and used throughout the process.

## Additional properties

You may add additional properties to the scenario. These are ignored by the core library and workflow engine.

The properties may have meaning for your application. For example; you can add a `ui` property for options for your front-end components.

```yaml
ui:
  view: stepper
  adminControls: true
```

Additional properties can also be referenced in [data functions](data-instruction.md) when running the process. An example of using a custom `messages` property in the scenario:

```yaml
notify:
  service: email
  message:
    recipient: !ref actors.client
    subject: Welcome to Acme
    body: !ref scenario.messages.introduction
```

{% hint style="warning" %}
Do not use additional properties for filtering. Use the `tags` instead.
{% endhint %}
