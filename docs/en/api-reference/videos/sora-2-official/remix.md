> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Azure Sora 2 Remix

> Remix an existing video using Azure Sora 2 model

The remix feature allows you to modify specific aspects of an existing video while preserving its core elements. By referencing the previous video ID from a successfully completed generation, and supplying an updated prompt, the system maintains the original video's framework, scene transitions, and visual layout while implementing your requested changes.

<Tip>
  For optimal results, limit your modifications to one clearly articulated adjustment. Narrow, precise edits retain greater fidelity to the source material and minimize the likelihood of generating visual defects.
</Tip>

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get your API Key from the [API Key Management Page](https://toapis.com/console/token)

  Add to request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Path Parameters

<ParamField path="video_id" type="string" required>
  The ID of a previously completed video to remix

  Example: `"video_01K8SGYNNNVBQTXNR4MM964S7K"`
</ParamField>

## Body

<ParamField body="model" type="string" required>
  Video generation model name

  Must use `sora-2-official` for Azure Sora 2 remix

  Example: `"sora-2-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Updated text description for the remixed video

  Describe the specific changes you want to make to the original video. Be precise and focused on one clear modification.

  Example: `"Shift the color palette to teal, sand, and rust, with a warm backlight"`
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique task identifier for the remixed video
</ResponseField>

<ResponseField name="object" type="string">
  Object type, always `video`
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

<ResponseField name="remixed_from_video_id" type="string">
  The original video ID that was remixed
</ResponseField>

<ResponseField name="seconds" type="string">
  Video duration in seconds
</ResponseField>

<ResponseField name="size" type="string">
  Video resolution (e.g., "1280x720")
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/video_01K8SGYNNNVBQTXNR4MM964S7K/remix \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "Shift the color palette to teal, sand, and rust, with a warm backlight"
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
          "prompt": "Shift the color palette to teal, sand, and rust, with a warm backlight"
      }
  )

  task = response.json()
  print(f"Remix Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  print(f"Remixed from: {task.get('remixed_from_video_id')}")
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
      prompt: 'Shift the color palette to teal, sand, and rust, with a warm backlight'
    })
  });

  const task = await response.json();
  console.log(`Remix Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
  console.log(`Remixed from: ${task.remixed_from_video_id}`);
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
