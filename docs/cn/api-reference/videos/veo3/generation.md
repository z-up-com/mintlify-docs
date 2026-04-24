# Veo3 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/veo3/generation

POST https://toapis.com/v1/videos/generations
使用 Google Veo3 模型生成高质量视频，支持异步任务管理

* 异步处理模式，返回任务ID用于后续查询
* 支持文本转视频、图生视频等多种生成模式
* 生成的视频链接，有效期为24小时，请尽快保存

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `image_urls` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
</Warning>

## Authorizations

<ParamField type="string">
  所有接口均需要使用Bearer Token进行认证

  获取 API Key：访问 [API Key 管理页面](https://toapis.com/console/token) 获取您的 API Key

  使用时在请求头中添加：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField type="string">
  视频生成模型名称

  可用模型：

  * `veo3.1-fast` - 快速生成模型，适用于快速预览和迭代
  * `veo3.1-quality` - 高质量生成模型，适用于最终制作
  * `veo3.1-lite` - 轻量生成模型，适用于更低成本的快速生成

  Example: `"veo3.1-fast"`
</ParamField>

<ParamField type="string">
  视频生成的文本描述
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  固定值：`8`（VEO3 仅支持 8 秒时长）
</ParamField>

<ParamField type="string">
  视频分辨率

  支持的格式：

  * `16:9` (横屏)
  * `9:16` (竖屏)
</ParamField>

<ParamField type="string[]">
  用于图生视频的参考图像 URL 数组

  **⚠️ 仅支持 URL 格式（不再支持 base64）**

  * 公开可访问的图像 URL（http\:// 或 https\://）
  * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

  **限制：**

  * 最大文件大小：10MB
  * 支持格式：.jpeg、.jpg、.png、.webp
</ParamField>

<ParamField type="object">
  VEO3 特有扩展参数

  <Expandable title="显示 metadata 字段">
    <ParamField type="string">
      视频生成类型

      支持的类型：

      * `frame` - 帧转视频（FL模式）
      * `reference` - 参考图生视频

      如果不传此参数，默认根据图片数量自动判断：2张为帧转视频，3张为参考图生视频

      **注意：`veo3.1-quality` 模型不支持 `reference` 参考图模式**
    </ParamField>

    <ParamField type="string">
      视频分辨率

      支持的值：

      * `720p`（默认）
      * `1080p`
      * `4k`
    </ParamField>

    <ParamField type="boolean">
      启用 GIF 输出格式。默认：`false`

      注意：GIF 和 1080p、4k 分辨率不能同时使用
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
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "veo3.1-fast",
      "prompt": "海豚在碧蓝海洋中跳跃",
      "duration": 8,
      "aspect_ratio": "16:9",
      "image_urls": ["https://example.com/start-frame.jpg", "https://example.com/end-frame.jpg"],
      "metadata": {
        "generation_type": "frame",
        "resolution": "1080p",
        "enable_gif": false
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
          "model": "veo3.1-fast",
          "prompt": "海豚在碧蓝海洋中跳跃",
          "duration": 8,
          "aspect_ratio": "16:9",
          "image_urls": ["https://example.com/start-frame.jpg", "https://example.com/end-frame.jpg"],
          "metadata": {
              "generation_type": "frame",
              "resolution": "1080p",
              "enable_gif": False
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
      model: 'veo3.1-fast',
      prompt: '海豚在碧蓝海洋中跳跃',
      duration: 8,
      aspect_ratio: '16:9',
      image_urls: ['https://example.com/start-frame.jpg', 'https://example.com/end-frame.jpg'],
      metadata: {
        generation_type: 'frame',
        resolution: '1080p',
        enable_gif: false
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
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "veo3.1-fast",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "aspect_ratio": "16:9"
    }
  }
  ```
</ResponseExample>
