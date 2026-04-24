---
title: "Kling v3 Video Generation"
---
> Generate high-quality videos using Kling v3 model, supporting text-to-video and image-to-video

* Async processing mode, returns task ID for subsequent queries
* Supports text-to-video and image-to-video (first frame / first-last frame control)
* Supports standard mode (720P) and professional mode (1080P)
* Text-to-video supports 15-second duration

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `image_urls`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) first to upload images and get URLs, then call this endpoint.
</Warning>

## Authorization

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get your API Key: Visit the [API Key Management Page](https://toapis.com/console/token) to get your API Key

  Add to request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Request Parameters

<ParamField body="model" type="string" required>
  Video generation model name, fixed as `kling-v3`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text prompt describing the video content

  Describe scenes, actions, and styles in detail for better generation results. English prompts are recommended.

  Example: `"A golden retriever running on a beach at sunset, cinematic quality"`
</ParamField>

<ParamField body="mode" type="string" default="std">
  Generation mode

  Options:

  * `std` - Standard mode (720P)
  * `pro` - Professional mode (1080P)

  Default: `std`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

  Options: `5`, `10`, or `15`

  Default: `5`

  <Warning>
    **15-second duration** is text-to-video only; image-to-video max is 10 seconds.
  </Warning>
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Video aspect ratio

  Options:

  * `16:9` - Landscape (default)
  * `9:16` - Portrait
  * `1:1` - Square

  Default: `16:9`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Image URL array for image-to-video generation

  * Pass **1 image**: used as first frame
  * Pass **2 images**: automatically assigned as first frame + last frame

  Maximum 2 images supported

  Example: `["https://example.com/first.jpg"]`

  <Warning>
    * Maximum 2 images supported
    * Image URLs must be publicly accessible, no hotlink protection
    * In image-to-video mode, `aspect_ratio` may be overridden by the actual image ratio
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters for Kling v3

  <Expandable title="Supported metadata fields">
    <ParamField body="negative_prompt" type="string">
      Negative prompt to exclude unwanted content

      Example: `"blurry, low quality, distorted"`
    </ParamField>

    <ParamField body="last_frame_image" type="string">
      Last frame image URL for image-to-video

      **⚠️ URL format only (base64 not supported)**

      Can also pass 2 images in `image_urls` to specify first + last frame
    </ParamField>

    <ParamField body="watermark" type="boolean">
      Whether to add watermark
    </ParamField>
  </Expandable>
</ParamField>

## Feature Support Matrix

| Type           | Feature     | std 5s | std 10s | std 15s | pro 5s | pro 10s |
| -------------- | ----------- | ------ | ------- | ------- | ------ | ------- |
| Text-to-Video  | Generation  | ✅      | ✅       | ✅       | ✅      | ✅       |
| Image-to-Video | Generation  | ✅      | ✅       | -       | ✅      | ✅       |
| Image-to-Video | First Frame | ✅      | ✅       | -       | ✅      | ✅       |
| Image-to-Video | Last Frame  | ✅      | ✅       | -       | ✅      | ✅       |

> **Note**: 15-second duration is text-to-video only; image-to-video max is 10 seconds.

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

<ResponseField name="metadata" type="object">
  Task metadata
</ResponseField>

## Use Cases

### Case 1: Text-to-Video (Standard Mode)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "A golden cat running on a sunlit meadow, slow motion, cinematic quality",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### Case 2: Text-to-Video (Pro Mode + Negative Prompt)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Tokyo Shibuya crossing at night, neon lights reflected on wet ground, people walking with umbrellas",
  "metadata": {
    "negative_prompt": "blurry, low quality, distorted"
  },
  "mode": "pro",
  "duration": 10,
  "aspect_ratio": "16:9"
}
```

### Case 3: Text-to-Video (15 seconds)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Time-lapse of a flower blooming in a garden",
  "duration": 15,
  "aspect_ratio": "16:9"
}
```

### Case 4: Image-to-Video (First Frame)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "The person in the frame turns and smiles",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

### Case 5: Image-to-Video (First + Last Frame Control)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "City timelapse transitioning from day to night",
  "image_urls": ["https://example.com/day-city.jpg", "https://example.com/night-city.jpg"],
  "mode": "pro",
  "duration": 5
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
      "model": "kling-v3",
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
          "model": "kling-v3",
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
      model: 'kling-v3',
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
    "model": "kling-v3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
