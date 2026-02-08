# Given

`Given` steps in gherkin are used to describe the initial context of the system. In the LetsFlow test suite, the `Given` steps are used to define the processes and introduce the actors.

## Define process

```gherkin
Given the process is created from the "example" scenario
Given the "main" process is created from the "example" scenario
```

Specify the scenario of the process. By default, this is the "main" process, but you can create multiple name processes.

The scenario file (`.yaml` or `.json`) is loaded from the `scenarios` directory. This directory can be changed using the `LETSFLOW_SCENARIO_PATH` environment variable.

## Define person

```gherkin
Given "Alice" is the actor
Given "Alice" is the "client" actor
Given "Alice" is the actor in the "main" process
Given "Alice" is the "client" actor in the "main" process
```

Give the person a name and specify which actor they play. A person can only be one actor in a process.

If the scenario only has the default actor (with the key `actor`), you don't need to specify which actor the person is. However, in most cases, you need to specify the actor key.

If there's more than one process, specify the process name.

### User properties

You can use `with` to specify user properties for a person.

```gherkin
Given "Alice" is the actor with:
   | name  | Alice Almond      |
   | email | alice@example.com |
  
Given "Alice" is the actor with:
   """yaml
     name: Alice Almond
     email: alice@example.com
   """
```

You can use a data table, but this is limited to a shallow structure. Alternatively, you can use YAML or JSON.

{% hint style="warning" %}
User properties are not used to set the actor properties during instantiation but can be referenced in the scenario via `events[*].actor`.
{% endhint %}
