# Custom JMESPath functions

{% hint style="danger" %}
This page is a stub. More information will be provided
{% endhint %}

The YAML library exposes `registerFunction` allowing you to add custom functions to JMESPath. These functions can not be async and should be [pure](https://en.wikipedia.org/wiki/Pure_function) (deterministic with no side effects).
