# Doubao SeeDance 1.5 Pro 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/doubao-seedance-1-5/generation

POST https://toapis.com/v1/videos/generations
使用字节跳动豆包 Doubao SeeDance 1.5 Pro 模型生成视频

* 字节跳动豆包视频生成模型 1.5 Pro 版本
* 支持文本到视频生成
* 支持首帧/尾帧/多参考图控制（1-4 张参考图）
* 支持音频生成（1.5 Pro 独有功能）
* 异步任务管理，通过任务 ID 查询结果

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

  * `doubao-seedance-1-5-pro` - 1.5 Pro 版，支持音频生成和多参考图
</ParamField>

<ParamField type="string">
  视频内容描述

  详细描述场景、动作、风格以获得更好的生成效果

  示例：`"海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  支持范围：`4` \~ `12` 秒

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

## 分辨率与宽高比组合

| 分辨率   | 支持的宽高比                          | 备注       |
| ----- | ------------------------------- | -------- |
| 480p  | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | 全部支持     |
| 720p  | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | 全部支持（默认） |
| 1080p | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | 全部支持     |

<ParamField type="string[]">
  图片 URL 数组，用于图生视频

  自动分配角色规则：

  * 1张 = 首帧
  * 2张 = 首帧 + 尾帧
  * 3+张 = 首帧 + 尾帧 + 参考图

  示例：`["https://example.com/first.png", "https://example.com/last.png"]`

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
      * `last_frame` - 尾帧图，作为视频结束画面（仅支持一张）
      * `reference_image` - 参考图，用于指导视频风格（支持 1-4 张）
    </ParamField>
  </Expandable>

  示例：

  ```json theme={null}
  [
    {"url": "https://example.com/start.png", "role": "first_frame"},
    {"url": "https://example.com/end.png", "role": "last_frame"},
    {"url": "https://example.com/ref1.png", "role": "reference_image"},
    {"url": "https://example.com/ref2.png", "role": "reference_image"}
  ]
  ```

  <Warning>
    * `image_urls` 和 `image_with_roles` 不能同时使用
    * 首帧和尾帧仅支持各一张
    * 参考图使用 `reference_image` 角色（注意：1.0 版本使用 `reference`）
  </Warning>
</ParamField>

<ParamField type="object">
  扩展参数

  <Expandable title="字段说明">
    <ParamField type="string">
      视频分辨率

      可选项：

      * `480p` - 标清
      * `720p` - 高清（默认）
      * `1080p` - 全高清
    </ParamField>

    <ParamField type="integer">
      种子整数，用于控制生成内容的随机性

      取值范围：`-1` \~ `2^32-1`

      相同 seed 值会生成类似结果，但不保证完全一致
    </ParamField>

    <ParamField type="boolean">
      是否生成音频

      设置为 `true` 时，视频将包含 AI 生成的配套音频

      **1.5 Pro 独有功能**
    </ParamField>

    <ParamField type="boolean">
      是否固定摄像头

      设置为 `true` 时，摄像头位置保持固定
    </ParamField>
  </Expandable>
</ParamField>

## 与 1.0 版本的差异

| 特性    | 1.0 fast/quality | 1.5 Pro                      |
| ----- | ---------------- | ---------------------------- |
| 默认分辨率 | 1080p            | **720p**                     |
| 支持分辨率 | 480p/720p/1080p  | **480p/720p/1080p**          |
| 时长范围  | 2-12秒            | **4-12秒**                    |
| 音频生成  | 不支持              | **支持**                       |
| 参考图角色 | `reference` (1张) | **`reference_image` (1-4张)** |

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
  ```bash cURL（带音频的文生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩",
      "duration": 5,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p",
        "audio": true
      }
    }'
  ```

  ```bash cURL（高质量竖屏短视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "女孩在樱花树下旋转，花瓣随风飘落",
      "duration": 5,
      "aspect_ratio": "9:16",
      "metadata": {
        "resolution": "720p",
        "audio": true
      }
    }'
  ```

  ```bash cURL（多参考图风格控制） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "参考图片风格生成视频，保持一致的视觉效果",
      "image_with_roles": [
        {"url": "https://example.com/first.png", "role": "first_frame"},
        {"url": "https://example.com/ref1.png", "role": "reference_image"},
        {"url": "https://example.com/ref2.png", "role": "reference_image"}
      ],
      "metadata": {
        "audio": true
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
          "model": "doubao-seedance-1-5-pro",
          "prompt": "海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩",
          "duration": 5,
          "aspect_ratio": "16:9",
          "metadata": {
              "resolution": "720p",
              "audio": True
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
      model: 'doubao-seedance-1-5-pro',
      prompt: '海滩日落，金色阳光照在海面上，海浪轻轻拍打沙滩',
      duration: 5,
      aspect_ratio: '16:9',
      metadata: {
        resolution: '720p',
        audio: true
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
    "id": "task_vid_xyz789ghi012",
    "object": "generation.task",
    "model": "doubao-seedance-1-5-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
