# Assert service

## Notification

Assert that a service has been notified.

```gherkin
Then service "email" is notified
Then service "email" in the main process is notified
```

### Notification properties

You can use `with` to specify expected properties for the notification.

```gherkin
Then service "email" is notified with:
  | name    | Alice Almond      |
  | email   | alice@example.com |
  | message | Hello Alice       |

Then service "email" is notified with:
  """yaml
    schema: messages/email-v1
    to:
      name: Alice
      email: alice@example.com
    template: no-quote
    data:
      customer:
        title: Customer
        id: !ref actors.customer.id
        company: Acme Inc
        contact: Alice
        email: alice@example.com
        address: 123 Main St
      reason: Unable to deliver on requirements
  """
```

You can use a data table, but this is limited to a shallow structure. Alternatively, you can use YAML or JSON.

### Inverse

Assert that a service has not been notified.

```gherkin
Then service "email" is not notified
Then service "email" in the "main" process is not notified
```
