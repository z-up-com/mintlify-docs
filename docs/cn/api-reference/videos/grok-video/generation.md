# Grok 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/grok-video/generation

POST https://toapis.com/v1/videos/generations
使用 Grok Video 模型生成高质量视频

* Grok Video 视频生成模型
* 通过 model 参数选择 `grok-video-3` 模型
* 支持文本到视频、图生视频
* 异步任务管理，通过任务 ID 查询结果

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `images` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
</Warning>

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
  视频生成模型名称

  支持的模型：

  * `grok-video-3` - Grok 视频生成模型

  示例：`"grok-video-3"`
</ParamField>

<ParamField type="string">
  视频生成的文本描述
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  支持的值：

  * `10`（默认）
  * `15`
</ParamField>

<ParamField type="string">
  视频宽高比

  支持的格式：

  * `16:9` (横屏)
  * `9:16` (竖屏)
</ParamField>

<ParamField type="string[]">
  用于图生视频的参考图像 URL 数组

  **⚠️ 仅支持 URL 格式（不再支持 base64）**

  * 公开可访问的图像 URL（http\:// 或 https\://）
  * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL
</ParamField>

<ParamField type="object">
  任务元数据

  <Expandable title="metadata 属性">
    <ParamField type="string">
      视频分辨率

      支持的值：

      * `720P`
      * `1080P`
    </ParamField>
  </Expandable>
</ParamField>

## Response

<ResponseField name="id" type="string">
  任务唯一标识符，用于查询任务状态
</ResponseField>

<ResponseField name="object" type="string">
  对象类型，固定为 `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  使用的模型名称
</ResponseField>

<ResponseField name="status" type="string">
  任务状态

  * `queued` - 排队等待处理
  * `in_progress` - 处理中
  * `completed` - 成功完成
  * `failed` - 失败
</ResponseField>

<ResponseField name="progress" type="integer">
  任务进度百分比（0-100）
</ResponseField>

<ResponseField name="created_at" type="integer">
  任务创建时间戳（Unix 时间戳）
</ResponseField>

<ResponseField name="metadata" type="object">
  任务元数据
</ResponseField>

<RequestExample>
  ```bash cURL（文生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "一只金毛犬在草地上奔跑，阳光明媚",
      "duration": 10,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "1080P"
      }
    }'
  ```

  ```bash cURL（图生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "让这只狗动起来，在草地上奔跑",
      "images": ["https://example.com/dog.jpg"],
      "duration": 10,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720P"
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "grok-video-3",
          "prompt": "一只金毛犬在草地上奔跑，阳光明媚",
          "duration": 10,
          "aspect_ratio": "16:9",
          "metadata": {
              "resolution": "1080P"
          }
      }
  )

  task = response.json()
  print(f"任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'grok-video-3',
      prompt: '一只金毛犬在草地上奔跑，阳光明媚',
      duration: 10,
      aspect_ratio: '16:9',
      metadata: {
        resolution: '1080P'
      }
    })
  });

  const task = await response.json();
  console.log(`任务 ID: ${task.id}`);
  console.log(`状态: ${task.status}`);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_abc123def456",
    "object": "generation.task",
    "model": "grok-video-3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
