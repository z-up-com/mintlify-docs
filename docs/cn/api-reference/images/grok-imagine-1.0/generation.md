---
title: "Grok Imagine 1.0 图像生成"
---
Source: https://docs.toapis.com/docs/cn/api-reference/images/grok-imagine-1.0/generation

POST https://toapis.com/v1/images/generations
使用 grok-imagine-1.0 进行异步图像生成

* 统一图像生成接口
* 异步任务模式，返回任务 ID
* 通过 `model` 指定为 `grok-imagine-1.0`

## Authorizations

<ParamField type="string">
  所有接口均使用 Bearer Token 认证

  获取 API Key：访问 [API Key 管理页面](https://toapis.com/console/token)

  请求头示例：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField type="string">
  模型名称，固定使用：

  * `grok-imagine-1.0`
</ParamField>

<ParamField type="string">
  图像生成提示词，支持多语言
</ParamField>

<ParamField type="string">
  图像生成尺寸

  支持的格式：

  * `1:1` - 正方形（默认）
  * `16:9` - 横版宽屏
  * `9:16` - 竖版长屏
  * `3:2` - 横版
  * `2:3` - 竖版
</ParamField>

<ParamField type="integer">
  生成图像数量

  取值范围：1-10（最少 1 张，最多 10 张）

  ⚠️ 注意：必须输入纯数字（如 `1`），不要加引号，否则会报错
</ParamField>

## Response

<ResponseField name="id" type="string">
  任务唯一 ID
</ResponseField>

<ResponseField name="object" type="string">
  固定为 `generation.task`
</ResponseField>

<ResponseField name="status" type="string">
  任务状态：`queued` / `in_progress` / `completed` / `failed`
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0",
      "prompt": "一只橘猫坐在阳光窗台上，油画风格",
      "size": "1:1",
      "n": 1
    }'
  ```

  ```python Python theme={null}
  import requests

  resp = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0",
          "prompt": "一只橘猫坐在阳光窗台上，油画风格",
          "size": "1:1",
          "n": 1,
      },
  )

  print(resp.status_code)
  print(resp.json())
  ```

  ```javascript JavaScript theme={null}
  const resp = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0',
      prompt: '一只橘猫坐在阳光窗台上，油画风格',
      size: '1:1',
      n: 1,
    }),
  });

  console.log(resp.status);
  console.log(await resp.json());
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_01JNXXXXXXXXXXXXXXXXXX",
    "object": "generation.task",
    "model": "grok-imagine-1.0",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
