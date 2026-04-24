# Doubao SeeDance 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/doubao-seedance/generation

POST https://toapis.com/v1/videos/generations
使用字节跳动豆包 Doubao SeeDance 模型生成视频

* 字节跳动豆包视频生成模型
* 通过 model 参数选择 `doubao-seedance-1-0-pro-fast` 或 `doubao-seedance-1-0-pro-quality` 模型
* 支持文本到视频生成
* 支持首帧/尾帧/参考图控制
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

  可用模型：

  * `doubao-seedance-1-0-pro-fast` - 快速版（40-90 秒生成）
  * `doubao-seedance-1-0-pro-quality` - 高质量版（90-300 秒生成）
</ParamField>

<ParamField type="string">
  视频内容描述

  详细描述场景、动作、风格以获得更好的生成效果

  示例：`"海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  支持范围：`2` \~ `12` 秒

  默认：`5`
</ParamField>

<ParamField type="string">
  视频宽高比

  可选项：

  * `16:9` - 横屏
  * `9:16` - 竖屏
  * `1:1` - 方形
  * `4:3` - 传统比例
  * `3:4` - 竖向传统比例
  * `21:9` - 超宽屏

  默认：`16:9`
</ParamField>

<ParamField type="string">
  视频分辨率

  可选项：

  * `480p` - 标清
  * `720p` - 高清
  * `1080p` - 全高清

  默认：`720p`

  **1080p 限制**：使用参考图（`image_with_roles` 中 `role: reference`）时，不支持 1080p 分辨率
</ParamField>

## 分辨率与宽高比组合

| 分辨率   | 支持的宽高比                          | 备注     |
| ----- | ------------------------------- | ------ |
| 480p  | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | 全部支持   |
| 720p  | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | 全部支持   |
| 1080p | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | 不支持参考图 |

<ParamField type="string[]">
  首帧图像 URL 数组，用于图生视频

  **⚠️ 仅支持 URL 格式（不再支持 base64）**

  * 公开可访问的图像 URL（http\:// 或 https\://）
  * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

  示例：`["https://example.com/cat.png"]`

  `image_urls` 和 `image_with_roles` 不能同时使用
</ParamField>

<ParamField type="array">
  带角色的图像数组，用于更精确的控制

  <Expandable title="字段说明">
    <ParamField type="string">
      图像 URL 地址
    </ParamField>

    <ParamField type="string">
      图像角色

      可选项：

      * `first_frame` - 首帧图，作为视频起始画面（仅支持一张）
      * `last_frame` - 尾帧图，作为视频结束画面（**仅 quality 版本支持**，仅支持一张）
      * `reference` - 参考图，用于指导视频风格（仅支持一张）
    </ParamField>
  </Expandable>

  示例：

  ```json theme={null}
  [
    {"url": "https://example.com/start.png", "role": "first_frame"},
    {"url": "https://example.com/end.png", "role": "last_frame"}
  ]
  ```

  <Warning>
    * `image_urls` 和 `image_with_roles` 不能同时使用
    * 每种角色的图片仅支持上传一张
    * `last_frame`（尾帧图）仅 `doubao-seedance-1-0-pro-quality` 版本支持，fast 版本不支持首尾帧同时使用
  </Warning>
</ParamField>

<ParamField type="object">
  扩展参数（可选）

  <Expandable title="字段说明">
    <ParamField type="integer">
      种子整数，用于控制生成内容的随机性

      取值范围：`-1` \~ `2^32-1`

      相同 seed 值会生成类似结果，但不保证完全一致
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
  ```bash cURL（快速预览横屏视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-fast",
      "prompt": "海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩",
      "duration": 5,
      "aspect_ratio": "16:9",
      "resolution": "720p"
    }'
  ```

  ```bash cURL（高质量竖屏短视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-quality",
      "prompt": "女孩在樱花树下旋转，花瓣随风飘落",
      "duration": 5,
      "aspect_ratio": "9:16",
      "resolution": "1080p"
    }'
  ```

  ```bash cURL（首尾帧动态过渡） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-quality",
      "prompt": "画面从白天逐渐过渡到夜晚，城市灯光逐渐亮起",
      "duration": 5,
      "image_with_roles": [
        {"url": "https://example.com/day.png", "role": "first_frame"},
        {"url": "https://example.com/night.png", "role": "last_frame"}
      ]
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
          "model": "doubao-seedance-1-0-pro-fast",
          "prompt": "海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩",
          "duration": 5,
          "aspect_ratio": "16:9",
          "resolution": "720p"
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
      model: 'doubao-seedance-1-0-pro-fast',
      prompt: '海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩',
      duration: 5,
      aspect_ratio: '16:9',
      resolution: '720p'
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
    "id": "task_vid_xyz789ghi012",
    "object": "generation.task",
    "model": "doubao-seedance-1-0-pro-fast",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
