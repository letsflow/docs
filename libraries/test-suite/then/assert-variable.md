# Assert variable

## Variables

Assert that a [process variable](../../../reference/process/#vars) (`vars`) has the expected value.

```gherkin
Then variable "price" is 100
Then variable "price" in the "main" process is 100

Then variable "requirements" is:
   """
   The product should be good and cheap.
   It must not break
   """
Then variable "address" is:
   | street | Red street 1 | 
   | city   | Amsterdam    |
```

If the property is a string, number, or boolean, it can be given inline. You can use a data table, if the response is a simple object with a shallow structure. For arrays and more complex objects, use YAML or JSON.

## Result

Assert that the [process result](../../../reference/process/#result) has the expected value.

```gherkin
Then the result is "Foo Bar"
Then the "main" process result is "Foo Bar"

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

The value can be given inline or as data table, YAML or JSON.

## Tags

Assert that the process has a specific [tag](../../../reference/process/#tags).

```gherkin
Then the process has tag "approved"
Then the "main" process has tag "approved"
```

### Inverse

Assert that the process doesn't have a specific tag.

```gherkin
Then the process doesn't have tag "denied"
```
