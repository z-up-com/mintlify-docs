# Anthropic Messages API
Source: https://docs.toapis.com/docs/cn/api-reference/chat/messages

POST https://toapis.com/v1/messages
使用 Anthropic Messages API 原生格式与 Claude 系列模型对话

* 兼容 Anthropic Messages API 原生格式
* 支持 Anthropic 官方 SDK（Python / JavaScript）直接对接，仅需修改 `base_url`
* 支持流式输出（SSE）
* 支持多轮对话、系统提示词、视觉输入、工具调用

<Note>
  如果你已经在使用 OpenAI SDK，建议使用 [OpenAI 格式接口](/docs/cn/api-reference/chat/chat)。
  如果你在使用 Anthropic SDK 或 Claude Code，推荐使用本接口。
</Note>

## Authorizations

<ParamField type="string">
  Bearer Token 认证，适用于直接 HTTP 调用

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

<ParamField type="string">
  API Key 认证，与 Anthropic SDK 兼容

  ```
  x-api-key: YOUR_API_KEY
  ```
</ParamField>

<ParamField type="string">
  Anthropic API 版本号，使用 Anthropic SDK 时自动传入

  推荐值：`2023-06-01`
</ParamField>

## Body

<ParamField type="string">
  模型名称

  支持所有 Claude 系列模型，例如：

  * `claude-opus-4-6`
  * `claude-sonnet-4-6`
  * `claude-haiku-4-5`
</ParamField>

<ParamField type="object[]">
  对话消息列表，按时间顺序排列。只支持 `user` 和 `assistant` 角色，系统提示词请使用顶层 `system` 字段

  <Expandable title="messages[n]">
    <ParamField type="string">
      消息角色，可选值：`user`、`assistant`
    </ParamField>

    <ParamField type="string | object[]">
      消息内容，字符串或内容块数组

      <Expandable title="内容块（content block）">
        <ParamField type="string">
          内容类型：`text`（文本）或 `image`（图片）
        </ParamField>

        <ParamField type="string">
          文本内容，`type` 为 `text` 时必填
        </ParamField>

        <ParamField type="object">
          图片来源，`type` 为 `image` 时必填

          <Expandable title="source">
            <ParamField type="string">
              图片来源类型：`base64` 或 `url`
            </ParamField>

            <ParamField type="string">
              MIME 类型，`type` 为 `base64` 时必填，例如 `image/jpeg`、`image/png`
            </ParamField>

            <ParamField type="string">
              Base64 编码的图片数据，`type` 为 `base64` 时必填
            </ParamField>

            <ParamField type="string">
              图片 URL，`type` 为 `url` 时必填
            </ParamField>
          </Expandable>
        </ParamField>
      </Expandable>
    </ParamField>
  </Expandable>
</ParamField>

<ParamField type="integer">
  生成内容的最大 token 数量

  * Claude Sonnet 4-6 最大支持 `64000`
  * Claude Opus 4-6 最大支持 `32000`
</ParamField>

<ParamField type="string | object[]">
  系统提示词，在顶层设置（不放在 `messages` 中）

  支持字符串或内容块数组格式
</ParamField>

<ParamField type="boolean">
  是否启用流式输出（Server-Sent Events）

  * `true`：逐 token 流式返回，事件格式遵循 Anthropic SSE 规范
  * `false`：等待完整响应后一次性返回
</ParamField>

<ParamField type="number">
  采样温度，控制输出随机性

  范围：`0` \~ `1`
</ParamField>

<ParamField type="number">
  核采样概率阈值

  范围：`0` \~ `1`，建议不要同时设置 `temperature` 和 `top_p`
</ParamField>

<ParamField type="string[]">
  停止序列，遇到指定字符串时停止生成
</ParamField>

## Response

<ResponseField name="id" type="string">
  本次请求的唯一标识符，格式为 `msg_*`
</ResponseField>

<ResponseField name="type" type="string">
  对象类型，固定为 `message`
</ResponseField>

<ResponseField name="role" type="string">
  响应角色，固定为 `assistant`
</ResponseField>

<ResponseField name="content" type="object[]">
  生成的内容块列表

  * `content[].type`：内容类型，通常为 `text`
  * `content[].text`：生成的文本内容
</ResponseField>

<ResponseField name="model" type="string">
  实际使用的模型名称
</ResponseField>

<ResponseField name="stop_reason" type="string">
  停止原因

  * `end_turn`：模型正常结束
  * `max_tokens`：达到 `max_tokens` 限制
  * `stop_sequence`：触发了停止序列
</ResponseField>

<ResponseField name="usage" type="object">
  本次请求的 token 消耗统计

  * `usage.input_tokens`：输入 token 数
  * `usage.output_tokens`：输出 token 数
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
          "content": "你好，请介绍一下你自己"
        }
      ]
    }'
  ```

  ```bash cURL（系统提示词） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "system": "你是一个专业的代码助手，擅长 Python 和 Go 语言。",
      "messages": [
        {
          "role": "user",
          "content": "用 Python 写一个快速排序"
        }
      ]
    }'
  ```

  ```bash cURL（流式输出） theme={null}
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
          "content": "解释一下什么是递归"
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
          {"role": "user", "content": "你好，请介绍一下你自己"}
      ]
  )

  print(message.content[0].text)
  ```

  ```python Python（带系统提示词） theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  message = client.messages.create(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      system="你是一个专业的代码助手，擅长 Python 和 Go 语言。",
      messages=[
          {"role": "user", "content": "用 Python 写一个快速排序"}
      ]
  )

  print(message.content[0].text)
  ```

  ```python Python（流式输出） theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  with client.messages.stream(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      messages=[
          {"role": "user", "content": "解释一下什么是递归"}
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
    messages: [{ role: "user", content: "你好，请介绍一下你自己" }],
  });

  console.log(message.content[0].text);
  ```

  ```javascript JavaScript（流式输出） theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic({
    apiKey: "your-ToAPIs-key",
    baseURL: "https://toapis.com",
  });

  const stream = await client.messages.stream({
    model: "claude-sonnet-4-6",
    max_tokens: 1024,
    messages: [{ role: "user", content: "解释一下什么是递归" }],
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
        "text": "你好！我是 Claude，由 Anthropic 开发的 AI 助手。我可以帮助你回答问题、分析信息、编写代码、创作内容等。有什么我可以帮你的吗？"
      }
    ],
    "model": "claude-sonnet-4-6",
    "stop_reason": "end_turn",
    "usage": {
      "input_tokens": 12,
      "output_tokens": 38
    }
  }
  ```

  ```json 200（流式，SSE 事件示例） theme={null}
  data: {"type":"message_start","message":{"id":"msg_01abc","type":"message","role":"assistant","content":[],"model":"claude-sonnet-4-6","stop_reason":null,"usage":{"input_tokens":12,"output_tokens":0}}}

  data: {"type":"content_block_start","index":0,"content_block":{"type":"text","text":""}}

  data: {"type":"content_block_delta","index":0,"delta":{"type":"text_delta","text":"你好"}}

  data: {"type":"content_block_delta","index":0,"delta":{"type":"text_delta","text":"！我是"}}

  data: {"type":"content_block_stop","index":0}

  data: {"type":"message_delta","delta":{"stop_reason":"end_turn"},"usage":{"output_tokens":38}}

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
