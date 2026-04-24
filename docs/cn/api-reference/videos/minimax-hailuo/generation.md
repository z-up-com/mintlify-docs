# MiniMax-Hailuo-02 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/minimax-hailuo/generation

POST https://toapis.com/v1/videos/generations
使用 MiniMax Hailuo 02 模型生成高质量视频

* 异步处理模式，返回任务ID用于后续查询
* 支持文本转视频、图生视频（首帧/尾帧）
* 支持 5 秒和 10 秒时长，多种分辨率可选
* 支持 prompt 自动优化和水印控制

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `image_urls`、`first_frame_image` 和 `last_frame_image` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
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
  视频生成模型名称

  固定值：`MiniMax-Hailuo-02`
</ParamField>

<ParamField type="string">
  视频内容描述

  建议详细描述场景、动作、风格等，以获得更好的生成效果

  示例：`"一只可爱的猫咪在草地上奔跑"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  可选值：

  * `5` - 5秒视频
  * `10` - 10秒视频

  默认值：`5`

  **1080p 限制**：使用 1080p 分辨率时，仅支持 5 秒时长
</ParamField>

<ParamField type="object">
  视频生成的高级配置选项

  <Expandable title="显示 metadata 字段说明">
    <ParamField type="string">
      视频分辨率

      可选值：

      * `512p` - 标清
      * `768p` - 高清
      * `1080p` - 全高清（仅支持 5 秒时长）

      默认值：`768p`
    </ParamField>

    <ParamField type="boolean">
      是否自动优化 prompt

      启用后，系统会自动优化您的提示词以获得更好的生成效果

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

    <ParamField type="string">
      视频起始帧图片

      **⚠️ 仅支持 URL 格式（不再支持 base64）**

      * 公开可访问的图像 URL（http\:// 或 https\://）
      * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

      用于指定视频的起始画面
    </ParamField>

    <ParamField type="string">
      视频结束帧图片

      **⚠️ 仅支持 URL 格式（不再支持 base64）**

      * 公开可访问的图像 URL（http\:// 或 https\://）
      * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

      用于指定视频的结束画面
    </ParamField>
  </Expandable>
</ParamField>

## 参数限制

| 限制项       | 说明                        |
| --------- | ------------------------- |
| 时长        | 仅支持 5 或 10 秒              |
| 1080p 分辨率 | 仅支持 5 秒时长                 |
| 图片格式      | 仅支持公网 URL（不再支持 Base64 编码） |

## 分辨率与时长组合

| 分辨率   | 支持的时长  | 备注     |
| ----- | ------ | ------ |
| 512p  | 5秒、10秒 | 全部支持   |
| 768p  | 5秒、10秒 | 全部支持   |
| 1080p | 5秒     | 不支持10秒 |

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

### 场景 1：快速生成文本视频

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "一只可爱的猫咪在草地上奔跑，阳光明媚"
}
```

### 场景 2：生成高质量 1080p 视频

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "城市夜景，霓虹灯闪烁，车流穿梭",
  "duration": 5,
  "metadata": {
    "resolution": "1080p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### 场景 3：基于首帧图生成视频

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "人物缓缓转身，面带微笑",
  "duration": 5,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/portrait.jpg"
  }
}
```

### 场景 4：首尾帧控制的转场视频

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "画面从白天逐渐过渡到夜晚，天空渐变",
  "duration": 10,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/day.jpg",
    "last_frame_image": "https://example.com/night.jpg",
    "prompt_optimizer": true
  }
}
```

### 场景 5：快速预处理模式

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "海浪拍打沙滩，日落时分",
  "duration": 5,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "fast_pretreatment": true
  }
}
```

<Note>
  **查询任务结果**

  视频生成为异步任务，提交后会返回 `task_id`。使用 [获取任务状态](/cn/api-reference/tasks/status) 接口查询生成进度和结果。
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "MiniMax-Hailuo-02",
      "prompt": "一只可爱的猫咪在草地上奔跑",
      "duration": 5,
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
          "model": "MiniMax-Hailuo-02",
          "prompt": "一只可爱的猫咪在草地上奔跑",
          "duration": 5,
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
      model: 'MiniMax-Hailuo-02',
      prompt: '一只可爱的猫咪在草地上奔跑',
      duration: 5,
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
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "MiniMax-Hailuo-02",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "resolution": "768p"
    }
  }
  ```
</ResponseExample>
