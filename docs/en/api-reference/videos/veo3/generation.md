---
title: "Veo3 Video Generation"
---
> Generate high-quality videos using Google Veo3 model, supports async task management

* Asynchronous processing mode, returns task ID for subsequent queries
* Supports multiple generation modes including text-to-video and image-to-video
* Generated video links are valid for 24 hours, please save them promptly

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `image_urls`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) first to upload images and get URLs, then call this endpoint.
</Warning>

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

<ParamField body="model" type="string" default="veo3.1-fast" required>
  Video generation model name

  Available models:

  * `veo3.1-fast` - Fast generation model, suitable for quick preview and iteration
  * `veo3.1-quality` - High-quality generation model, suitable for final production
  * `veo3.1-lite` - Lightweight generation model, suitable for lower-cost fast generation

  Example: `"veo3.1-fast"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for video generation
</ParamField>

<ParamField body="duration" type="integer">
  Video duration (seconds)

  Fixed value: `8` (VEO3 only supports 8-second duration)
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Video resolution

  Supported formats:

  * `16:9` (landscape)
  * `9:16` (portrait)
</ParamField>

<ParamField body="image_urls" type="string[]">
  Array of reference image URLs for image-to-video generation

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limits:**

  * Maximum file size: 10MB
  * Supported formats: .jpeg, .jpg, .png, .webp
</ParamField>

<ParamField body="metadata" type="object">
  VEO3-specific extended parameters

  <Expandable title="Show metadata fields">
    <ParamField body="generation_type" type="string">
      Video generation type

      Supported types:

      * `frame` - Frame-to-video (FL mode)
      * `reference` - Reference image-to-video

      If not provided, automatically determined by image count: 2 images for frame mode, 3 images for reference mode

      **Note: `veo3.1-quality` model does not support `reference` mode**
    </ParamField>

    <ParamField body="resolution" type="string">
      Video resolution

      Supported values:

      * `720p` (default)
      * `1080p`
      * `4k`
    </ParamField>

    <ParamField body="enable_gif" type="boolean">
      Enable GIF output format. Default: `false`

      Note: GIF cannot be used with 1080p or 4k resolution
    </ParamField>
  </Expandable>
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
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "veo3.1-fast",
      "prompt": "Dolphins jumping in the blue ocean",
      "duration": 8,
      "aspect_ratio": "16:9",
      "image_urls": ["https://example.com/start-frame.jpg", "https://example.com/end-frame.jpg"],
      "metadata": {
        "generation_type": "frame",
        "resolution": "1080p",
        "enable_gif": false
      }
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
          "model": "veo3.1-fast",
          "prompt": "Dolphins jumping in the blue ocean",
          "duration": 8,
          "aspect_ratio": "16:9",
          "image_urls": ["https://example.com/start-frame.jpg", "https://example.com/end-frame.jpg"],
          "metadata": {
              "generation_type": "frame",
              "resolution": "1080p",
              "enable_gif": False
          }
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
      model: 'veo3.1-fast',
      prompt: 'Dolphins jumping in the blue ocean',
      duration: 8,
      aspect_ratio: '16:9',
      image_urls: ['https://example.com/start-frame.jpg', 'https://example.com/end-frame.jpg'],
      metadata: {
        generation_type: 'frame',
        resolution: '1080p',
        enable_gif: false
      }
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
    "model": "veo3.1-fast",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "aspect_ratio": "16:9"
    }
  }
  ```
</ResponseExample>
