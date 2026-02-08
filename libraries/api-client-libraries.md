# Core library

{% hint style="danger" %}
This page is a stub. More information will be provided
{% endhint %}

{% embed url="https://github.com/letsflow/letsflow/" %}

```bash
yarn add @letsflow/core
```

## normalize

The normalized JSON is convenient for processing by a computer. But is very verbose and therefore less readable by humans.

Pretty JSON collapses arrays, maps where there is only one option and omits properties that have the default value. This improves readability for humans, but is more difficult for computers are the structure and data types differ based on the data.

The YAML format is similar to pretty JSON, with the addition of custom tags for [data functions](../reference/scenario/data-instruction.md). YAML is the recommended format for defining scenarios.
