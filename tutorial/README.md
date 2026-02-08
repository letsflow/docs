---
coverY: 0
---

# Tutorial

Learn how to design, test, and run workflows with LetsFlow. These tutorials will take you from the basics of creating and testing scenarios to handling complex interactions with multiple actors.

Each tutorial introduces new concepts step by step, allowing you to build dynamic and structured conversations using LetsFlow’s state-based engine. By the end, you’ll be able to create workflows that model real-world interactions with actors, conditions, and validations.

## **1. The basics**

This tutorial introduces the fundamental concepts of LetsFlow, including workflows, actors, actions, and transitions. You’ll learn to define a simple workflow where two actors interact, set up actions that trigger state changes, and run your first test case to validate the flow. This foundation will help you understand how LetsFlow structures conversations and how different components work together.

{% content-ref url="the-basics.md" %}
[the-basics.md](the-basics.md)
{% endcontent-ref %}

## **2. A handshake**

Building on the basics, this tutorial demonstrates how two actors engage in a simple handshake interaction. You’ll define transitions where each actor takes a turn, ensuring the workflow progresses smoothly. This example illustrates how LetsFlow tracks actor states and enforces a structured sequence of actions, giving you a clearer understanding of state-based interactions.

{% content-ref url="a-handshake.md" %}
[a-handshake.md](a-handshake.md)
{% endcontent-ref %}

## **3. A conversation**

Conversations often involve multiple exchanges between actors, and this tutorial expands your workflow to include back-and-forth interactions. You’ll see how LetsFlow manages multi-step dialogues, allowing actors to respond in sequence while maintaining control over the flow. This step is essential for modeling more complex scenarios where responses influence what happens next.

{% content-ref url="a-conversation.md" %}
[a-conversation.md](a-conversation.md)
{% endcontent-ref %}

## **4. A proper introduction**

In many workflows, actors need to provide structured information, such as names or organizational details. This tutorial introduces schemas, which define expected response formats and validate user input. You’ll learn how to enforce these rules, ensuring that actors provide correctly formatted data before moving forward in the workflow. This is especially useful for building structured conversations with predefined inputs.

{% content-ref url="a-proper-introduction.md" %}
[a-proper-introduction.md](a-proper-introduction.md)
{% endcontent-ref %}

## **5. Group meeting**

Not all conversations follow a fixed script, and sometimes you need to handle an unknown number of participants. This tutorial introduces wildcard actors, allowing any number of people to join a workflow dynamically. You’ll also use conditions to control transitions, ensuring that the conversation progresses smoothly regardless of the number of participants. This approach is useful for scenarios like meetings, forums, and open-ended discussions.

{% content-ref url="group-meeting.md" %}
[group-meeting.md](group-meeting.md)
{% endcontent-ref %}

## 6. Quote

Many real-world workflows involve collecting information in stages before providing a response. This tutorial walks you through building a simple quote request scenario. You’ll define steps to gather customer input, handle missing information, and generate a personalized response. Along the way, you’ll learn how to use branching, retries, and computed values to create more interactive and adaptive workflows—ideal for support requests, service quotes, and intake forms.

{% content-ref url="quote.md" %}
[quote.md](quote.md)
{% endcontent-ref %}
