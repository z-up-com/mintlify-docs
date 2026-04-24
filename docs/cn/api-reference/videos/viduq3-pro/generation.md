# Vidu Q3 Pro 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/viduq3-pro/generation

POST https://toapis.com/v1/videos/generations
使用 Vidu Q3 Pro 模型生成高质量视频，支持文生视频、图生视频和首尾帧控制

* 异步处理模式，返回任务 ID 供后续查询
* 支持文生视频、图生视频、首尾帧视频生成
* 支持 540p / 720p / 1080p 分辨率
* 时长范围 1-16 秒，默认开启音频

## 认证

<ParamField type="string">
  所有接口均需要使用 Bearer Token 进行认证

  获取 API Key：访问 [API Key 管理页面](https://toapis.com/console/token) 获取您的 API Key

  使用时在请求头中添加：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## 请求参数

<ParamField type="string">
  视频生成模型名称，固定为 `viduq3-pro`
</ParamField>

<ParamField type="string">
  文字提示词，最多 **2000 字符**

  文生视频模式下必填；图生视频和首尾帧模式下可选

  示例：`"一只猫咪在弹钢琴，镜头缓缓推近"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  范围：`1` 到 `16`

  默认值：`5`
</ParamField>

<ParamField type="string">
  视频分辨率

  可选值：

  * `540p` - 标清
  * `720p` - 高清（默认）
  * `1080p` - 全高清

  默认值：`720p`
</ParamField>

<ParamField type="string">
  视频宽高比（仅文生视频模式有效）

  可选值：

  * `16:9` - 横屏
  * `9:16` - 竖屏
  * `4:3` - 传统横屏
  * `3:4` - 传统竖屏
  * `1:1` - 方形

  <Warning>
    此参数仅在文生视频模式（未提供 `image_urls`）下有效。图生视频模式下，视频宽高比由参考图片自动决定。
  </Warning>
</ParamField>

<ParamField type="string[]">
  图片 URL 数组，用于图生视频

  系统根据图片数量自动判断生成模式：

  * **0 张**（不提供）：文生视频模式
  * **1 张**：图生视频模式（图片作为起始帧）
  * **2 张**：首尾帧模式（第一张=首帧，第二张=尾帧）

  示例：`["https://example.com/photo.jpg"]`

  <Warning>
    * 最多支持 2 张图片
    * 首尾帧模式需提供恰好 2 张图片
    * 提供 `image_urls` 时（无论 1 张还是 2 张），`aspect_ratio` 参数不可用
  </Warning>
</ParamField>

<ParamField type="boolean">
  是否生成音频（对话、音效）

  默认值：`true`

  设为 `false` 可生成静音视频
</ParamField>

<ParamField type="integer">
  随机种子，用于复现结果

  相同种子和相同参数将产生相同的视频输出
</ParamField>

## 自动路由说明

系统根据 `image_urls` 中的图片数量自动判断生成模式：

| 图片数量   | 模式    | 说明            |
| ------ | ----- | ------------- |
| 0（不提供） | 文生视频  | 仅根据文字描述生成     |
| 1      | 图生视频  | 以图片作为起始帧      |
| 2      | 首尾帧模式 | 第一张=首帧，第二张=尾帧 |

## 参数支持矩阵

| 参数             | 文生视频    | 图生视频    | 首尾帧模式   |
| -------------- | ------- | ------- | ------- |
| `model`        | ✅ 必填    | ✅ 必填    | ✅ 必填    |
| `prompt`       | ✅ 必填    | 可选      | 可选      |
| `image_urls`   | -       | ✅ 1 张   | ✅ 2 张   |
| `duration`     | ✅ 1-16s | ✅ 1-16s | ✅ 1-16s |
| `resolution`   | ✅       | ✅       | ✅       |
| `aspect_ratio` | ✅       | -       | -       |
| `audio`        | ✅       | ✅       | ✅       |
| `seed`         | ✅       | ✅       | ✅       |

## 响应

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

## 使用场景

### 场景 1：文生视频

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "一只猫咪在弹钢琴，镜头缓缓推近",
  "duration": 8,
  "resolution": "1080p",
  "aspect_ratio": "16:9"
}
```

### 场景 2：图生视频（单张图片）

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "人物缓缓转身，微笑着看向镜头",
  "image_urls": ["https://example.com/photo.jpg"],
  "duration": 5,
  "resolution": "720p"
}
```

### 场景 3：首尾帧视频

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "人物从站立缓缓坐下",
  "image_urls": [
    "https://example.com/first.jpg",
    "https://example.com/last.jpg"
  ],
  "duration": 8
}
```

### 场景 4：静音视频

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "日落时分的海景延时摄影",
  "duration": 10,
  "resolution": "1080p",
  "audio": false
}
```

<Note>
  **查询任务结果**

  视频生成为异步任务，提交后会返回任务 ID。使用 [获取任务状态](/cn/api-reference/tasks/video-status) 接口查询生成进度和结果。
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "viduq3-pro",
      "prompt": "一只猫咪在弹钢琴，镜头缓缓推近",
      "duration": 8,
      "resolution": "1080p",
      "aspect_ratio": "16:9"
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
          "model": "viduq3-pro",
          "prompt": "一只猫咪在弹钢琴，镜头缓缓推近",
          "duration": 8,
          "resolution": "1080p",
          "aspect_ratio": "16:9"
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
      model: 'viduq3-pro',
      prompt: '一只猫咪在弹钢琴，镜头缓缓推近',
      duration: 8,
      resolution: '1080p',
      aspect_ratio: '16:9'
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "viduq3-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
