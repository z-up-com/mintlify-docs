> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Grok Video Extend

> Extend generated Grok videos

* Grok Video extend feature
* Extend the duration of existing videos
* Async task management, query results via task ID

## Path Parameters

<ParamField path="video_id" type="string" required>
  Original video task ID

  This is the task ID returned from a previous video generation request
</ParamField>

## Authorizations

<ParamField header="Authorization" type="string" required>
  All API requests require Bearer Token authentication

  Get API Key: Visit the [API Key Management Page](https://toapis.com/console/token) to obtain your API Key

  Add to request headers:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="grok-video-3" required>
  Video extend model name

  Example: `"grok-video-3"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for video extension
</ParamField>

<ParamField body="size" type="string">
  Video size
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique task identifier for querying task status
</ResponseField>

<ResponseField name="object" type="string">
  Object type, always `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Model name used
</ResponseField>

<ResponseField name="status" type="string">
  Task status

  * `queued` - Waiting to be processed
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
    --url https://toapis.com/v1/videos/video_abc123def456/extend \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "Continue the scene, make the dog run towards the horizon"
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
          "prompt": "Continue the scene, make the dog run towards the horizon"
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
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
      prompt: 'Continue the scene, make the dog run towards the horizon'
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
