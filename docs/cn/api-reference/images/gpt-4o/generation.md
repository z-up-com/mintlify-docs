# GPT-4o 图像生成
Source: https://docs.toapis.com/docs/cn/api-reference/images/gpt-4o/generation

POST https://toapis.com/v1/images/generations
使用 GPT-4o 模型生成图像，支持文本到图像和图像编辑

* 统一的图像生成 API 接口
* 通过 model 参数选择 `gpt-4o-image` 模型
* 支持文本到图像、图生图和图像编辑
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

  示例：`"gpt-4o-image"`
</ParamField>

<ParamField type="string">
  图像生成的文本描述

  最长 1000 个字符
</ParamField>

<ParamField type="string">
  图像生成的尺寸比例

  支持的格式：

  * `1:1` - 正方形（默认）
  * `2:3` - 竖版
  * `3:2` - 横版
</ParamField>

<ParamField type="integer">
  生成图像的数量

  支持 1、2、4，会根据生成数量进行预扣费

  默认：1

  **⚠️ 注意：** 必须输入纯数字（如 `1`），不要加引号，否则会报错
</ParamField>

<ParamField type="string[]">
  参考图像 URL 列表，用于图生图或图像编辑

  **⚠️ 仅支持 URL 格式（不再支持 base64）**

  * 公开可访问的图片 URL（http\:// 或 https\://）
  * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

  **限制：**

  * 最多 5 张图片
  * 单张图片不超过 10MB
  * 支持格式：jpeg、jpg、png、webp
</ParamField>

<ParamField type="string">
  蒙版图像 URL（用于图像编辑）

  * 必须是 PNG 格式
  * 尺寸必须与参考图像匹配
  * 不得超过 4MB
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
      "model": "gpt-4o-image",
      "prompt": "星空下的古老城堡",
      "size": "1:1",
      "n": 1
    }'
  ```

  ```bash cURL (图生图) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-4o-image",
      "prompt": "将这张图片转换为星空下的古老城堡风格",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/castle.jpg"
      ]
    }'
  ```

  ```bash cURL (图像编辑) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-4o-image",
      "prompt": "在城堡上方添加烟花",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/castle.jpg"
      ],
      "mask_url": "https://example.com/mask.png"
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
          "model": "gpt-4o-image",
          "prompt": "星空下的古老城堡",
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
          "model": "gpt-4o-image",
          "prompt": "将这张图片转换为星空下的古老城堡风格",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/castle.jpg"]
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
      model: 'gpt-4o-image',
      prompt: '星空下的古老城堡',
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
      model: 'gpt-4o-image',
      prompt: '将这张图片转换为星空下的古老城堡风格',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/castle.jpg']
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
    "model": "gpt-4o-image",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```

  ```json 400 theme={null}
  {
    "error": {
      "code": "invalid_request",
      "message": "The 'prompt' field is required.",
      "param": "prompt",
      "type": "invalid_request_error"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": "unauthorized",
      "message": "Invalid API key",
      "type": "authentication_error"
    }
  }
  ```
</ResponseExample>
