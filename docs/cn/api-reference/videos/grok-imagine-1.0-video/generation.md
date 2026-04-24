---
title: "Grok Imagine 1.0 视频生成"
---
Source: https://docs.toapis.com/docs/cn/api-reference/videos/grok-imagine-1.0-video/generation

POST https://toapis.com/v1/videos/generations
使用 grok-imagine-1.0-video 进行异步视频生成

* 统一视频生成接口
* 异步任务模式，返回任务 ID
* 支持文生视频和图生视频

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

  * `grok-imagine-1.0-video`
</ParamField>

<ParamField type="string">
  视频内容描述，支持多语言
</ParamField>

<ParamField type="string">
  视频比例

  支持的格式：

  * `16:9` - 横屏（默认）
  * `9:16` - 竖屏
  * `1:1` - 方形
  * `3:2` - 横版
  * `2:3` - 竖版
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  取值范围：6-30（最短 6 秒，最长 30 秒）

  ⚠️ 注意：必须输入纯数字（如 `6`），不要加引号，否则会报错
</ParamField>

<ParamField type="string">
  视频质量

  支持的值：

  * `480p`（默认）
  * `720p`
</ParamField>

<ParamField type="string[]">
  参考图 URL 数组（用于图生视频）

  限制：

  * 最多 7 张
  * 仅支持公网 URL
  * 不支持 base64
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
  ```bash cURL（文生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0-video",
      "prompt": "一只狗在海边奔跑，阳光明媚，慢动作镜头",
      "aspect_ratio": "16:9",
      "duration": 6,
      "quality": "720p"
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0-video",
          "prompt": "一只狗在海边奔跑，阳光明媚，慢动作镜头",
          "aspect_ratio": "16:9",
          "duration": 6,
          "quality": "720p",
      },
  )

  print(response.status_code)
  print(response.json())
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0-video',
      prompt: '一只狗在海边奔跑，阳光明媚，慢动作镜头',
      aspect_ratio: '16:9',
      duration: 6,
      quality: '720p',
    }),
  });

  console.log(response.status);
  console.log(await response.json());
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_01JNXXXXXXXXXXXXXXXXXX",
    "object": "generation.task",
    "model": "grok-imagine-1.0-video",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
