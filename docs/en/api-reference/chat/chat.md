---
title: "Chat Completions"
description: "OpenAI-compatible chat completions endpoint supporting text and multimodal message content."
openapi: "/api-reference/z-up_openapi.json POST /chat/completions"
---

# Chat Completions

> OpenAI-compatible chat completions endpoint supporting all text models

- OpenAI-compatible Chat Completions API
- Specify the model via the `model` parameter — see [Model List](./models) for all supported models
- Supports streaming output (SSE)
- Supports multi-turn conversation and system prompts
- Select models support vision input (images) and extended thinking

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

Get your API Key from the [API Key Management Page](https://toapis.com/console/token)

Add to request header:

```
Authorization: Bearer YOUR_API_KEY
```

</ParamField>

## Body

<ParamField body="model" type="string" required>
  Model name — see [Model List](./models) for all supported models

Examples: `"claude-sonnet-4-6"`, `"gpt-5"`, `"qwen3-max"`
</ParamField>

<ParamField body="messages" type="object[]" required>
  List of conversation messages in chronological order

  <Expandable title="messages[n]">
    <ParamField body="role" type="string" required>
      Message role — `system`, `user`, or `assistant`
    </ParamField>

    <ParamField body="content" type="string | object[]" required>
      Message content in one of two formats:

      * **String**: plain text, supported by all models
      * **Content block array**: mixed text and images, only supported by vision-capable models

      <Expandable title="Content block array format (content[n])">
        <ParamField body="type" type="string" required>
          Block type — `text` or `image_url`
        </ParamField>

        <ParamField body="text" type="string">
          Text content, required when `type` is `text`
        </ParamField>

        <ParamField body="image_url" type="object">
          Image details, required when `type` is `image_url`

          <Expandable title="image_url">
            <ParamField body="url" type="string" required>
              Image URL or Base64 data URI (format: `data:image/jpeg;base64,...`)
            </ParamField>
          </Expandable>
        </ParamField>
      </Expandable>
    </ParamField>

  </Expandable>
</ParamField>

<ParamField body="stream" type="boolean" default={false}>
  Whether to enable streaming output (Server-Sent Events)

- `true`: Stream tokens incrementally
- `false`: Return the complete response at once

</ParamField>

<ParamField body="max_tokens" type="integer">
  Maximum number of tokens to generate

Uses the model's default limit when not set
</ParamField>

<ParamField body="temperature" type="number" default={1}>
  Sampling temperature, controls output randomness

- Range: `0` to `2`
- Lower values produce more deterministic output; higher values produce more varied output

</ParamField>

<ParamField body="top_p" type="number" default={1}>
  Nucleus sampling probability threshold

Range: `0` to `1`. It is not recommended to modify both `temperature` and `top_p` at the same time
</ParamField>

<ParamField body="stop" type="string | string[]">
  Stop sequences — generation stops when these strings are encountered

Maximum 4 stop sequences
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique identifier for this request
</ResponseField>

<ResponseField name="object" type="string">
  Object type, always `chat.completion`
</ResponseField>

<ResponseField name="created" type="integer">
  Request creation timestamp (Unix)
</ResponseField>

<ResponseField name="model" type="string">
  The model that was actually used
</ResponseField>

<ResponseField name="choices" type="object[]">
  List of generated results

- `choices[].message.role`: Message role, always `assistant`
- `choices[].message.content`: Generated text content
- `choices[].finish_reason`: Stop reason — `stop` / `length` / `content_filter`
- `choices[].index`: Result index

</ResponseField>

<ResponseField name="usage" type="object">
  Token usage statistics for this request

- `usage.prompt_tokens`: Input token count
- `usage.completion_tokens`: Output token count
- `usage.total_tokens`: Total token count

</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/chat/completions \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "messages": [
        {
          "role": "user",
          "content": "Hello, please introduce yourself"
        }
      ]
    }'
  ```

```bash cURL (Streaming) theme={null}
curl --request POST \
  --url https://toapis.com/v1/chat/completions \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "model": "gpt-5",
    "messages": [
      {
        "role": "system",
        "content": "You are a professional code assistant"
      },
      {
        "role": "user",
        "content": "Write a quicksort in Python"
      }
    ],
    "stream": true
  }'
```

```bash cURL (Vision Input) theme={null}
curl --request POST \
  --url https://toapis.com/v1/chat/completions \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "model": "claude-sonnet-4-6",
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "text",
            "text": "What is in this image?"
          },
          {
            "type": "image_url",
            "image_url": {
              "url": "https://www.gstatic.com/webp/gallery/1.jpg"
            }
          }
        ]
      }
    ],
    "max_tokens": 300
  }'
```

```python Python (openai SDK) theme={null}
from openai import OpenAI

client = OpenAI(
    api_key="your-ToAPIs-key",
    base_url="https://toapis.com/v1"
)

response = client.chat.completions.create(
    model="claude-sonnet-4-6",  # replace with any supported model
    messages=[
        {"role": "user", "content": "Hello, please introduce yourself"}
    ]
)

print(response.choices[0].message.content)
```

```python Python (Streaming) theme={null}
from openai import OpenAI

client = OpenAI(
    api_key="your-ToAPIs-key",
    base_url="https://toapis.com/v1"
)

stream = client.chat.completions.create(
    model="gpt-5",  # replace with any supported model
    messages=[
        {"role": "system", "content": "You are a professional code assistant"},
        {"role": "user", "content": "Write a quicksort in Python"}
    ],
    stream=True
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
```

```javascript JavaScript (openai SDK) theme={null}
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "your-ToAPIs-key",
  baseURL: "https://toapis.com/v1",
});

const response = await client.chat.completions.create({
  model: "claude-sonnet-4-6", // replace with any supported model
  messages: [{ role: "user", content: "Hello, please introduce yourself" }],
});

console.log(response.choices[0].message.content);
```

</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "chatcmpl-abc123",
    "object": "chat.completion",
    "created": 1703884800,
    "model": "claude-sonnet-4-6",
    "choices": [
      {
        "index": 0,
        "message": {
          "role": "assistant",
          "content": "Hello! I'm Claude, an AI assistant made by Anthropic. I can help you answer questions, analyze information, write code, create content, and much more. How can I help you today?"
        },
        "finish_reason": "stop"
      }
    ],
    "usage": {
      "prompt_tokens": 13,
      "completion_tokens": 38,
      "total_tokens": 51
    }
  }
  ```

```json 400 theme={null}
{
  "error": {
    "code": "invalid_request_error",
    "message": "The 'messages' field is required.",
    "param": "messages",
    "type": "invalid_request_error"
  }
}
```

```json 401 theme={null}
{
  "error": {
    "code": "unauthorized",
    "message": "Invalid API key",
    "type": "authentication_error"
  }
}
```

</ResponseExample>
