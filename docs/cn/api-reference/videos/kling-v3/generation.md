# Kling v3 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/kling-v3/generation

POST https://toapis.com/v1/videos/generations
使用可灵 Kling v3 模型生成高质量视频，支持文生视频和图生视频

* 异步处理模式，返回任务 ID 供后续查询
* 支持文生视频、图生视频（首帧控制 / 首尾帧控制）
* 支持标准模式（720P）和专业模式（1080P）
* 文生视频支持 15 秒时长
* 支持生成有声视频

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `image_urls` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
</Warning>

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
  视频生成模型名称，固定为 `kling-v3`
</ParamField>

<ParamField type="string">
  文字提示词，建议详细描述场景、动作、风格等以获得更好的生成效果

  示例：`"一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感"`
</ParamField>

<ParamField type="string">
  生成模式

  可选值：

  * `std` - 标准模式（720P）
  * `pro` - 专业模式（1080P）

  默认值：`std`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  可选值：`5`、`10` 或 `15`

  默认值：`5`

  <Warning>
    **15 秒时长**仅文生视频支持，图生视频最长 10 秒。
  </Warning>
</ParamField>

<ParamField type="string">
  视频宽高比

  可选值：

  * `16:9` - 横屏（默认）
  * `9:16` - 竖屏
  * `1:1` - 方形

  默认值：`16:9`
</ParamField>

<ParamField type="string[]">
  图片 URL 数组，用于图生视频

  * 传入 **1 张图片**：作为首帧
  * 传入 **2 张图片**：自动设为首帧 + 尾帧

  最多支持 2 张图片

  示例：`["https://example.com/first.jpg"]`

  <Warning>
    * 最多支持 2 张图片
    * 图生视频时，`aspect_ratio` 可能被实际图片比例覆盖
  </Warning>
</ParamField>

<ParamField type="boolean">
  是否生成有声视频

  设为 `true` 时，生成的视频将包含与画面匹配的音频

  默认值：`false`
</ParamField>

<ParamField type="object">
  扩展参数

  <Expandable title="显示 metadata 字段">
    <ParamField type="string">
      负面提示词，描述不希望出现的内容

      示例：`"模糊, 低质量, 变形"`
    </ParamField>

    <ParamField type="string">
      尾帧图片 URL，作为图生视频的替代方案

      也可通过 `image_urls` 传 2 张图片来替代此参数
    </ParamField>

    <ParamField type="boolean">
      是否添加水印
    </ParamField>
  </Expandable>
</ParamField>

## 功能支持矩阵

| 类型   | 功能   | std 5s | std 10s | std 15s | pro 5s | pro 10s |
| ---- | ---- | ------ | ------- | ------- | ------ | ------- |
| 文生视频 | 视频生成 | ✅      | ✅       | ✅       | ✅      | ✅       |
| 图生视频 | 视频生成 | ✅      | ✅       | -       | ✅      | ✅       |
| 图生视频 | 首帧控制 | ✅      | ✅       | -       | ✅      | ✅       |
| 图生视频 | 尾帧控制 | ✅      | ✅       | -       | ✅      | ✅       |

> **注意**：15 秒时长仅文生视频支持，图生视频最长 10 秒。

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

<ResponseField name="metadata" type="object">
  任务元数据
</ResponseField>

## 使用场景

### 场景 1：文生视频（标准模式）

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### 场景 2：文生视频（专业模式 + 负面提示词）

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "东京涩谷夜晚的十字路口，霓虹灯倒映在湿润的地面上，人们撑伞走过",
  "metadata": {
    "negative_prompt": "模糊, 低质量, 变形"
  },
  "mode": "pro",
  "duration": 10,
  "aspect_ratio": "16:9"
}
```

### 场景 3：文生视频（15 秒）

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "花园中一朵花绽放的延时摄影",
  "duration": 15,
  "aspect_ratio": "16:9"
}
```

### 场景 4：图生视频（首帧控制）

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "画面中的人转身微笑",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

### 场景 5：图生视频（首尾帧控制）

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "城市延时摄影，从白天过渡到夜晚",
  "image_urls": ["https://example.com/day-city.jpg", "https://example.com/night-city.jpg"],
  "mode": "pro",
  "duration": 5
}
```

也可通过 `metadata.last_frame_image` 指定尾帧：

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "流畅的电影级画面过渡",
  "image_urls": ["https://example.com/frame-start.jpg"],
  "metadata": {
    "last_frame_image": "https://example.com/frame-end.jpg"
  },
  "mode": "std",
  "duration": 5
}
```

### 场景 6：生成有声视频

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "一个在舞台上唱歌的摇滚歌手，演唱会现场，灯光闪烁",
  "audio": true,
  "mode": "std",
  "duration": 5
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
      "model": "kling-v3",
      "prompt": "一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感",
      "mode": "std",
      "duration": 5,
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
          "model": "kling-v3",
          "prompt": "一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感",
          "mode": "std",
          "duration": 5,
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
      model: 'kling-v3',
      prompt: '一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感',
      mode: 'std',
      duration: 5,
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
    "model": "kling-v3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
