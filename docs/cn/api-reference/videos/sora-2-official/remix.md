# Azure Sora 2 Remix
Source: https://docs.toapis.com/docs/cn/api-reference/videos/sora-2-official/remix

POST https://toapis.com/v1/videos/{video_id}/remix
使用 Azure Sora 2 模型对现有视频进行重混

Remix 功能允许您修改现有视频的特定方面，同时保留其核心元素。通过引用之前成功完成的视频 ID，并提供更新的提示词，系统会维持原始视频的框架、场景转换和视觉布局，同时实现您请求的更改。

<Tip>
  为获得最佳效果，请将修改限制在一个明确的调整上。精确、聚焦的编辑能更好地保持与源素材的一致性，并最大限度地减少生成视觉缺陷的可能性。
</Tip>

## Authorizations

<ParamField type="string">
  所有接口均需要使用 Bearer Token 进行认证

  获取 API Key：访问 [API Key 管理页面](https://toapis.com/console/token) 获取您的 API Key

  使用时在请求头中添加：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Path Parameters

<ParamField type="string">
  之前已完成的视频 ID，用于进行 remix

  示例：`"video_01K8SGYNNNVBQTXNR4MM964S7K"`
</ParamField>

## Body

<ParamField type="string">
  视频生成模型名称

  Azure Sora 2 remix 必须使用 `sora-2-official`

  示例：`"sora-2-official"`
</ParamField>

<ParamField type="string">
  重混视频的更新文本描述

  描述您想对原始视频进行的具体更改。保持精确并专注于一个明确的修改。

  示例：`"将色彩调整为青色、沙色和铁锈色，配合温暖的背光"`
</ParamField>

## Response

<ResponseField name="id" type="string">
  重混视频的唯一任务标识符
</ResponseField>

<ResponseField name="object" type="string">
  对象类型，固定为 `video`
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

<ResponseField name="remixed_from_video_id" type="string">
  被重混的原始视频 ID
</ResponseField>

<ResponseField name="seconds" type="string">
  视频时长（秒）
</ResponseField>

<ResponseField name="size" type="string">
  视频分辨率（如 "1280x720"）
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/video_01K8SGYNNNVBQTXNR4MM964S7K/remix \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "将色彩调整为青色、沙色和铁锈色，配合温暖的背光"
    }'
  ```

  ```python Python theme={null}
  import requests

  video_id = "video_01K8SGYNNNVBQTXNR4MM964S7K"

  response = requests.post(
      f"https://toapis.com/v1/videos/{video_id}/remix",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2-official",
          "prompt": "将色彩调整为青色、沙色和铁锈色，配合温暖的背光"
      }
  )

  task = response.json()
  print(f"Remix 任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  print(f"重混自: {task.get('remixed_from_video_id')}")
  ```

  ```javascript JavaScript theme={null}
  const videoId = "video_01K8SGYNNNVBQTXNR4MM964S7K";

  const response = await fetch(`https://toapis.com/v1/videos/${videoId}/remix`, {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2-official',
      prompt: '将色彩调整为青色、沙色和铁锈色，配合温暖的背光'
    })
  });

  const task = await response.json();
  console.log(`Remix 任务 ID: ${task.id}`);
  console.log(`状态: ${task.status}`);
  console.log(`重混自: ${task.remixed_from_video_id}`);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_68ff7cef76cc8190b7eab9395e936d9e",
    "object": "video",
    "model": "sora-2-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1761574127,
    "remixed_from_video_id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "seconds": "8",
    "size": "1280x720"
  }
  ```
</ResponseExample>
