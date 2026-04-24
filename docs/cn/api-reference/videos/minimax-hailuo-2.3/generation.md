---
title: "MiniMax-Hailuo-2.3 视频生成"
---
Source: https://docs.toapis.com/docs/cn/api-reference/videos/minimax-hailuo-2.3/generation

POST https://toapis.com/v1/videos/generations
使用 MiniMax Hailuo 2.3 模型生成高质量视频

* 异步处理模式，返回任务 ID 供后续查询
* 支持文生视频、图生视频（首帧控制）
* 支持 6 秒和 10 秒时长，768p/1080p 分辨率
* 支持 15 种运镜指令、prompt 自动优化和水印控制

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `metadata.first_frame_image` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
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
  视频生成模型名称，固定为 `MiniMax-Hailuo-2.3`
</ParamField>

<ParamField type="string">
  视频内容描述，最多 **2000 字符**

  建议详细描述场景、动作、风格等。支持运镜指令（如 `[推进]`、`[拉远]` 等，详见下方运镜指令表）

  示例：`"一只可爱的猫咪在草地上奔跑"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  可选值：`6` 或 `10`

  默认值：`6`

  <Warning>
    **1080p 限制**：使用 1080p 分辨率时，仅支持 6 秒时长。
  </Warning>
</ParamField>

<ParamField type="object">
  扩展参数

  <Expandable title="显示 metadata 字段">
    <ParamField type="string">
      视频分辨率

      可选值：

      * `768p` - 高清（默认）
      * `1080p` - 全高清（仅支持 6 秒时长）

      默认值：`768p`
    </ParamField>

    <ParamField type="string">
      首帧图片 URL 或 base64，用于图生视频

      传入后将以该图片作为视频的起始画面

      **⚠️ 建议使用 URL**：请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传获取 URL
    </ParamField>

    <ParamField type="boolean">
      是否自动优化 prompt

      启用后系统会自动优化提示词以获得更好的生成效果

      默认值：`true`
    </ParamField>

    <ParamField type="boolean">
      是否缩短 prompt 优化耗时

      启用后可加快处理速度，但可能略微影响优化效果

      默认值：`false`
    </ParamField>

    <ParamField type="boolean">
      是否添加水印

      默认值：`false`
    </ParamField>
  </Expandable>
</ParamField>

## 分辨率与时长组合

| 分辨率   | 支持的时长    | 备注       |
| ----- | -------- | -------- |
| 768p  | 6 秒、10 秒 | 全部支持     |
| 1080p | 6 秒      | 不支持 10 秒 |

## 运镜指令

在 `prompt` 中使用 `[指令]` 语法控制运镜效果，支持以下 15 种指令：

| 类别  | 指令                   |
| --- | -------------------- |
| 平移  | `[左移]` `[右移]`        |
| 左右摇 | `[左摇]` `[右摇]`        |
| 推拉  | `[推进]` `[拉远]`        |
| 升降  | `[上升]` `[下降]`        |
| 上下摇 | `[上摇]` `[下摇]`        |
| 变焦  | `[变焦推近]` `[变焦拉远]`    |
| 其他  | `[晃动]` `[跟随]` `[固定]` |

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

### 场景 1：快速文生视频

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "一只可爱的猫咪在草地上奔跑，阳光明媚"
}
```

### 场景 2：1080p 高质量视频

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "城市夜景，霓虹灯闪烁，车流穿梭",
  "duration": 6,
  "metadata": {
    "resolution": "1080p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### 场景 3：指定首帧图片

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "小猫跑向镜头，微笑着眨眼",
  "duration": 6,
  "metadata": {
    "first_frame_image": "https://example.com/cat.jpg",
    "resolution": "1080p"
  }
}
```

### 场景 4：使用运镜指令

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "[推进]一只猫咪在花园中奔跑，镜头缓缓推进特写",
  "duration": 6,
  "metadata": {
    "resolution": "768p"
  }
}
```

### 场景 5：快速预处理模式

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "海浪拍打沙滩，日落时分",
  "duration": 10,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "fast_pretreatment": true
  }
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
      "model": "MiniMax-Hailuo-2.3",
      "prompt": "一只可爱的猫咪在草地上奔跑",
      "duration": 6,
      "metadata": {
        "resolution": "768p",
        "prompt_optimizer": true,
        "fast_pretreatment": false,
        "watermark": false
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
          "model": "MiniMax-Hailuo-2.3",
          "prompt": "一只可爱的猫咪在草地上奔跑",
          "duration": 6,
          "metadata": {
              "resolution": "768p",
              "prompt_optimizer": True,
              "fast_pretreatment": False,
              "watermark": False
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
      model: 'MiniMax-Hailuo-2.3',
      prompt: '一只可爱的猫咪在草地上奔跑',
      duration: 6,
      metadata: {
        resolution: '768p',
        prompt_optimizer: true,
        fast_pretreatment: false,
        watermark: false
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "MiniMax-Hailuo-2.3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
