---
title: "Kling v2.6 视频生成"
---
Source: https://docs.toapis.com/docs/cn/api-reference/videos/kling-2-6/generation

POST https://toapis.com/v1/videos/generations
使用可灵 Kling v2.6 模型生成高质量视频，支持文生视频和图生视频

* 异步处理模式，返回任务 ID 供后续查询
* 支持文生视频、图生视频（首帧控制 / 首尾帧控制）
* 支持标准模式（720P）和专业模式（1080P）
* 专业模式支持自动音频生成

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
  视频生成模型名称，固定为 `kling-2-6`
</ParamField>

<ParamField type="string">
  文字提示词，最多 **2500 字符**

  建议详细描述场景、动作、风格等以获得更好的生成效果

  示例：`"一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感"`
</ParamField>

<ParamField type="string">
  生成模式

  可选值：

  * `std` - 标准模式（720P，仅支持静音视频）
  * `pro` - 专业模式（1080P，支持自动音频生成）

  默认值：`std`

  <Warning>
    **标准模式限制**：`std` 模式仅支持静音视频，`audio` 参数需在 `pro` 模式下使用。
  </Warning>
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

<ParamField type="string">
  负面提示词，描述不希望出现的内容

  示例：`"模糊, 低质量, 变形"`
</ParamField>

<ParamField type="string[]">
  图片 URL 数组，用于图生视频

  * 传入 **1 张图片**：作为首帧
  * 传入 **2 张图片**：自动设为首帧 + 尾帧（需 `mode: "pro"`）

  最多支持 2 张图片

  示例：`["https://example.com/first.jpg"]`

  <Warning>
    * 最多支持 2 张图片
    * 尾帧（2 张图片）仅 `pro` 模式支持；`std` 模式仅支持首帧（1 张图片）
    * **尾帧与音频互斥**：`pro` 模式下，尾帧（2 张图片）和音频（`audio: true`）不能同时使用
    * 图生视频模式下，`aspect_ratio` 可能被实际图片比例覆盖
  </Warning>
</ParamField>

<ParamField type="boolean">
  是否自动生成音频

  默认值：`false`

  <Warning>
    * 仅在 `mode: "pro"` 下可用
    * **与尾帧互斥**：音频不能与尾帧（2 张图片）同时使用
  </Warning>
</ParamField>

<ParamField type="boolean">
  是否添加水印
</ParamField>

## 计费说明

按秒计费，价格因模式和音频设置而异：

| 模式    | 分辨率   | 音频  | 单价           |
| ----- | ----- | --- | ------------ |
| `std` | 720P  | 不支持 | \$0.0368 / 秒 |
| `pro` | 1080P | 关闭  | \$0.0625 / 秒 |
| `pro` | 1080P | 开启  | \$0.125 / 秒  |

示例：`pro` 模式生成 10 秒无音频视频，费用 = 10 × $0.0625 = $0.625

## 功能支持矩阵

| 类型   | 功能   | std 5s | std 10s | pro 5s | pro 10s |
| ---- | ---- | ------ | ------- | ------ | ------- |
| 文生视频 | 生成   | ✅（仅静音） | ✅（仅静音）  | ✅      | ✅       |
| 文生视频 | 自动音频 | -      | -       | ✅      | ✅       |
| 图生视频 | 生成   | ✅（仅静音） | ✅（仅静音）  | ✅      | ✅       |
| 图生视频 | 首帧控制 | ✅      | ✅       | ✅      | ✅       |
| 图生视频 | 尾帧控制 | -      | -       | ✅      | ✅       |
| 图生视频 | 自动音频 | -      | -       | ✅      | ✅       |

> `pro` 模式下，尾帧控制与音频互斥，不能同时使用。

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

### 场景 1：文生视频（标准模式）

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "一只金色的猫咪在阳光普照的草地上奔跑，慢动作，电影质感",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### 场景 2：文生视频（专业模式 + 负面提示词）

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "东京涩谷夜晚的十字路口，霓虹灯倒映在湿润的地面上，人们撑伞走过",
  "negative_prompt": "模糊, 低质量, 变形",
  "mode": "pro",
  "duration": 10,
  "aspect_ratio": "16:9"
}
```

### 场景 3：图生视频（首帧控制）

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "画面中的人转身微笑",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

### 场景 4：图生视频（首尾帧控制，需 pro 模式）

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "城市延时摄影，从白天过渡到夜晚",
  "image_urls": ["https://example.com/day-city.jpg", "https://example.com/night-city.jpg"],
  "mode": "pro",
  "duration": 5
}
```

### 场景 5：专业模式 + 自动音频

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "海浪拍打礁石，海鸥在空中盘旋，远处有一座灯塔",
  "mode": "pro",
  "duration": 10,
  "audio": true,
  "aspect_ratio": "16:9"
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
      "model": "kling-2-6",
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
          "model": "kling-2-6",
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
      model: 'kling-2-6',
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
    "model": "kling-2-6",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
