---
title: "Kling v2.6 Video Generation"
---
> Generate high-quality videos using the Kling v2.6 model, supporting text-to-video and image-to-video

* Async processing mode, returns task ID for subsequent queries
* Supports text-to-video, image-to-video (first frame / first-last frame control)
* Supports standard mode (720P) and professional mode (1080P)
* Professional mode supports automatic audio generation

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
  Video generation model name, fixed as `kling-2-6`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text prompt, maximum **2500 characters**

  Describe scenes, actions, and styles in detail for better generation results

  Example: `"A golden cat running on a sunlit meadow, slow motion, cinematic quality"`
</ParamField>

<ParamField body="mode" type="string" default="std">
  Generation mode

  Options:

  * `std` - Standard mode (720P, silent video only)
  * `pro` - Professional mode (1080P, supports automatic audio generation)

  Default: `std`

  <Warning>
    **Standard mode limitation**: `std` mode only supports silent video. The `audio` parameter requires `pro` mode.
  </Warning>
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

  Options: `5` or `10`

  Default: `5`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Video aspect ratio

  Options:

  * `16:9` - Landscape (default)
  * `9:16` - Portrait
  * `1:1` - Square

  Default: `16:9`
</ParamField>

<ParamField body="negative_prompt" type="string">
  Negative prompt to exclude unwanted content

  Example: `"blurry, low quality, distorted"`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Image URL array for image-to-video generation

  * Pass **1 image**: used as first frame
  * Pass **2 images**: automatically assigned as first frame + last frame (requires `mode: "pro"`)

  Maximum 2 images supported

  Example: `["https://example.com/first.jpg"]`

  <Warning>
    * Maximum 2 images supported
    * Last frame (2 images) requires `pro` mode only; `std` mode only supports first frame (1 image)
    * **Last frame and audio are mutually exclusive**: In `pro` mode, last frame (2 images) and audio (`audio: true`) cannot be used together
    * In image-to-video mode, `aspect_ratio` may be overridden by the actual image ratio
  </Warning>
</ParamField>

<ParamField body="audio" type="boolean" default="false">
  Whether to automatically generate audio

  Default: `false`

  <Warning>
    * Only available in `mode: "pro"`
    * **Mutually exclusive with last frame**: Audio cannot be used together with last frame (2 images)
  </Warning>
</ParamField>

<ParamField body="watermark" type="boolean">
  Whether to add watermark
</ParamField>

## Pricing

Billed per second, price varies by mode and audio setting:

| Mode  | Resolution | Audio         | Price          |
| ----- | ---------- | ------------- | -------------- |
| `std` | 720P       | Not supported | \$0.0368 / sec |
| `pro` | 1080P      | Off           | \$0.0625 / sec |
| `pro` | 1080P      | On            | \$0.125 / sec  |

Example: `pro` mode, 10-second video without audio = 10 × $0.0625 = $0.625

## Feature Support Matrix

| Type           | Feature     | std 5s          | std 10s         | pro 5s | pro 10s |
| -------------- | ----------- | --------------- | --------------- | ------ | ------- |
| Text-to-Video  | Generation  | ✅ (silent only) | ✅ (silent only) | ✅      | ✅       |
| Text-to-Video  | Auto Audio  | -               | -               | ✅      | ✅       |
| Image-to-Video | Generation  | ✅ (silent only) | ✅ (silent only) | ✅      | ✅       |
| Image-to-Video | First Frame | ✅               | ✅               | ✅      | ✅       |
| Image-to-Video | Last Frame  | -               | -               | ✅      | ✅       |
| Image-to-Video | Auto Audio  | -               | -               | ✅      | ✅       |

> In `pro` mode, last frame and audio control are mutually exclusive and cannot be used together.

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

### Case 1: Text-to-Video (Standard Mode)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "A golden cat running on a sunlit meadow, slow motion, cinematic quality",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### Case 2: Text-to-Video (Pro Mode + Negative Prompt)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "Tokyo Shibuya crossing at night, neon lights reflected on wet ground, people walking with umbrellas",
  "negative_prompt": "blurry, low quality, distorted",
  "mode": "pro",
  "duration": 10,
  "aspect_ratio": "16:9"
}
```

### Case 3: Image-to-Video (First Frame)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "The person in the frame turns and smiles",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

### Case 4: Image-to-Video (First + Last Frame Control)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "City timelapse transitioning from day to night",
  "image_urls": ["https://example.com/day-city.jpg", "https://example.com/night-city.jpg"],
  "mode": "pro",
  "duration": 5
}
```

### Case 5: Pro Mode + Auto Audio

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "Waves crashing against rocks, seagulls circling in the sky, lighthouse in the distance",
  "mode": "pro",
  "duration": 10,
  "audio": true,
  "aspect_ratio": "16:9"
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
      "model": "kling-2-6",
      "prompt": "A golden cat running on a sunlit meadow, slow motion, cinematic quality",
      "mode": "std",
      "duration": 5,
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
          "model": "kling-2-6",
          "prompt": "A golden cat running on a sunlit meadow, slow motion, cinematic quality",
          "mode": "std",
          "duration": 5,
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
      model: 'kling-2-6',
      prompt: 'A golden cat running on a sunlit meadow, slow motion, cinematic quality',
      mode: 'std',
      duration: 5,
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
    "model": "kling-2-6",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
