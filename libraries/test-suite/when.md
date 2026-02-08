# When

`When` steps are used to describe an _action_. This can be a actor interacting with the system, or it can be an event triggered by another system.

## Step

```gherkin
When "Alice" does "accept"
When "Alice" does "accept" in the "main" process
```

An actor performs an action in a process. Use the actor's name as defined by the _Given_ statement and the key of the action that is performed. By default, the "main" process is selected.

### Service step

```gherkin
When the "verify" service does "approve"
When the "verify" service does "approve" in the "main" process
```

A service can perform an action rather than an actor. The syntax is similar.

### Response

You can use `with` to specify the response

```gherkin
When "Alice" does "deny" with "Too expensive"

When "Alice" does "deny" with:
   | reason | Too expensive    |
   | email  | alice@example.com |
  
When "Alice" does "deny" with:
   """yaml
     reason: Too expensive
     email: alice@example.com
   """
   
When the "verify" service does "reject" with "Insufficient balance"
```

If the response expects a string, date, number, or boolean, it can be given inline. You can use a data table, if the response is a simple object with a shallow structure. For arrays and more complex objects, use YAML or JSON.

## Timeout

```gherkin
When 10 minutes pass
When 1 hour passes
When 30 minutes pass for the "main" process
```

Trigger a timeout state change. Specify the period in English. By default, the timeout is applied to all running processes. You can specify which process, if needed.

Note: If there are multiple timeouts, only the first will trigger.

