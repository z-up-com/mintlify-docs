---
title: "Vidu Q3 Pro Video Generation"
---
> Generate high-quality videos using Vidu Q3 Pro, supporting text-to-video, image-to-video, and first-last frame control

* Async processing mode, returns task ID for subsequent queries
* Supports text-to-video, image-to-video, first-last frame video generation
* Supports 540p / 720p / 1080p resolution
* Duration range 1-16 seconds, audio enabled by default

## Authorization

<ParamField header="Authorization" type="string" required>
  All API endpoints require Bearer Token authentication

  Get your API Key: Visit the [API Key Management Page](https://toapis.com/console/token) to get your API Key

  Add it to the request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Request Parameters

<ParamField body="model" type="string" required>
  Video generation model name, fixed as `viduq3-pro`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text prompt, maximum **2000 characters**

  Required for text-to-video. Optional for image-to-video and first-last frame modes.

  Example: `"A cat playing piano, camera slowly zooms in"`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

  Range: `1` to `16`

  Default: `5`
</ParamField>

<ParamField body="resolution" type="string" default="720p">
  Video resolution

  Options:

  * `540p` - Standard definition
  * `720p` - HD (default)
  * `1080p` - Full HD

  Default: `720p`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Video aspect ratio (only for text-to-video mode)

  Options:

  * `16:9` - Landscape
  * `9:16` - Portrait
  * `4:3` - Traditional
  * `3:4` - Portrait traditional
  * `1:1` - Square

  <Warning>
    This parameter is only available in text-to-video mode (when `image_urls` is not provided). In image-to-video mode, the aspect ratio is automatically determined by the image.
  </Warning>
</ParamField>

<ParamField body="image_urls" type="string[]">
  Image URL array for image-to-video generation

  The system automatically determines the generation mode based on the number of images:

  * **0 images** (not provided): Text-to-video mode
  * **1 image**: Image-to-video mode (image used as starting frame)
  * **2 images**: First-last frame mode (first image = first frame, second image = last frame)

  Example: `["https://example.com/photo.jpg"]`

  <Warning>
    * Maximum 2 images supported
    * For first-last frame mode, exactly 2 images must be provided
    * When `image_urls` is provided, the `aspect_ratio` parameter cannot be used
  </Warning>
</ParamField>

<ParamField body="audio" type="boolean" default="true">
  Whether to generate audio (dialogue, sound effects)

  Default: `true`

  Set to `false` for a silent video.
</ParamField>

<ParamField body="seed" type="integer">
  Random seed for reproducible results

  Using the same seed with identical parameters will produce the same video output.
</ParamField>

## Auto Routing

The system automatically determines the generation mode based on the number of images in `image_urls`:

| Images Count     | Mode             | Description                                          |
| ---------------- | ---------------- | ---------------------------------------------------- |
| 0 (not provided) | Text-to-Video    | Generate from text description only                  |
| 1                | Image-to-Video   | Use the image as starting frame                      |
| 2                | First-Last Frame | First image = first frame, second image = last frame |

## Parameter Support Matrix

| Parameter      | Text-to-Video | Image-to-Video | First-Last Frame |
| -------------- | ------------- | -------------- | ---------------- |
| `model`        | ✅ Required    | ✅ Required     | ✅ Required       |
| `prompt`       | ✅ Required    | Optional       | Optional         |
| `image_urls`   | -             | ✅ 1 image      | ✅ 2 images       |
| `duration`     | ✅ 1-16s       | ✅ 1-16s        | ✅ 1-16s          |
| `resolution`   | ✅             | ✅              | ✅                |
| `aspect_ratio` | ✅             | -              | -                |
| `audio`        | ✅             | ✅              | ✅                |
| `seed`         | ✅             | ✅              | ✅                |

## Response

<ResponseField name="id" type="string">
  Unique task identifier for querying task status
</ResponseField>

<ResponseField name="object" type="string">
  Object type, fixed as `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Model name used for generation
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
  Task creation Unix timestamp
</ResponseField>

## Use Cases

### Case 1: Text-to-Video

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "A cat playing piano, camera slowly zooms in",
  "duration": 8,
  "resolution": "1080p",
  "aspect_ratio": "16:9"
}
```

### Case 2: Image-to-Video (Single Image)

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "The person slowly turns and smiles",
  "image_urls": ["https://example.com/photo.jpg"],
  "duration": 5,
  "resolution": "720p"
}
```

### Case 3: First-Last Frame Video

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "The person gradually sits down from standing",
  "image_urls": [
    "https://example.com/first.jpg",
    "https://example.com/last.jpg"
  ],
  "duration": 8
}
```

### Case 4: Silent Video (Audio Disabled)

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "Sunset seascape timelapse photography",
  "duration": 10,
  "resolution": "1080p",
  "audio": false
}
```

<Note>
  **Query Task Results**

  Video generation is an async task that returns a task ID upon submission. Use the [Get Task Status](/en/api-reference/tasks/status) endpoint to query generation progress and results.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "viduq3-pro",
      "prompt": "A cat playing piano, camera slowly zooms in",
      "duration": 8,
      "resolution": "1080p",
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
          "model": "viduq3-pro",
          "prompt": "A cat playing piano, camera slowly zooms in",
          "duration": 8,
          "resolution": "1080p",
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
      model: 'viduq3-pro',
      prompt: 'A cat playing piano, camera slowly zooms in',
      duration: 8,
      resolution: '1080p',
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "viduq3-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
