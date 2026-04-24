---
title: "Gemini-2.5-Flash 图像生成"
---
Source: https://docs.toapis.com/docs/cn/api-reference/images/gemini-2.5-flash/generation

POST https://toapis.com/v1/images/generations
使用 Google Gemini 2.5 Flash 模型生成图像，快速高效

* Google Gemini 2.5 Flash 高效图像生成模型 (Nano banana)
* 通过 model 参数选择 `gemini-2.5-flash-image-preview` 模型
* 支持文本到图像生成
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
  图像生成模型名称

  可用模型别名：`nano-banana`

  示例：`"gemini-2.5-flash-image-preview"`
</ParamField>

<ParamField type="string">
  图像生成的文本描述

  最长 1000 个字符
</ParamField>

<ParamField type="string">
  图像生成的尺寸比例

  支持的格式：

  | 值      | 说明   | 像素（参考）    |
  | ------ | ---- | --------- |
  | `1:1`  | 正方形  | 1024x1024 |
  | `16:9` | 横向宽屏 | 1792x1024 |
  | `9:16` | 竖向长图 | 1024x1792 |
  | `4:3`  | 横向标准 | 1365x1024 |
  | `3:4`  | 竖向标准 | 1024x1365 |
  | `3:2`  | 横向相片 | 1536x1024 |
  | `2:3`  | 竖向相片 | 1024x1536 |
</ParamField>

<ParamField type="integer">
  生成图像的数量

  固定为 1

  **⚠️ 注意：** 必须是纯数字（如 `1`），不要加引号，否则会报错
</ParamField>

<ParamField type="object[]">
  参考图像 URL 列表，用于图生图或图像编辑

  <Expandable title="详细字段说明">
    <ParamField type="string">
      图像 URL 地址

      **⚠️ 仅支持 URL 格式（不再支持 base64）**

      * 公开可访问的图片 URL（http\:// 或 https\://）
      * 示例：`https://example.com/image.jpg`
      * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

      **限制：**

      * 单张图片不得超过 10MB
      * 支持格式：.jpeg, .jpg, .png, .webp
    </ParamField>
  </Expandable>

  **限制：** 最多 14 张图片
</ParamField>

<ParamField type="object">
  元数据参数，用于传递额外的配置选项

  <Expandable title="支持的元数据字段">
    <ParamField type="string">
      输出图像分辨率

      支持的值：

      * `1K` - 1K 分辨率（默认，唯一支持的选项）
    </ParamField>

    <ParamField type="string">
      图像方向

      支持的值：

      * `landscape` - 横向
      * `portrait` - 竖向
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
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-2.5-flash-image-preview",
      "prompt": "一只穿着宇航服的猫咪在月球上行走",
      "size": "1:1",
      "n": 1
    }'
  ```

  ```bash cURL (图生图示例) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-2.5-flash-image-preview",
      "prompt": "将这只猫变成卡通风格",
      "size": "1:1",
      "n": 1,
      "image_urls": ["https://example.com/cat.jpg"]
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gemini-2.5-flash-image-preview",
          "prompt": "一只穿着宇航服的猫咪在月球上行走",
          "size": "1:1",
          "n": 1
      }
  )

  task = response.json()
  print(f"任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  ```

  ```python Python (图生图) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gemini-2.5-flash-image-preview",
          "prompt": "将这只猫变成卡通风格",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/cat.jpg"]
      }
  )

  task = response.json()
  print(f"任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gemini-2.5-flash-image-preview',
      prompt: '一只穿着宇航服的猫咪在月球上行走',
      size: '1:1',
      n: 1
    })
  });

  const task = await response.json();
  console.log(`任务 ID: ${task.id}`);
  console.log(`状态: ${task.status}`);
  ```

  ```javascript JavaScript (图生图) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gemini-2.5-flash-image-preview',
      prompt: '将这只猫变成卡通风格',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/cat.jpg']
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
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gemini-2.5-flash-image-preview",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
