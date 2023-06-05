---
layout: default
title: Switch Between OpenAI and Google PaLM APIs
description: Usage Panda can dynamically route requests between OpenAI and Google PaLM using the same OpenAI API format.
parent: Use Cases
---

# Switch Between OpenAI and Google PaLM APIs

With many new LLM APIs emerging, it can be challenging to update your application to support each new provider, given the differences in API format and supported features. Usage Panda makes it easy to switch between OpenAI and Google PalM APIs with a simple tweak to your existing codebase. Usage Panda then converts the request and response formats dynamically via its proxy.

## Pre-Requisites
Before you can take advantage of this feature, you must have a Google PaLM API key, which you can obtain from the [Google Maker Suite](https://makersuite.google.com/). You may need to join a waitlist if you don't already have access. You can test that your key is working using the following curl command:

```bash
curl \
-H 'Content-Type: application/json' \
-d '{ "prompt": { "text": "Write a story about a magic backpack"} }' \
"https://generativelanguage.googleapis.com/v1beta2/models/text-bison-001:generateText?key=YOUR_API_KEY"
```

Additionally, you must have a Usage Panda account (you can [sign up here](https://app.usagepanda.com)) and have generated a Usage Panda API key from the [API Keys](https://app.usagepanda.com/connections) page.

## Configuring Your Codebase
From here, you can adjust your codebase to instruct Usage Panda on how it should route the request, to OpenAI's API, or to Google PaLM.

```python
import openai
openai.api_base = "https://proxy.usagepanda.com"
openai.api_key = "sk-your-openai-key"
PALM_KEY="your-google-palm-key"
USAGE_PANDA_KEY="up-your-usage-panda-key"

response = openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
  ],
  headers={ # Usage Panda Auth
    "x-usagepanda-api-key": USAGE_PANDA_KEY,
    "x-usagepanda-palm-api-key": PALM_KEY
  }
)
output = response.choices[0].message
print(output)
```

In the above code, we first point the OpenAI base URL to the Usage Panda proxy. You can then set your OpenAI, PaLM, and Usage Panda API keys. The `x-usagepanda-palm-api-key` header tells Usage Panda to route your request to Google PaLM's API, even though you are using the OpenAI SDK. This allows you to dynamically switch between backend APIs, without modifying the structure of your codebase.

If you want to switch back to the OpenAI API, simply comment out the `x-usagepanda-palm-api-key` header.

## API Differences
The Google PaLM API is currently in limited preview and does not support all of the same APIs as OpenAI. As of June, 2023, PaLM supports chat and completion calls, both of which are supported by Usage Panda.

### Completions

* [OpenAI API Reference](https://platform.openai.com/docs/api-reference/completions/create)
* [Google PaLM API Reference](https://developers.generativeai.google/api/rest/generativelanguage/models/generateText)

Key Differences
* PaLM's API specifies the `model` in its URL, rather than in the request body
* PaLM only supports one model, `text-bison-001`. Usage Panda will default all models to this type.
* PaLM uses `stopSequences`, while OpenAI uses `stop`.
* PaLM uses `candidateCount`, while OpenAI uses `n`.
* PaLM uses `maxOutputTokens`, while OpenAI uses `max_tokens`.
* PaLM supports `topP` and `topK` parameters, while OpenAI supports only `top_p`.

Additionally, PaLM supports additional `safetySettings` in its APIs, which OpenAI does not support (there is a separate moderation endpoint that you can use). Usage Panda will ignore these response settings, but will return an error message if the safety filter is triggered and no response candidates are returned.

Usage Panda converts OpenAI to PaLM using the following (where `body` is the original OpenAI-formatted payload):
```
prompt: {
    text: body.prompt
},
temperature: body.temperature,
candidateCount: body.n || 1,
maxOutputTokens: body.max_tokens,
topP: body.top_p,
stopSequences: (body.stop ? (Array.isArray(body.stop) ? body.stop : [body.stop]) : null),
```

### Chat Completions

* [OpenAI API Reference](https://platform.openai.com/docs/api-reference/chat/create)
* [Google PaLM API Reference](https://developers.generativeai.google/api/rest/generativelanguage/models/generateMessage)

Key Differences
* PaLM's API specifies the `model` in its URL, rather than in the request body
* PaLM only supports one model, `chat-bison-001`. Usage Panda will default all models to this type.
* PaLM uses `candidateCount`, while OpenAI uses `n`.
* PaLM does not support `max_tokens` for chats
* PaLM supports `topP` and `topK` parameters, while OpenAI supports only `top_p`.

Additionally, PaLM supports additional `filters` in its API response, which OpenAI does not support (there is a separate moderation endpoint that you can use). Usage Panda will ignore these response settings, but will return an error message if the safety filter is triggered and no response candidates are returned.

Usage Panda converts OpenAI to PaLM using the following (where `body` is the original OpenAI-formatted payload):
```
prompt: {
    messages: body.messages.map(function(m){
        return {
            author: m.role,
            content: m.content
        }
    })
},
temperature: body.temperature,
candidateCount: body.n || 1,
topP: body.top_p
```

### Model Support
Currently, PaLM only supports `text-bison-001` (for completions) and `chat-bison-001` (for chats), so all OpenAI requests are converted to these models, regardless of OpenAI model provided in the request. In the future, as PaLM adds additional model support, Usage Panda will provide a mapping of OpenAI to PaLM models.

## Limitations
This is an experimental feature. The OpenAI APIs and PaLM APIs are not 100% compatible, so certain request parameters may be dropped.