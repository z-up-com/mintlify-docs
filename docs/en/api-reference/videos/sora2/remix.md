> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Sora2 Video Remix

> Edit and modify generated videos

* Sora2 video remix feature
* Remix and edit existing generated videos
* Supports secondary creation based on existing videos
* Asynchronous processing mode, returns task ID for subsequent queries

## Path Parameters

<ParamField path="video_id" type="string" required>
  Original video task ID

  This is the task ID returned from a previous video generation request
</ParamField>

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get your API Key from the [API Key Management Page](https://toapis.com/console/token)

  Add to request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="sora-2" required>
  Video remix model name

  Supported models:

  * `sora-2` - Standard version
  * `sora-2-pro` - Professional version, supports longer duration
  * `sora-2-vip` - VIP version, higher priority

  Example: `"sora-2"` or `"sora-2-pro"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Remix instruction description
</ParamField>

<ParamField body="duration" type="integer">
  Video duration in seconds

  * `sora-2`: Supports 10 or 15 seconds
  * `sora-2-pro`: Supports 15 seconds (HD) or 25 seconds

  Example: `15`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Video aspect ratio

  Supported ratios:

  * `16:9` - Standard widescreen (recommended)
  * `9:16` - Portrait mode
  * `1:1` - Square

  Example: `"16:9"`
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique task identifier for status queries
</ResponseField>

<ResponseField name="object" type="string">
  Object type, always `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Model name used
</ResponseField>

<ResponseField name="status" type="string">
  Task status

  * `queued` - Queued for processing
  * `in_progress` - Processing
  * `completed` - Successfully completed
  * `failed` - Failed
</ResponseField>

<ResponseField name="progress" type="integer">
  Task progress percentage (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation timestamp (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Task metadata
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
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
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
  console.log(`Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
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
