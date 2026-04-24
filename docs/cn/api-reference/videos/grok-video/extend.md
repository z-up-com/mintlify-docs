---
title: "Grok 视频延伸"
---
Source: https://docs.toapis.com/docs/cn/api-reference/videos/grok-video/extend

POST https://toapis.com/v1/videos/{video_id}/extend
延伸已生成的 Grok 视频

* Grok 视频延伸功能
* 基于现有视频延长时长
* 异步任务管理，通过任务 ID 查询结果

## Path Parameters

<ParamField type="string">
  原始视频任务 ID

  这是之前视频生成请求返回的任务 ID
</ParamField>

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
  视频延伸模型名称

  示例：`"grok-video-3"`
</ParamField>

<ParamField type="string">
  延伸视频的文本描述
</ParamField>

<ParamField type="string">
  视频尺寸
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
    --url https://toapis.com/v1/videos/video_abc123def456/extend \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "继续延伸这个场景，让狗跑向远方"
    }'
  ```

  ```python Python theme={null}
  import requests

  video_id = "video_abc123def456"

  response = requests.post(
      f"https://toapis.com/v1/videos/{video_id}/extend",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "grok-video-3",
          "prompt": "继续延伸这个场景，让狗跑向远方"
      }
  )

  task = response.json()
  print(f"任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const videoId = 'video_abc123def456';

  const response = await fetch(`https://toapis.com/v1/videos/${videoId}/extend`, {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'grok-video-3',
      prompt: '继续延伸这个场景，让狗跑向远方'
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
    "id": "video_extend_abc123",
    "object": "generation.task",
    "model": "grok-video-3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
