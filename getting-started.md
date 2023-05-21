---
title: Getting Started
description: Learn how to register for Usage Panda and begin using it to layer production features over your usage of OpenAI APIs.
layout: default
nav_order: 2
---

# Getting Started

## Requirements
You must have an OpenAI account and API key before configuring Usage Panda.

{: .warning }
Usage Panda is currently in beta and features may change without notice (although we'll do our best to avoid it!). Please use caution before deploying Usage Panda to production.

## Sign Up for Usage Panda
Usage Panda is accessed as a SaaS application from your web browser.

[Sign Up](https://app.usagepanda.com/signin){: .btn .btn-purple }

{: .note }
Usage Panda does not use passwords. Please sign up using an email address you have access to. When signing in, Usage Panda will send you a temporary sign in link to click.

## Configuring Usage Panda

To use Usage Panda, you must first configure a **connection** from the "Connections" page. Each connection is assigned a Usage Panda API key. Think of a connection as an OpenAI-compatible endpoint that enforces custom policies (which you define).

You must then make two small changes to your application environment:
1. Set `openai.api_base` to `"https://proxy.usagepanda.com"`
2. Set `openai.api_key` to a combined string containing your OpenAI API key _and_ your Usage Panda connection API key.

```python
openai.api_base = "https://proxy.usagepanda.com"
openai.api_key = "sk-abcdefg123456789//up-abcdefg123456789"
```

No other code changes are required. The next time your application makes a call to the OpenAI API, it will be routed through the Usage Panda proxy where your custom policy will be enforced.

By default, Usage Panda does not apply any additional policy controls (it simply passes the request and response through). However, you should be able to see your requests on the "Logs" page as soon as they are sent.

You can try the Python example below to test out your connection.

```python
response = openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant"},
        {"role": "user", "content": "Hello, who are you?"}
    ]
)
output = response.choices[0].message
print(output)
```

## Next Steps
Once Usage Panda is working, you can begin configuring policies on your connection.