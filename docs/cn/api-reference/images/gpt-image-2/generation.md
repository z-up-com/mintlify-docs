# GPT-Image-2 图像生成
Source: https://docs.toapis.com/docs/cn/api-reference/images/gpt-image-2/generation

POST https://toapis.com/v1/images/generations
使用 gpt-image-2 模型生成图像，支持文生图和 reference_images 图生图

* 统一的图像生成 API 接口
* 通过 `model` 参数选择 `gpt-image-2`
* 支持文生图、单图参考和多图参考生成
* 异步任务管理，通过任务 ID 查询结果

<Warning>
  **重要变更**：为了更好的性能和成本控制，我们不再支持在 `image_urls` / `reference_images` 中直接传入 base64 图片数据。请先使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传图片，获取 URL 后再调用本接口。
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

  示例：`"gpt-image-2"`
</ParamField>

<ParamField type="string">
  图像生成的文本描述

  最长 4000 个字符
</ParamField>

<ParamField type="string">
  输出图像尺寸

  常用值：

  * `1024x1024`
  * `1536x1024`
  * `1024x1536`
</ParamField>

<ParamField type="integer">
  生成图像的数量

  默认：1
</ParamField>

<ParamField type="string">
  返回格式

  固定返回图片 URL，推荐使用 `url`
</ParamField>

<ParamField type="string[]">
  参考图 URL 列表，用于图生图

  **⚠️ 仅支持 URL 格式（不再支持 base64）**

  * 公开可访问的图片 URL（http\:// 或 https\://）
  * 可使用 [上传图片接口](/docs/cn/api-reference/uploads/images) 上传本地图片获取 URL
  * 支持单图和多图参考
</ParamField>

<ParamField type="string[]">
  向后兼容的参考图字段

  在 ToAPIs 中会自动归一化为 `reference_images`
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

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-image-2",
      "prompt": "生成一张未来城市夜景海报，霓虹灯，电影感构图",
      "n": 1,
      "size": "1024x1024",
      "response_format": "url"
    }'
  ```

  ```bash cURL (图生图) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-image-2",
      "prompt": "保留主体结构，把画面改成赛博朋克风格，增强光影和细节",
      "reference_images": [
        "https://example.com/source.png"
      ],
      "response_format": "url"
    }'
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gpt-image-2',
      prompt: 'Generate a futuristic city night poster with neon lighting and cinematic composition',
      n: 1,
      size: '1024x1024',
      response_format: 'url'
    })
  });

  const task = await response.json();
  console.log(task.id, task.status);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gpt-image-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
