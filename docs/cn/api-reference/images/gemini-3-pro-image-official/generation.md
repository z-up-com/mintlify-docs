# Gemini-3-Pro Official 图像生成
Source: https://docs.toapis.com/docs/cn/api-reference/images/gemini-3-pro-image-official/generation

POST https://toapis.com/v1/images/generations
使用 Google Gemini 3 Pro 官方直连模型生成图像，支持原生参数控制

* 直连 Google Vertex AI，使用 Gemini 3 Pro Image 原生 API
* 通过 model 参数选择 `gemini-3-pro-image-preview-official` 模型
* 支持文生图、图生图
* 支持 temperature、topP、maxOutputTokens 等原生生成参数
* 支持 resolution（1K/2K/4K）、personGeneration、imageOutputOptions 图像配置
* 支持 thinkingConfig 思考模式，提升复杂场景生成质量
* 支持 safetySettings 安全设置
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

  示例：`"gemini-3-pro-image-preview-official"`
</ParamField>

<ParamField type="string">
  图像生成的文本描述
</ParamField>

<ParamField type="string">
  图像宽高比

  支持的格式：

  * `1:1` - 正方形
  * `3:2` / `2:3`
  * `3:4` / `4:3`
  * `4:5` / `5:4`
  * `9:16` / `16:9`
  * `21:9`
</ParamField>

<ParamField type="integer">
  生成图像的数量

  固定为 1
</ParamField>

<ParamField type="string[]">
  参考图像 URL 数组，用于图生图或图像编辑

  **⚠️ 仅支持 URL 格式（不再支持 base64）**

  * 公开可访问的图片 URL（http\:// 或 https\://）
  * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL

  **限制：**

  * 最多 14 张图片
  * 单张图片不得超过 10MB
  * 支持格式：.jpeg, .jpg, .png, .webp
</ParamField>

<ParamField type="object">
  Vertex AI 原生扩展参数

  <Expandable title="显示 metadata 字段">
    <ParamField type="number">
      生成温度，控制输出的随机性

      取值范围：`0.0` - `2.0`
    </ParamField>

    <ParamField type="number">
      Top-P 采样参数

      取值范围：`0.0` - `1.0`，默认 `0.95`
    </ParamField>

    <ParamField type="integer">
      最大输出 token 数

      默认 `32768`
    </ParamField>

    <ParamField type="string">
      输出图像分辨率，后端自动映射为 Vertex AI 原生 imageSize

      可选值：`1K`、`2K`、`4K`，默认 `1K`
    </ParamField>

    <ParamField type="string">
      人物生成控制

      可选值：

      * `ALLOW_ALL` - 允许生成所有人物（包括成人和儿童）
      * `ALLOW_ADULT` - 仅允许生成成人
      * `ALLOW_NONE` - 禁止生成人物
    </ParamField>

    <ParamField type="object">
      图像输出格式配置

      <Expandable title="imageOutputOptions 字段">
        <ParamField type="string">
          输出图像格式

          可选值：`image/png`、`image/jpeg`、`image/webp`
        </ParamField>

        <ParamField type="integer">
          压缩质量（仅 JPEG 有效）
        </ParamField>
      </Expandable>
    </ParamField>

    <ParamField type="object">
      思考模式配置，启用后模型会先进行推理再生成图像，适合复杂场景

      <Expandable title="thinkingConfig 字段">
        <ParamField type="integer">
          思考 token 预算，控制模型思考的深度

          取值范围：`0` - `24576`，默认由模型自动决定
        </ParamField>

        <ParamField type="string">
          思考级别

          可选值：`LOW`、`MEDIUM`、`HIGH`、`MINIMAL`
        </ParamField>
      </Expandable>
    </ParamField>

    <ParamField type="array">
      安全设置数组，控制内容安全过滤级别

      <Expandable title="safetySettings 元素">
        <ParamField type="string">
          安全类别

          可选值：`HARM_CATEGORY_HATE_SPEECH`、`HARM_CATEGORY_DANGEROUS_CONTENT`、`HARM_CATEGORY_SEXUALLY_EXPLICIT`、`HARM_CATEGORY_HARASSMENT`
        </ParamField>

        <ParamField type="string">
          过滤阈值

          可选值：`OFF`、`BLOCK_LOW_AND_ABOVE`、`BLOCK_MEDIUM_AND_ABOVE`、`BLOCK_ONLY_HIGH`
        </ParamField>
      </Expandable>
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
      "model": "gemini-3-pro-image-preview-official",
      "prompt": "未来城市的天际线，霓虹灯光，赛博朋克风格",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "temperature": 1.0,
        "topP": 0.95,
        "resolution": "2K",
        "personGeneration": "ALLOW_ALL",
        "thinkingConfig": {
          "thinkingLevel": "HIGH"
        }
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
          "model": "gemini-3-pro-image-preview-official",
          "prompt": "未来城市的天际线，霓虹灯光，赛博朋克风格",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "temperature": 1.0,
              "topP": 0.95,
              "resolution": "2K",
              "personGeneration": "ALLOW_ALL",
              "thinkingConfig": {
                  "thinkingLevel": "HIGH"
              }
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
      model: 'gemini-3-pro-image-preview-official',
      prompt: '未来城市的天际线，霓虹灯光，赛博朋克风格',
      size: '16:9',
      n: 1,
      metadata: {
        temperature: 1.0,
        topP: 0.95,
        resolution: '2K',
        personGeneration: 'ALLOW_ALL',
        thinkingConfig: {
          thinkingLevel: 'HIGH'
        }
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
    "model": "gemini-3-pro-image-preview-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
