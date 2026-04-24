# Grok Imagine 1.0 Video Generation

> Async video generation with grok-imagine-1.0-video

* Unified video generation endpoint
* Async task mode, returns a task ID
* Supports text-to-video and image-to-video

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get API Key from [API Key Management](https://toapis.com/console/token)

  Header example:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="grok-imagine-1.0-video" required>
  Model name:

  * `grok-imagine-1.0-video`
</ParamField>

<ParamField body="prompt" type="string" required>
  Video content prompt, multilingual supported
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Video aspect ratio

  Supported formats:

  * `16:9` - Landscape (default)
  * `9:16` - Portrait
  * `1:1` - Square
  * `3:2` - Landscape
  * `2:3` - Portrait
</ParamField>

<ParamField body="duration" type="integer" default={6}>
  Video duration in seconds

  Range: 6-30 (minimum 6 seconds, maximum 30 seconds)

  ⚠️ Note: use a plain number (for example `6`), do not wrap it in quotes, otherwise an error will occur
</ParamField>

<ParamField body="quality" type="string" default="480p">
  Video quality

  Supported values:

  * `480p` (default)
  * `720p`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL array (for image-to-video)

  Limits:

  * Maximum 7 images
  * Public URL only
  * Base64 not supported
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique task ID
</ResponseField>

<ResponseField name="object" type="string">
  Always `generation.task`
</ResponseField>

<ResponseField name="status" type="string">
  Task status: `queued` / `in_progress` / `completed` / `failed`
</ResponseField>

<RequestExample>
  ```bash cURL (Text-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0-video",
      "prompt": "A dog running on the beach, sunny weather, slow motion",
      "aspect_ratio": "16:9",
      "duration": 6,
      "quality": "720p"
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0-video",
          "prompt": "A dog running on the beach, sunny weather, slow motion",
          "aspect_ratio": "16:9",
          "duration": 6,
          "quality": "720p",
      },
  )

  print(response.status_code)
  print(response.json())
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0-video',
      prompt: 'A dog running on the beach, sunny weather, slow motion',
      aspect_ratio: '16:9',
      duration: 6,
      quality: '720p',
    }),
  });

  console.log(response.status);
  console.log(await response.json());
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_01JNXXXXXXXXXXXXXXXXXX",
    "object": "generation.task",
    "model": "grok-imagine-1.0-video",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
