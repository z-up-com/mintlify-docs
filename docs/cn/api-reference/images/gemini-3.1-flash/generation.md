# Gemini-3.1-Flash 图像生成
Source: https://docs.toapis.com/docs/cn/api-reference/images/gemini-3.1-flash/generation

POST https://toapis.com/v1/images/generations
使用 Google Gemini 3.1 Flash 模型生成图像，支持极端宽高比与 Google 搜索增强

* Google Gemini 3.1 Flash 图像生成模型（Nano banana2）
* 通过 model 参数选择 `gemini-3.1-flash-image-preview` 模型
* 支持文生图、图生图，最高 4K 分辨率输出
* 最多 14 张参考图，保持风格/角色一致性
* 支持极端宽高比（1:4、4:1、1:8、8:1）
* 集成 Google Search 搜索增强，生成更贴合真实世界的图片
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

  示例：`"gemini-3.1-flash-image-preview"`
</ParamField>

<ParamField type="string">
  图像生成的文本描述
</ParamField>

<ParamField type="string">
  图像生成的宽高比

  支持的比例：

  | 值               | 适用场景         |
  | --------------- | ------------ |
  | `1:1`           | 方形图、头像、社交媒体  |
  | `3:2` / `2:3`   | 标准照片         |
  | `4:3` / `3:4`   | 传统显示器比例      |
  | `16:9` / `9:16` | 宽屏/竖屏视频封面    |
  | `5:4` / `4:5`   | Instagram 图片 |
  | `21:9`          | 超宽屏 Banner   |
  | `1:4` / `4:1`   | 长条海报/横幅      |
  | `1:8` / `8:1`   | 极端长图/横幅广告    |
</ParamField>

<ParamField type="integer">
  生成图像的数量

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

  **限制：** 最多 14 张图片（建议：最多 10 张物体参考 + 4 张角色参考）
</ParamField>

<ParamField type="object">
  元数据参数，用于传递额外的配置选项

  <Expandable title="支持的元数据字段">
    <ParamField type="string">
      输出图像分辨率

      支持的值：

      * `0.5K` - 约 512px，低分辨率预览
      * `1K` - 约 1024px，标准分辨率（默认）
      * `2K` - 约 2048px，高分辨率
      * `4K` - 约 4096px，超高分辨率

      **注意：** 不同分辨率计费不同，4K 价格高于 1K
    </ParamField>

    <ParamField type="boolean">
      启用 Google 文字搜索增强

      * `true`：模型会先搜索网络文字信息来辅助生成图片，适合需要真实信息的场景
      * `false`：不启用（默认）
    </ParamField>

    <ParamField type="boolean">
      启用 Google 图片搜索增强

      * `true`：除了文字搜索，还会搜索参考图片来辅助生成，适合需要视觉参考的场景
      * `false`：不启用（默认）

      **注意：** 需要配合 `google_search: true` 一起使用
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
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "赛博朋克风格的城市夜景，霓虹灯闪烁",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (图生图示例) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "将这张照片改为水彩画风格",
      "size": "1:1",
      "n": 1,
      "image_urls": ["https://example.com/photo.jpg"],
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Google 搜索增强示例) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "2024年最新款 iPhone 产品宣传图",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "2K",
        "google_search": true,
        "google_image_search": true
      }
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
          "model": "gemini-3.1-flash-image-preview",
          "prompt": "赛博朋克风格的城市夜景，霓虹灯闪烁",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "resolution": "2K"
          }
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
          "model": "gemini-3.1-flash-image-preview",
          "prompt": "将这张照片改为水彩画风格",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/photo.jpg"],
          "metadata": {
              "resolution": "2K"
          }
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
      model: 'gemini-3.1-flash-image-preview',
      prompt: '赛博朋克风格的城市夜景，霓虹灯闪烁',
      size: '16:9',
      n: 1,
      metadata: {
        resolution: '2K'
      }
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
      model: 'gemini-3.1-flash-image-preview',
      prompt: '将这张照片改为水彩画风格',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/photo.jpg'],
      metadata: {
        resolution: '2K'
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
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gemini-3.1-flash-image-preview",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
