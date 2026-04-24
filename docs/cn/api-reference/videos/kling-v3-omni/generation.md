# Kling v3 Omni 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/kling-v3-omni/generation

POST https://toapis.com/v1/videos/generations
使用可灵 Kling v3 Omni 模型生成视频，统一文生视频和图生视频接口

* 异步处理模式，返回任务 ID 供后续查询
* 统一文生视频/图生视频接口，支持 `<<<image_N>>>` 图片引用语法
* 支持标准模式（720P）和专业模式（1080P）
* 若传入图片但 prompt 中无引用，系统自动在 prompt 前添加 `<<<image_1>>>`
* 支持生成有声视频（与 `video_list` 互斥）
* 支持参考视频编辑和特征参考

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
  视频生成模型名称，固定为 `kling-v3-omni`
</ParamField>

<ParamField type="string">
  文字提示词，支持 `<<<image_N>>>` 语法引用 `image_urls` 中的图片，`N` 从 1 开始

  示例：`"让<<<image_1>>>中的人物向镜头挥手"`

  <Note>
    若传了 `image_urls` 但 prompt 中没有 `<<<image_N>>>` 引用，系统会自动在 prompt 前添加 `<<<image_1>>>`。
  </Note>
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

  可选值：`5` 或 `10`

  默认值：`5`
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
  图片 URL 数组，用于图片引用

  在 prompt 中通过 `<<<image_N>>>` 引用对应位置的图片（N 从 1 开始）

  示例：`["https://example.com/photo.jpg"]`

  <Warning>
    * 图片 URL 必须公开可访问，不能有防盗链
    * 图生视频时，`aspect_ratio` 可能被图片实际比例覆盖
  </Warning>
</ParamField>

<ParamField type="object[]">
  参考视频列表，最多 1 段视频

  每个对象包含以下字段：

  <Expandable title="显示 video_list 对象字段">
    <ParamField type="string">
      视频 URL，必须公开可访问

      仅支持 MP4/MOV 格式；时长不少于 3 秒；分辨率 720px-2160px；帧率 24-60fps；大小不超过 200MB
    </ParamField>

    <ParamField type="string">
      参考类型

      可选值：

      * `base` - 待编辑视频（默认）
      * `feature` - 特征参考视频

      默认值：`base`
    </ParamField>

    <ParamField type="string">
      是否保留原视频声音

      可选值：

      * `yes` - 保留原声
      * `no` - 不保留（默认）

      默认值：`no`
    </ParamField>
  </Expandable>

  <Warning>
    * `video_url` 不能为空，且视频 URL 需可访问
    * 当 `refer_type=base` 时：不能定义视频首尾帧；参考视频需 3–10 秒；生成视频时长以上传视频为准
    * 当 `refer_type=feature` 且 `video_url` 不为空时：`image_urls` 只可上传首帧图片
    * 此参数与 `audio` 互斥，不可同时使用
  </Warning>
</ParamField>

<ParamField type="boolean">
  是否生成有声视频

  设为 `true` 时，生成的视频将包含与画面匹配的音频

  默认值：`false`

  <Warning>
    此参数与 `video_list` 互斥。当 `video_list` 有值时，不可使用 `audio` 参数。
  </Warning>
</ParamField>

<ParamField type="object">
  扩展参数

  <Expandable title="显示 metadata 字段">
    <ParamField type="boolean">
      是否添加水印
    </ParamField>
  </Expandable>
</ParamField>

## 图片引用语法

Omni 模型使用 `<<<image_N>>>` 语法在提示词中引用图片，实现统一的文生视频/图生视频体验：

| 语法              | 说明                         |
| --------------- | -------------------------- |
| `<<<image_1>>>` | 引用 `image_urls` 数组中第 1 张图片 |
| `<<<image_2>>>` | 引用 `image_urls` 数组中第 2 张图片 |

<Note>
  **自动引用**：若传了 `image_urls` 但 prompt 中没有 `<<<image_N>>>` 引用，系统会自动在 prompt 前添加 `<<<image_1>>>`。
</Note>

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

### 场景 1：文生视频

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "一只金毛犬在沙滩上奔跑，日落，电影质感",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### 场景 2：单张图片引用

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "让<<<image_1>>>中的人物向镜头挥手",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "pro",
  "duration": 5
}
```

### 场景 3：多图片引用

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "<<<image_1>>>中的角色走向<<<image_2>>>中的场景",
  "image_urls": [
    "https://example.com/character.jpg",
    "https://example.com/scene.jpg"
  ],
  "mode": "pro",
  "duration": 5
}
```

### 场景 4：传图片但不显式引用（自动添加）

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "人物缓缓转头微笑",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

> 系统会自动在 prompt 前添加 `<<<image_1>>>`，等效于 `"<<<image_1>>>人物缓缓转头微笑"`。

### 场景 5：生成有声视频

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "一只黄色的金丝雀在树枝上鸣叫",
  "audio": true,
  "mode": "std",
  "duration": 5
}
```

> **注意**：`audio` 与 `video_list` 互斥。当 `video_list` 有值时，不可使用 `audio` 参数。

### 场景 6：参考视频编辑（video\_list - base）

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "将视频中的背景替换为海边日落",
  "video_list": [
    {
      "video_url": "https://example.com/source-video.mp4",
      "refer_type": "base",
      "keep_original_sound": "no"
    }
  ]
}
```

### 场景 7：特征参考视频（video\_list - feature）

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "<<<image_1>>>中的人物模仿视频中的动作",
  "image_urls": ["https://example.com/character.jpg"],
  "video_list": [
    {
      "video_url": "https://example.com/dance-reference.mp4",
      "refer_type": "feature",
      "keep_original_sound": "no"
    }
  ],
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
      "model": "kling-v3-omni",
      "prompt": "让<<<image_1>>>中的人物向镜头挥手",
      "image_urls": ["https://example.com/portrait.jpg"],
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
          "model": "kling-v3-omni",
          "prompt": "让<<<image_1>>>中的人物向镜头挥手",
          "image_urls": ["https://example.com/portrait.jpg"],
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
      model: 'kling-v3-omni',
      prompt: '让<<<image_1>>>中的人物向镜头挥手',
      image_urls: ['https://example.com/portrait.jpg'],
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
    "model": "kling-v3-omni",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
