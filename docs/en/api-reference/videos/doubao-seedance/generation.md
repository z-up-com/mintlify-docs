---
title: "Doubao SeeDance Video Generation"
---
> Generate videos using ByteDance Doubao SeeDance model

* ByteDance Doubao video generation model
* Use model parameter to select `doubao-seedance-1-0-pro-fast` or `doubao-seedance-1-0-pro-quality` model
* Supports text-to-video generation
* Supports first frame / last frame / reference image control
* Async task management, query results by task ID

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

<ParamField body="model" type="string" default="doubao-seedance-1-0-pro-fast" required>
  Video generation model name

  Available models:

  * `doubao-seedance-1-0-pro-fast` - Fast version (40-90 seconds generation)
  * `doubao-seedance-1-0-pro-quality` - Quality version (90-300 seconds generation)
</ParamField>

<ParamField body="prompt" type="string" required>
  Video content description

  Describe scenes, actions, styles in detail for better generation results

  Example: `"Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand"`
</ParamField>

<ParamField body="duration" type="integer" default={5}>
  Video duration (seconds)

  Supported range: `2` \~ `12` seconds

  Default: `5`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Video aspect ratio

  Options:

  * `16:9` - Landscape
  * `9:16` - Portrait
  * `1:1` - Square
  * `4:3` - Traditional ratio
  * `3:4` - Vertical traditional ratio
  * `21:9` - Ultra-wide

  Default: `16:9`
</ParamField>

<ParamField body="resolution" type="string" default="720p">
  Video resolution

  Options:

  * `480p` - Standard definition
  * `720p` - High definition
  * `1080p` - Full HD

  Default: `720p`

  **1080p Limitation**: When using reference images (`role: reference` in `image_with_roles`), 1080p resolution is not supported
</ParamField>

## Resolution and Aspect Ratio Combinations

| Resolution | Supported Aspect Ratios         | Notes                         |
| ---------- | ------------------------------- | ----------------------------- |
| 480p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | All supported                 |
| 720p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | All supported                 |
| 1080p      | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Reference image not supported |

<ParamField body="image_urls" type="string[]">
  First frame image URL array for image-to-video generation

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  Example: `["https://example.com/cat.png"]`

  `image_urls` and `image_with_roles` cannot be used together
</ParamField>

<ParamField body="image_with_roles" type="array">
  Image array with roles for more precise control

  <Expandable title="Field Description">
    <ParamField body="url" type="string" required>
      Image URL address
    </ParamField>

    <ParamField body="role" type="string" required>
      Image role

      Options:

      * `first_frame` - First frame image, as video starting frame (only one supported)
      * `last_frame` - Last frame image, as video ending frame (**quality version only**, only one supported)
      * `reference` - Reference image, for guiding video style (only one supported)
    </ParamField>
  </Expandable>

  Example:

  ```json theme={null}
  [
    {"url": "https://example.com/start.png", "role": "first_frame"},
    {"url": "https://example.com/end.png", "role": "last_frame"}
  ]
  ```

  <Warning>
    * `image_urls` and `image_with_roles` cannot be used together
    * Only one image per role is supported
    * `last_frame` is only supported by `doubao-seedance-1-0-pro-quality` version, fast version does not support first and last frame together
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters (optional)

  <Expandable title="Field Description">
    <ParamField body="seed" type="integer">
      Seed integer for controlling generation randomness

      Range: `-1` \~ `2^32-1`

      Same seed value produces similar results, but not guaranteed to be identical
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
  ```bash cURL (Quick Preview Landscape Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-fast",
      "prompt": "Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand",
      "duration": 5,
      "aspect_ratio": "16:9",
      "resolution": "720p"
    }'
  ```

  ```bash cURL (High-Quality Portrait Short Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-quality",
      "prompt": "A girl spinning under cherry blossom trees, petals falling with the wind",
      "duration": 5,
      "aspect_ratio": "9:16",
      "resolution": "1080p"
    }'
  ```

  ```bash cURL (Dynamic Transition Effect - First/Last Frame) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-quality",
      "prompt": "Scene transitions from day to night, city lights gradually turning on",
      "duration": 5,
      "image_with_roles": [
        {"url": "https://example.com/day.png", "role": "first_frame"},
        {"url": "https://example.com/night.png", "role": "last_frame"}
      ]
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
          "model": "doubao-seedance-1-0-pro-fast",
          "prompt": "Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand",
          "duration": 5,
          "aspect_ratio": "16:9",
          "resolution": "720p"
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
      model: 'doubao-seedance-1-0-pro-fast',
      prompt: 'Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand',
      duration: 5,
      aspect_ratio: '16:9',
      resolution: '720p'
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
    "id": "task_vid_xyz789ghi012",
    "object": "generation.task",
    "model": "doubao-seedance-1-0-pro-fast",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
