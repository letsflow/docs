# Customize

The test-suite includes generic step definitions for most common scenarios. For projects with specific needs, you can define custom step definitions. These have access to a specialized `World` class that supports working with multiple named processes and actors.

## World

The `world` instance provides methods for managing process instances and actors:

* `addProcess(name, scenario)`: Load and register a scenario under a given process name.
* `addActor(name, process, key, properties)`: Add an actor with a key and optional metadata to a specific process.
* `getProcess(name)`: Retrieve the `Process` instance by name.
* `getActorByName(process, name)`: Retrieve the actor’s key and properties.

This design supports testing multiple processes and services in parallel or isolation.

## Example: actor must have a role

Here’s a custom step to assert that an actor has a specific role, assuming it's stored as part of the actor's properties.

```ts
import { Then } from '@cucumber/cucumber';
import { world } from '@letsflow/testing';

Then(
  'actor {string} in process {string} should have role {string}',
  function (actorName, processName, expectedRole) {
    const actor = world.getActorByName(processName, actorName);
    if (actor.role !== expectedRole) {
      throw new Error(
        `Expected role "${expectedRole}", but got "${actor.role}" for actor "${actorName}" in process "${processName}"`
      );
    }
  }
);
```

This step checks actor metadata and can be used to validate setup or process-specific logic.

## Directory and setup

Cucumber automatically loads step definitions from files in your `features/step_definitions` directory. No additional registration is required.

Custom step files should use TypeScript and declare the `this` context as `CustomWorld` to get proper typing and autocompletion.
