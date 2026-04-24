# Wan2.6 Flash Video Generation

> Generate videos using Alibaba Cloud Wan2.6 Flash model — faster image-to-video and reference video generation

* Alibaba Cloud Wanxiang Flash (accelerated) video generation model
* Supports **two modes**: Image-to-Video and Reference Video (r2v)
* **Text-only generation is not supported** — you must provide an image or reference video URLs
* Supports 720p/1080p resolution; audio on/off option affects billing
* Faster generation speed — ideal for quick previews and iteration

<Warning>
  **Image or reference video URL is required**: You must provide either `image_urls` (image-to-video) or `metadata.reference_urls` (reference video). Sending a prompt-only request will return an error.
</Warning>

## Routing Logic

The server automatically selects the upstream model based on your request parameters:

| Parameters Provided                    | Upstream Mode                     |
| -------------------------------------- | --------------------------------- |
| `metadata.reference_urls` (video URLs) | Reference Video Flash (r2v-flash) |
| `image_urls` (image)                   | Image-to-Video Flash (i2v-flash)  |
| `prompt` only                          | ❌ Returns error                   |

## Authentication

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get your API Key from the [API Key Management Page](https://toapis.com/console/token)

  Add to request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Request Parameters

<ParamField body="model" type="string" required>
  Video generation model name, fixed as `wan2.6-flash`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL array for image-to-video mode (only supports 1 image)

  **Required if `metadata.reference_urls` is not provided**

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  Example: `["https://example.com/image.jpg"]`
</ParamField>

<ParamField body="prompt" type="string">
  Video content description (optional)

  Describe the expected motion or style to guide the model

  Example: `"The person comes alive and smiles at the camera"`
</ParamField>

<ParamField body="resolution" type="string" default="1080p">
  Video resolution

  Available values:

  * `720p` - Standard definition
  * `1080p` - High definition (default)

  Default: `1080p`

  Different resolutions have different prices — refer to the model marketplace for details.
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

  Supported values: `5`, `10`, `15`

  Default: `5`
</ParamField>

<ParamField body="audio" type="boolean" default="true">
  Whether to generate audio in the video

  * `true` - Generate video with audio (default)
  * `false` - Generate silent video

  **⚠️ Audio and silent videos are billed at different rates — choose accordingly**

  Default: `true`
</ParamField>

<ParamField body="watermark" type="boolean">
  Add an Alibaba Cloud watermark to the generated video
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters

  <Expandable title="Show metadata fields">
    <ParamField body="reference_urls" type="string[]">
      **Reference Video (r2v) mode** — array of reference video URLs

      When provided, the server routes to the reference video Flash upstream model (wan2.6-r2v-flash). The model uses these videos to generate new content with consistent characters or scenes.

      * Each entry must be a publicly accessible video URL (http\:// or https\://)

      Example: `["https://cdn.example.com/ref-character.mp4"]`

      **Note:** Cannot be combined with `image_urls`
    </ParamField>
  </Expandable>
</ParamField>

## Audio vs Silent Pricing

| Mode                       | 720p Unit Price | 1080p Unit Price |
| -------------------------- | --------------- | ---------------- |
| With audio (`audio: true`) | ¥0.3 / second   | ¥0.5 / second    |
| Silent (`audio: false`)    | ¥0.15 / second  | ¥0.25 / second   |

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

## Usage Scenarios

### Scenario 1: Image-to-Video (with audio)

```json theme={null}
{
  "model": "wan2.6-flash",
  "image_urls": ["https://example.com/photo.jpg"],
  "prompt": "The person comes alive and smiles",
  "resolution": "1080p",
  "duration": 5,
  "audio": true
}
```

### Scenario 2: Image-to-Video (silent, lower cost)

```json theme={null}
{
  "model": "wan2.6-flash",
  "image_urls": ["https://example.com/photo.jpg"],
  "resolution": "720p",
  "duration": 5,
  "audio": false
}
```

### Scenario 3: Reference Video (r2v-flash)

```json theme={null}
{
  "model": "wan2.6-flash",
  "prompt": "The character is watching a movie on the couch",
  "metadata": {
    "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
  },
  "resolution": "1080p",
  "duration": 5,
  "audio": true
}
```

<Note>
  **Querying Task Results**

  Video generation is an asynchronous task. After submission, a `task_id` is returned. Use the [Get Task Status](/en/api-reference/tasks/status) interface to query generation progress and results.
</Note>

<RequestExample>
  ```bash cURL (Image-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6-flash",
      "image_urls": ["https://example.com/photo.jpg"],
      "prompt": "The person comes alive and smiles",
      "resolution": "1080p",
      "duration": 5,
      "audio": true
    }'
  ```

  ```bash cURL (Reference Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6-flash",
      "prompt": "The character is watching a movie on the couch",
      "metadata": {
        "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
      },
      "resolution": "1080p",
      "duration": 5,
      "audio": true
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
          "model": "wan2.6-flash",
          "image_urls": ["https://example.com/photo.jpg"],
          "prompt": "The person comes alive and smiles",
          "resolution": "1080p",
          "duration": 5,
          "audio": True
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
      model: 'wan2.6-flash',
      image_urls: ['https://example.com/photo.jpg'],
      prompt: 'The person comes alive and smiles',
      resolution: '1080p',
      duration: 5,
      audio: true
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
    "id": "video_01K2ABJPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "wan2.6-flash",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
