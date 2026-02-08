# Assert state

## Running state

Assert that the process is in the expected state.

```gherkin
Then the process is in "waiting_for_client"
Then the "main" process is in "waiting_for_client"
```

## End state

Check if the process has ended. Typically, you want to specify which end state.

```gherkin
Then the process ended
Then the process ended in "success"
Then the "main" process ended in "success"
```

### Process result

You can also check the [process result](../../../reference/process/#result) with the same assertion.

```gherkin
Then the process ended with "Foo Bar"
Then the process ended in "success" with "Foo Bar"

Then the process ended with:
   | document    | somedoc.pdf |
   | description | Foo Bar     |
Then the process ended with:
   """yaml
     documents:
       - quote.pdf
       - plan.pdf
   """
```

If the result schema is a string, number, or boolean, it can be given inline. You can use a data table, if the response is a simple object with a shallow structure. For arrays and more complex objects, use YAML or JSON.

## State property

Assert that a property of the current state has the expected value. This is particularly useful for [data functions](../../../reference/scenario/data-instruction.md).&#x20;

```gherkin
Then the state description is "Waiting for the client"
Then the state description of the "main" process is "Waiting ..."

Then the state instructions are:
  | admin  | Wait for the client              |
  | client | Please choose one of the options |
```

You can specify the value inline, as data table, or as YAML / JSON.

### Instructions

Check the instructions for a specific actor in the current state.

```gherkin
Then actor "admin" has the instruction "Wait for the client"
Then actor "client" has instructions:
  """
  Please review the quote.
  The total amount is €100.
  You can accept or reject the offer.
  """
```

You can specify the instructions inline or as doc string.
