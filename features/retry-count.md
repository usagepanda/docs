---
layout: default
title: Automatic Request Retries
description: Usage Panda can automatically detect OpenAI API failures and retry requests without any changes to your code.
parent: Features
nav_order: 10
---

# Automatic Request Retries

## Background
The upstream LLM APIs, such as OpenAI's API, can be unstable, rate-limited, or otherwise return occasional errors. A good production practice is to retry failed requests in case the upstream error was transient. Usage Panda can automatically retry requests on your behalf, without having to write retry logic into your application code.

## Enabling the Setting
To enable automatic retries:

1. Navigate to the [API Keys](https://app.usagepanda.com/connections) page
2. Click the gear (settings) icon on the API key you wish to modify
3. Scroll down to the "Retry Count" setting and enter a number between 0 (disabled) and 5
4. Click "Save"

## Setting via Headers
You can optionally override this setting on a per-request basis by passing the `x-usagepanda-retry-count` header, like so:

```python
response = openai.Completion.create(
  model="text-davinci-003",
  prompt="Hello there",
  headers={ # Usage Panda Auth
    "x-usagepanda-api-key": USAGE_PANDA_KEY,
    "x-usagepanda-retry-count": 3
  }
)
output = response.choices[0].text
```
