# Azure Sora 2 Video Generation

> Generate high-quality videos using Azure Sora 2 model

* Azure Sora 2 video generation model (official Azure OpenAI endpoint)
* Use model parameter to select `sora-2-official`
* Supports text-to-video, image-to-video, and remix
* Async task management, query results by task ID

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

<ParamField body="model" type="string" required>
  Video generation model name

  Supported models:

  * `sora-2-official` - Azure Sora 2 official model

  Example: `"sora-2-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Natural-language description for video generation

  Include shot type, subject, action, setting, lighting, and camera motion to reduce ambiguity. Keep it single-purpose for best adherence.

  Example: `"A golden retriever running on grass, sunny day"`
</ParamField>

<ParamField body="duration" type="integer" default="4">
  Video duration in seconds

  Supported values:

  * `4` - 4 seconds
  * `8` - 8 seconds
  * `12` - 12 seconds

  Default: `4`

  Example: `12`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="9:16">
  Video aspect ratio

  Supported formats:

  * `16:9` (Landscape, 1280×720)
  * `9:16` (Portrait, 720×1280)

  Default: `9:16`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Array of reference image URLs for image-to-video generation

  **⚠️ URL format only**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs
  * Only the first image will be used as reference
  * Supported formats: JPEG, PNG, WebP
  * **Image size must be 1280×720 (16:9) or 720×1280 (9:16)**
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
  Task metadata including size and duration
</ResponseField>

<RequestExample>
  ```bash cURL (Text-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "A golden retriever running on grass, sunny day",
      "duration": 12,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL (Image-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "Make this dog come alive, running on grass",
      "image_urls": ["https://example.com/dog.jpg"],
      "duration": 8,
      "aspect_ratio": "16:9"
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2-official",
          "prompt": "A golden retriever running on grass, sunny day",
          "duration": 12,
          "aspect_ratio": "16:9"
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2-official',
      prompt: 'A golden retriever running on grass, sunny day',
      duration: 12,
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
    "model": "sora-2-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "1280x720",
      "seconds": "12"
    }
  }
  ```
</ResponseExample>
