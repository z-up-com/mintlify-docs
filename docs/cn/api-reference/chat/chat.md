---
title: "Chat Completions"
---
Source: https://docs.toapis.com/docs/cn/api-reference/chat/chat

POST https://toapis.com/v1/chat/completions
兼容 OpenAI 格式的文字对话接口，支持全部文字模型

* 兼容 OpenAI Chat Completions API 格式
* 通过 `model` 参数指定模型，所有支持的模型请参阅 [模型一览](./models)
* 支持流式输出（SSE）
* 支持多轮对话、系统提示词
* 部分模型支持视觉输入（图片）和深度思考模式

## Authorizations

<ParamField type="string">
  所有接口均需要使用 Bearer Token 进行认证

  获取 API Key：访问 [API Key 管理页面](https://toapis.com/console/token) 获取您的 API Key

  使用时在请求头中添加：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField type="string">
  模型名称，所有支持的模型请参阅 [模型一览](./models)

  示例：`"claude-sonnet-4-6"`、`"gpt-5"`、`"qwen3-max"`
</ParamField>

<ParamField type="object[]">
  对话消息列表，按时间顺序排列

  <Expandable title="messages[n]">
    <ParamField type="string">
      消息角色，可选值 `system`、`user`、`assistant`
    </ParamField>

    <ParamField type="string | object[]">
      消息内容，两种格式：

      * **字符串**：纯文本消息，适用于所有模型
      * **内容块数组**：混合文本与图片，仅支持视觉输入的模型可用

      <Expandable title="内容块数组格式（content[n]）">
        <ParamField type="string">
          内容块类型，可选值 `text` 或 `image_url`
        </ParamField>

        <ParamField type="string">
          文本内容，`type` 为 `text` 时必填
        </ParamField>

        <ParamField type="object">
          图片信息，`type` 为 `image_url` 时必填

          <Expandable title="image_url">
            <ParamField type="string">
              图片 URL 或 Base64 数据（格式：`data:image/jpeg;base64,...`）
            </ParamField>
          </Expandable>
        </ParamField>
      </Expandable>
    </ParamField>
  </Expandable>
</ParamField>

<ParamField type="boolean">
  是否启用流式输出（Server-Sent Events）

  * `true`：逐 token 流式返回
  * `false`：等待完整响应后一次性返回
</ParamField>

<ParamField type="integer">
  生成内容的最大 token 数量

  不设置时使用模型默认上限
</ParamField>

<ParamField type="number">
  采样温度，控制输出随机性

  * 范围：`0` \~ `2`
  * 值越低输出越稳定，值越高输出越随机
</ParamField>

<ParamField type="number">
  核采样概率阈值

  范围：`0` \~ `1`，建议不要同时修改 `temperature` 和 `top_p`
</ParamField>

<ParamField type="string | string[]">
  停止序列，遇到指定字符串时停止生成

  最多 4 个停止序列
</ParamField>

## Response

<ResponseField name="id" type="string">
  本次请求的唯一标识符
</ResponseField>

<ResponseField name="object" type="string">
  对象类型，固定为 `chat.completion`
</ResponseField>

<ResponseField name="created" type="integer">
  请求创建时间（Unix 时间戳）
</ResponseField>

<ResponseField name="model" type="string">
  实际使用的模型名称
</ResponseField>

<ResponseField name="choices" type="object[]">
  生成结果列表

  * `choices[].message.role`：消息角色，固定为 `assistant`
  * `choices[].message.content`：生成的文本内容
  * `choices[].finish_reason`：结束原因，`stop` / `length` / `content_filter`
  * `choices[].index`：结果索引
</ResponseField>

<ResponseField name="usage" type="object">
  本次请求的 token 消耗统计

  * `usage.prompt_tokens`：输入 token 数
  * `usage.completion_tokens`：输出 token 数
  * `usage.total_tokens`：总 token 数
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
          "content": "你好，请介绍一下你自己"
        }
      ]
    }'
  ```

  ```bash cURL（流式输出） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/chat/completions \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-5",
      "messages": [
        {
          "role": "system",
          "content": "你是一个专业的代码助手"
        },
        {
          "role": "user",
          "content": "用 Python 写一个快速排序"
        }
      ],
      "stream": true
    }'
  ```

  ```bash cURL（图片输入） theme={null}
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
              "text": "这张图片里有什么？"
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
      model="claude-sonnet-4-6",  # 替换为任意支持的模型
      messages=[
          {"role": "user", "content": "你好，请介绍一下你自己"}
      ]
  )

  print(response.choices[0].message.content)
  ```

  ```python Python（流式输出） theme={null}
  from openai import OpenAI

  client = OpenAI(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com/v1"
  )

  stream = client.chat.completions.create(
      model="gpt-5",  # 替换为任意支持的模型
      messages=[
          {"role": "system", "content": "你是一个专业的代码助手"},
          {"role": "user", "content": "用 Python 写一个快速排序"}
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
    model: "claude-sonnet-4-6", // 替换为任意支持的模型
    messages: [{ role: "user", content: "你好，请介绍一下你自己" }],
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
          "content": "你好！我是 Claude，由 Anthropic 开发的 AI 助手。我可以帮助你回答问题、分析信息、编写代码、创作内容等。有什么我可以帮你的吗？"
        },
        "finish_reason": "stop"
      }
    ],
    "usage": {
      "prompt_tokens": 15,
      "completion_tokens": 42,
      "total_tokens": 57
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
