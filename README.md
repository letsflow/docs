---
description: LetsFlow · human-centric workflow automation
cover: .gitbook/assets/Colored.png
coverY: 0
---

# Introduction

**Letsflow is a human-centric workflow automation engine that blends automation with human decision-making, enabling dynamic interactions between people and automated steps.**

Unlike traditional workflow engines that use Directed Acyclic Graphs (DAGs) for fully automated workflows, LetsFlow is built on an extended finite-state machine model, offering flexibility for dynamic and non-linear processes.

With LetsFlow, you can define workflows where actors—real people—play a central role, while seamlessly integrating automated steps. This makes it ideal for scenarios like approval systems, customer service workflows, and collaborative processes, where decisions and actions often depend on human input.

{% tabs %}
{% tab title="YAML" %}
```yaml
# $schema: "https://schemas.letsflow.io/v1.0/scenario"
title: My first scenario

actors:
  user:
    title: The user

actions:
  complete:
    title: Complete the process

states:
  initial:
    on: complete
    goto: (done)
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "$schema": "https://schemas.letsflow.io/v1.0/scenario",
    "title": "My first scenario",
    "actors": {
        "user": {
            "title": "user"
        }
    },
    "actions": {
        "complete": {
            "title": "Complete the process"
        }
    },
    "states": {
        "initial": {
            "action": "complete",
            "transition": "(done)"
        }
    }
}
```
{% endtab %}
{% endtabs %}

## Getting started

If you're new to LetsFlow, it's recommended that you start with the [tutorial](tutorial/), which teaches you how to create a **scenario**. The tutorial introduces all the important features of LetsFlow one at a time.

The tutorial also teaches you how to use the test suite. This is an important tool when creating production-quality scenarios.

You can integrate LetsFlow directly into your application using the core library. However, it's easier to use the **engine** allowing you to interact with a simple REST API. The engine takes care of access control, storage, and timed events. It uses messaging to interact with your backend or microservices.

LetsFlow easily integrates into your frontend, regardless of whether you're using React, Angular, Vue, Svelte, or any other component framework. You’ll define reusable action schemas and build components that match these schemas.
