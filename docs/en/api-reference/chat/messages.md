> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Anthropic Messages API

> Chat with Claude models using the native Anthropic Messages API format

* Native Anthropic Messages API format
* Drop-in compatible with the official Anthropic SDK (Python / JavaScript) — just change `base_url`
* Supports streaming (SSE)
* Supports multi-turn conversations, system prompts, vision input, and tool use

<Note>
  If you are already using the OpenAI SDK, use the [OpenAI-compatible endpoint](/docs/en/api-reference/chat/chat) instead.
  If you are using the Anthropic SDK or Claude Code, this endpoint is recommended.
</Note>

## Authorizations

<ParamField header="Authorization" type="string">
  Bearer token authentication for direct HTTP calls

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

<ParamField header="x-api-key" type="string">
  API key authentication, compatible with the Anthropic SDK

  ```
  x-api-key: YOUR_API_KEY
  ```
</ParamField>

<ParamField header="anthropic-version" type="string" default="2023-06-01">
  Anthropic API version. The Anthropic SDK sets this automatically.

  Recommended: `2023-06-01`
</ParamField>

## Body

<ParamField body="model" type="string" required>
  Model name

  All Claude models are supported, for example:

  * `claude-opus-4-6`
  * `claude-sonnet-4-6`
  * `claude-haiku-4-5`
</ParamField>

<ParamField body="messages" type="object[]" required>
  Conversation messages in chronological order. Only `user` and `assistant` roles are allowed here — use the top-level `system` field for system prompts.

  <Expandable title="messages[n]">
    <ParamField body="role" type="string" required>
      Message role: `user` or `assistant`
    </ParamField>

    <ParamField body="content" type="string | object[]" required>
      Message content as a string or an array of content blocks

      <Expandable title="Content block">
        <ParamField body="type" type="string" required>
          Content type: `text` or `image`
        </ParamField>

        <ParamField body="text" type="string">
          Text content. Required when `type` is `text`.
        </ParamField>

        <ParamField body="source" type="object">
          Image source. Required when `type` is `image`.

          <Expandable title="source">
            <ParamField body="type" type="string" required>
              Source type: `base64` or `url`
            </ParamField>

            <ParamField body="media_type" type="string">
              MIME type, required for `base64`. E.g. `image/jpeg`, `image/png`
            </ParamField>

            <ParamField body="data" type="string">
              Base64-encoded image data. Required when `type` is `base64`.
            </ParamField>

            <ParamField body="url" type="string">
              Image URL. Required when `type` is `url`.
            </ParamField>
          </Expandable>
        </ParamField>
      </Expandable>
    </ParamField>
  </Expandable>
</ParamField>

<ParamField body="max_tokens" type="integer" required>
  Maximum number of tokens to generate

  * Claude Sonnet 4-6: up to `64000`
  * Claude Opus 4-6: up to `32000`
</ParamField>

<ParamField body="system" type="string | object[]">
  System prompt, set at the top level (not inside `messages`)

  Accepts a plain string or an array of content blocks.
</ParamField>

<ParamField body="stream" type="boolean" default={false}>
  Enable streaming output (Server-Sent Events)

  * `true`: tokens streamed incrementally following the Anthropic SSE event format
  * `false`: full response returned at once
</ParamField>

<ParamField body="temperature" type="number" default={1}>
  Sampling temperature controlling output randomness

  Range: `0` – `1`
</ParamField>

<ParamField body="top_p" type="number">
  Nucleus sampling threshold

  Range: `0` – `1`. Avoid setting both `temperature` and `top_p` simultaneously.
</ParamField>

<ParamField body="stop_sequences" type="string[]">
  Stop sequences — generation stops when any of these strings is produced
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique identifier for the request, prefixed with `msg_`
</ResponseField>

<ResponseField name="type" type="string">
  Object type, always `message`
</ResponseField>

<ResponseField name="role" type="string">
  Response role, always `assistant`
</ResponseField>

<ResponseField name="content" type="object[]">
  List of generated content blocks

  * `content[].type`: content type, typically `text`
  * `content[].text`: generated text
</ResponseField>

<ResponseField name="model" type="string">
  The model that handled the request
</ResponseField>

<ResponseField name="stop_reason" type="string">
  Reason generation stopped

  * `end_turn`: model finished naturally
  * `max_tokens`: `max_tokens` limit reached
  * `stop_sequence`: a stop sequence was triggered
</ResponseField>

<ResponseField name="usage" type="object">
  Token usage for this request

  * `usage.input_tokens`: input token count
  * `usage.output_tokens`: output token count
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "messages": [
        {
          "role": "user",
          "content": "Hello, tell me about yourself"
        }
      ]
    }'
  ```

  ```bash cURL (system prompt) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "system": "You are a professional coding assistant specializing in Python and Go.",
      "messages": [
        {
          "role": "user",
          "content": "Write a quicksort in Python"
        }
      ]
    }'
  ```

  ```bash cURL (streaming) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "stream": true,
      "messages": [
        {
          "role": "user",
          "content": "Explain what recursion is"
        }
      ]
    }'
  ```

  ```python Python (Anthropic SDK) theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  message = client.messages.create(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      messages=[
          {"role": "user", "content": "Hello, tell me about yourself"}
      ]
  )

  print(message.content[0].text)
  ```

  ```python Python (with system prompt) theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  message = client.messages.create(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      system="You are a professional coding assistant specializing in Python and Go.",
      messages=[
          {"role": "user", "content": "Write a quicksort in Python"}
      ]
  )

  print(message.content[0].text)
  ```

  ```python Python (streaming) theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  with client.messages.stream(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      messages=[
          {"role": "user", "content": "Explain what recursion is"}
      ]
  ) as stream:
      for text in stream.text_stream:
          print(text, end="", flush=True)
  ```

  ```javascript JavaScript (Anthropic SDK) theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic({
    apiKey: "your-ToAPIs-key",
    baseURL: "https://toapis.com",
  });

  const message = await client.messages.create({
    model: "claude-sonnet-4-6",
    max_tokens: 1024,
    messages: [{ role: "user", content: "Hello, tell me about yourself" }],
  });

  console.log(message.content[0].text);
  ```

  ```javascript JavaScript (streaming) theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic({
    apiKey: "your-ToAPIs-key",
    baseURL: "https://toapis.com",
  });

  const stream = await client.messages.stream({
    model: "claude-sonnet-4-6",
    max_tokens: 1024,
    messages: [{ role: "user", content: "Explain what recursion is" }],
  });

  for await (const chunk of stream) {
    if (
      chunk.type === "content_block_delta" &&
      chunk.delta.type === "text_delta"
    ) {
      process.stdout.write(chunk.delta.text);
    }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "msg_01XFDUDYJgAACzvnptvVoYEL",
    "type": "message",
    "role": "assistant",
    "content": [
      {
        "type": "text",
        "text": "Hello! I'm Claude, an AI assistant made by Anthropic. I can help with questions, analysis, coding, writing, and more. How can I help you today?"
      }
    ],
    "model": "claude-sonnet-4-6",
    "stop_reason": "end_turn",
    "usage": {
      "input_tokens": 12,
      "output_tokens": 35
    }
  }
  ```

  ```json 200 (streaming SSE) theme={null}
  data: {"type":"message_start","message":{"id":"msg_01abc","type":"message","role":"assistant","content":[],"model":"claude-sonnet-4-6","stop_reason":null,"usage":{"input_tokens":12,"output_tokens":0}}}

  data: {"type":"content_block_start","index":0,"content_block":{"type":"text","text":""}}

  data: {"type":"content_block_delta","index":0,"delta":{"type":"text_delta","text":"Hello"}}

  data: {"type":"content_block_delta","index":0,"delta":{"type":"text_delta","text":"! I'm Claude"}}

  data: {"type":"content_block_stop","index":0}

  data: {"type":"message_delta","delta":{"stop_reason":"end_turn"},"usage":{"output_tokens":35}}

  data: {"type":"message_stop"}
  ```

  ```json 400 theme={null}
  {
    "type": "error",
    "error": {
      "type": "invalid_request_error",
      "message": "messages: field required"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "type": "error",
    "error": {
      "type": "authentication_error",
      "message": "Invalid API key"
    }
  }
  ```

  ```json 429 theme={null}
  {
    "type": "error",
    "error": {
      "type": "rate_limit_error",
      "message": "Rate limit exceeded"
    }
  }
  ```
</ResponseExample>
