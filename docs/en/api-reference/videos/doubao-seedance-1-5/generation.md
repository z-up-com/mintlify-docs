# Doubao SeeDance 1.5 Pro Video Generation

> Generate videos using ByteDance Doubao SeeDance 1.5 Pro model

* ByteDance Doubao video generation model 1.5 Pro version
* Supports text-to-video generation
* Supports first frame / last frame / multi-reference image control (1-4 reference images)
* Supports audio generation (1.5 Pro exclusive feature)
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

<ParamField body="model" type="string" default="doubao-seedance-1-5-pro" required>
  Video generation model name

  Available models:

  * `doubao-seedance-1-5-pro` - 1.5 Pro version, supports audio generation and multi-reference images
</ParamField>

<ParamField body="prompt" type="string" required>
  Video content description

  Describe scenes, actions, styles in detail for better generation results

  Example: `"Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand"`
</ParamField>

<ParamField body="duration" type="integer" default={5}>
  Video duration (seconds)

  Supported range: `4` \~ `12` seconds

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

## Resolution and Aspect Ratio Combinations

| Resolution | Supported Aspect Ratios         | Notes                   |
| ---------- | ------------------------------- | ----------------------- |
| 480p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | All supported           |
| 720p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | All supported (default) |

<Warning>
  **1.5 Pro Resolution Limitation**: 1.5 Pro does not support 1080p resolution, maximum is 720p
</Warning>

<ParamField body="image_urls" type="string[]">
  Image URL array for image-to-video generation

  Auto role assignment rules:

  * 1 image = first frame
  * 2 images = first frame + last frame
  * 3+ images = first frame + last frame + reference images

  Example: `["https://example.com/first.png", "https://example.com/last.png"]`

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
      * `last_frame` - Last frame image, as video ending frame (only one supported)
      * `reference_image` - Reference image, for guiding video style (supports 1-4 images)
    </ParamField>
  </Expandable>

  Example:

  ```json theme={null}
  [
    {"url": "https://example.com/start.png", "role": "first_frame"},
    {"url": "https://example.com/end.png", "role": "last_frame"},
    {"url": "https://example.com/ref1.png", "role": "reference_image"},
    {"url": "https://example.com/ref2.png", "role": "reference_image"}
  ]
  ```

  <Warning>
    * `image_urls` and `image_with_roles` cannot be used together
    * First and last frame support only one image each
    * Reference images use `reference_image` role (Note: 1.0 version uses `reference`)
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters

  <Expandable title="Field Description">
    <ParamField body="resolution" type="string" default="720p">
      Video resolution

      Options:

      * `480p` - Standard definition
      * `720p` - High definition (default)

      1.5 Pro does not support 1080p
    </ParamField>

    <ParamField body="seed" type="integer">
      Seed integer for controlling generation randomness

      Range: `-1` \~ `2^32-1`

      Same seed value produces similar results, but not guaranteed to be identical
    </ParamField>

    <ParamField body="audio" type="boolean" default={false}>
      Whether to generate audio

      When set to `true`, the video will include AI-generated accompanying audio

      **1.5 Pro exclusive feature**
    </ParamField>

    <ParamField body="camerafixed" type="boolean" default={false}>
      Whether to fix camera position

      When set to `true`, the camera position remains fixed
    </ParamField>
  </Expandable>
</ParamField>

## Differences from 1.0 Version

| Feature               | 1.0 fast/quality      | 1.5 Pro                            |
| --------------------- | --------------------- | ---------------------------------- |
| Default resolution    | 1080p                 | **720p**                           |
| Supported resolutions | 480p/720p/1080p       | **480p/720p**                      |
| Duration range        | 2-12s                 | **4-12s**                          |
| Audio generation      | Not supported         | **Supported**                      |
| Reference image role  | `reference` (1 image) | **`reference_image` (1-4 images)** |

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
  ```bash cURL (Text-to-Video with Audio) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand",
      "duration": 5,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p",
        "audio": true
      }
    }'
  ```

  ```bash cURL (High-Quality Portrait Short Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "A girl spinning under cherry blossom trees, petals falling with the wind",
      "duration": 5,
      "aspect_ratio": "9:16",
      "metadata": {
        "resolution": "720p",
        "audio": true
      }
    }'
  ```

  ```bash cURL (Multi-Reference Image Style Control) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "Generate video in the style of reference images, maintain consistent visual effect",
      "image_with_roles": [
        {"url": "https://example.com/first.png", "role": "first_frame"},
        {"url": "https://example.com/ref1.png", "role": "reference_image"},
        {"url": "https://example.com/ref2.png", "role": "reference_image"}
      ],
      "metadata": {
        "audio": true
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
          "model": "doubao-seedance-1-5-pro",
          "prompt": "Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand",
          "duration": 5,
          "aspect_ratio": "16:9",
          "metadata": {
              "resolution": "720p",
              "audio": True
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
      model: 'doubao-seedance-1-5-pro',
      prompt: 'Sunset at the beach, golden sunlight on the sea, waves gently hitting the sand',
      duration: 5,
      aspect_ratio: '16:9',
      metadata: {
        resolution: '720p',
        audio: true
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
    "id": "task_vid_xyz789ghi012",
    "object": "generation.task",
    "model": "doubao-seedance-1-5-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
