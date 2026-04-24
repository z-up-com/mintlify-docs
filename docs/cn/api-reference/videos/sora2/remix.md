# Sora2 视频混音
Source: https://docs.toapis.com/docs/cn/api-reference/videos/sora2/remix

POST https://toapis.com/v1/videos/{video_id}/remix
编辑和修改已生成的视频

* Sora2 视频混音功能
* 混音和编辑已生成的视频
* 支持基于现有视频进行二次创作
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
  视频混音模型名称

  支持的模型：

  * `sora-2` - 标准版
  * `sora-2-pro` - 专业版，支持更长时长
  * `sora-2-vip` - VIP版，更高优先级

  示例：`"sora-2"` 或 `"sora-2-pro"`
</ParamField>

<ParamField type="string">
  混音指令描述
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  * `sora-2`：支持 10 或 15 秒
  * `sora-2-pro`：支持 15 秒（HD）或 25 秒

  示例：`15`
</ParamField>

<ParamField type="string">
  视频宽高比

  支持的格式：

  * `16:9` - 标准宽屏（推荐）
  * `9:16` - 竖屏模式
  * `1:1` - 方形

  示例：`"16:9"`
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
    --url https://toapis.com/v1/videos/task_01K8SGYNNNVBQTXNR4MM964S7K/remix \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Add a puppy playing in the scene",
      "duration": 15,
      "aspect_ratio": "16:9"
    }'
  ```

  ```python Python theme={null}
  import requests

  video_id = "task_01K8SGYNNNVBQTXNR4MM964S7K"

  response = requests.post(
      f"https://toapis.com/v1/videos/{video_id}/remix",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "prompt": "Add a puppy playing in the scene",
          "duration": 15,
          "aspect_ratio": "16:9"
      }
  )

  task = response.json()
  print(f"任务 ID: {task['id']}")
  print(f"状态: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const videoId = 'task_01K8SGYNNNVBQTXNR4MM964S7K';

  const response = await fetch(`https://toapis.com/v1/videos/${videoId}/remix`, {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      prompt: 'Add a puppy playing in the scene',
      duration: 15,
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
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "720x720"
    }
  }
  ```
</ResponseExample>
