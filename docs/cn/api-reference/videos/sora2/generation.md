---
title: "Sora2 视频生成"
---
Source: https://docs.toapis.com/docs/cn/api-reference/videos/sora2/generation

POST https://toapis.com/v1/videos/generations
使用 OpenAI Sora2 模型生成高质量视频

* OpenAI Sora2 视频生成模型
* 通过 model 参数选择 `sora-2` 或 `sora-2-pro` 模型
* 支持文本到视频、图生视频、角色引用
* 异步任务管理，通过任务 ID 查询结果

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `image_urls` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
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

  * `sora-2` - 标准版
  * `sora-2-pro` - 专业版，支持更长时长
  * `sora-2-vip` - VIP版，更高优先级

  示例：`"sora-2"` 或 `"sora-2-pro"`
</ParamField>

<ParamField type="string">
  视频生成的文本描述

  **💡 @角色引用功能：**

  您可以在提示词中使用 `@username` 格式引用之前创建的角色，将其包含在生成的视频中。

  * 角色的 `username` 可通过 [查询角色 API](/cn/api-reference/videos/sora2/get-personas) 获取
  * 支持同时引用多个角色

  示例：`"一只猫和一只狗一起开车 @duksvfkf.cruisingki @zdqwahgj.baronbarki"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  * `sora-2`：支持 10 或 15 秒
  * `sora-2-pro`：支持 15 秒（高清）或 25 秒

  示例：`10`
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

<ParamField type="boolean">
  是否生成视频缩略图

  * `true` - 生成视频缩略图用于预览和展示

  如果不需要缩略图，可以省略此参数
</ParamField>

<ParamField type="integer">
  生成不同变体的数量

  取值范围：1-4

  默认值：`1`
</ParamField>

<ParamField type="boolean">
  是否为生成的视频添加水印

  * `false` - 无水印
  * `true` - 为视频添加 Sora 官方水印

  默认值：`false`
</ParamField>

<ParamField type="boolean">
  是否生成高清视频

  * `true` - 生成高清视频
  * `false` - 标准清晰度

  **⚠️ 注意：** 需使用 `sora-2-pro` 模型且时长不可为 25 秒

  默认值：`false`
</ParamField>

<ParamField type="boolean">
  是否启用隐私模式

  * `true` - 视频不会被发布，且无法被 remix（重新编辑）
  * `false` - 视频可以被发布和 remix

  默认值：`false`
</ParamField>

<ParamField type="string">
  视频风格

  支持的值：

  * `thanksgiving` - 感恩节风格
  * `comic` - 漫画风格
  * `news` - 新闻风格
  * `selfie` - 自拍风格
  * `nostalgic` - 怀旧/复古风格
  * `anime` - 动漫风格

  示例：`"anime"`
</ParamField>

<ParamField type="boolean">
  是否使用故事板功能，更精细地控制视频生成细节

  * `true` - 启用故事板功能
  * `false` - 不使用故事板

  示例：`true`
</ParamField>

<ParamField type="string">
  用于角色提取的参考视频 URL

  用于在生成的视频中重用参考视频中的角色

  示例：`"https://filesystem.site/cdn/20251030/javYrU4etHVFDqg8by7mViTWHlMOZy.mp4"`

  **⚠️ 注意：** 使用此参数时，生成的视频时长会减少 1 秒
</ParamField>

<ParamField type="string">
  角色出现的时间戳

  指定参考视频中角色出现的时间范围，仅支持 2 秒片段

  格式：`"起始秒,结束秒"`

  示例：`"1,3"` 表示参考视频中第 1 秒到第 3 秒的角色

  **⚠️ 注意：** 使用此参数时，生成的视频时长会减少 1 秒
</ParamField>

<ParamField type="boolean">
  创建视频完成后，自动根据生成的视频创建角色

  * `true` - 自动创建角色
  * `false` - 不创建角色

  默认值：`false`
</ParamField>

<ParamField type="string">
  根据已经生成的任务 ID 来创建角色

  可以引用之前生成的视频任务 ID，从该视频中提取角色

  示例：`"video_01K8SGYNNNVBQTXNR4MM964S7K"`
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
      "model": "sora-2",
      "prompt": "一只金毛犬在草地上奔跑，阳光明媚",
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL（图生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "让这只狗动起来，在草地上奔跑",
      "image_urls": ["https://example.com/dog.jpg"],
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL（带风格参数） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "一只可爱的卡通大象",
      "aspect_ratio": "16:9",
      "duration": 10,
      "metadata": {
        "style": "anime",
        "watermark": false
      }
    }'
  ```

  ```bash cURL（使用角色引用） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "一只猫和一只狗一起开车 @duksvfkf.cruisingki @zdqwahgj.baronbarki",
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```python Python（带 metadata 参数） theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "prompt": "一只金毛犬在草地上奔跑，阳光明媚",
          "duration": 10,
          "aspect_ratio": "16:9",
          "metadata": {
              "style": "anime",
              "watermark": False,
              "n": 2
          }
      }
  )

  task = response.json()
  print(f"任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  ```

  ```javascript JavaScript（带 metadata 参数） theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      prompt: '一只金毛犬在草地上奔跑，阳光明媚',
      duration: 10,
      aspect_ratio: '16:9',
      metadata: {
        style: 'anime',
        watermark: false,
        n: 2
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
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "720x720"
    }
  }
  ```
</ResponseExample>
