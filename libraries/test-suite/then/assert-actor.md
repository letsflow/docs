# Assert actor

## Actor exists

Assert that the actor is defined in the process

```gherkin
Then the process has actor "admin"
Then the "main" process has actor "admin"
```

Specify the key of the actor. By default, the "main" process is used.

## Actor property

Check if a property of an actor has the expected value.

```gherkin
Then actor "admin" has "name" is "Joe"
Then actor "admin" in the "main" process has "age" is 42

Then actor "admin" has "address":
   | street | Red street 10 |
   | city   | Amsterdam     |
Then actor "admin" has "pets":
   """yaml
    - dog
    - cat
    - bird
   """
```

If the property is a string, number, or boolean, it can be given inline. You can use a data table, if the response is a simple object with a shallow structure. For arrays and more complex objects, use YAML or JSON.
