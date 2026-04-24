> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# MiniMax-Hailuo-02 Video Generation

> Generate high-quality videos using MiniMax Hailuo 02 model

* Asynchronous processing mode, returns task ID for subsequent queries
* Supports text-to-video, image-to-video (first frame/last frame)
* Supports 5-second and 10-second durations, multiple resolutions available
* Supports automatic prompt optimization and watermark control

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `image_urls`, `first_frame_image` and `last_frame_image`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) first to upload images and get URLs, then call this endpoint.
</Warning>

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
  Video generation model name

  Fixed value: `MiniMax-Hailuo-02`
</ParamField>

<ParamField body="prompt" type="string" required>
  Video content description

  Recommended to describe scene, action, style in detail for better generation results

  Example: `"A cute cat running on grass"`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

  Available values:

  * `5` - 5-second video
  * `10` - 10-second video

  Default: `5`

  **1080p Limitation**: When using 1080p resolution, only 5-second duration is supported
</ParamField>

<ParamField body="metadata" type="object">
  Advanced configuration options for video generation

  <Expandable title="Show metadata field descriptions">
    <ParamField body="metadata.resolution" type="string" default="768p">
      Video resolution

      Available values:

      * `512p` - Standard definition
      * `768p` - High definition
      * `1080p` - Full HD (only supports 5-second duration)

      Default: `768p`
    </ParamField>

    <ParamField body="metadata.prompt_optimizer" type="boolean" default="true">
      Auto-optimize prompt

      When enabled, the system will automatically optimize your prompt for better generation results

      Default: `true`
    </ParamField>

    <ParamField body="metadata.fast_pretreatment" type="boolean" default="false">
      Shorten prompt optimization time

      When enabled, speeds up processing but may slightly affect optimization quality

      Default: `false`
    </ParamField>

    <ParamField body="metadata.watermark" type="boolean" default="false">
      Add watermark

      Default: `false`
    </ParamField>

    <ParamField body="metadata.first_frame_image" type="string">
      Video starting frame image

      **⚠️ URL format only (base64 no longer supported)**

      * Publicly accessible image URL (http\:// or https\://)
      * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

      Used to specify the starting frame of the video
    </ParamField>

    <ParamField body="metadata.last_frame_image" type="string">
      Video ending frame image

      **⚠️ URL format only (base64 no longer supported)**

      * Publicly accessible image URL (http\:// or https\://)
      * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

      Used to specify the ending frame of the video
    </ParamField>
  </Expandable>
</ParamField>

## Parameter Limitations

| Limitation       | Description                                  |
| ---------------- | -------------------------------------------- |
| Duration         | Only supports 5 or 10 seconds                |
| 1080p Resolution | Only supports 5-second duration              |
| Image Format     | URL format only (base64 no longer supported) |

## Resolution and Duration Combinations

| Resolution | Supported Durations | Note              |
| ---------- | ------------------- | ----------------- |
| 512p       | 5s, 10s             | All supported     |
| 768p       | 5s, 10s             | All supported     |
| 1080p      | 5s                  | 10s not supported |

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

### Scenario 1: Quick Text-to-Video Generation

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "A cute cat running on grass, sunny day"
}
```

### Scenario 2: Generate High-Quality 1080p Video

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "City night scene, neon lights flickering, traffic flowing",
  "duration": 5,
  "metadata": {
    "resolution": "1080p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### Scenario 3: First Frame-Based Video Generation

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Person slowly turning around, smiling",
  "duration": 5,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/portrait.jpg"
  }
}
```

### Scenario 4: First and Last Frame Controlled Transition Video

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Scene gradually transitions from day to night, sky gradient",
  "duration": 10,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/day.jpg",
    "last_frame_image": "https://example.com/night.jpg",
    "prompt_optimizer": true
  }
}
```

### Scenario 5: Fast Preprocessing Mode

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Waves hitting the beach, sunset time",
  "duration": 5,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "fast_pretreatment": true
  }
}
```

<Note>
  **Query Task Results**

  Video generation is an asynchronous task. After submission, a `task_id` is returned. Use the [Get Task Status](/en/api-reference/tasks/status) interface to query generation progress and results.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "MiniMax-Hailuo-02",
      "prompt": "A cute cat running on grass",
      "duration": 5,
      "metadata": {
        "resolution": "768p",
        "prompt_optimizer": true,
        "fast_pretreatment": false,
        "watermark": false
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
          "model": "MiniMax-Hailuo-02",
          "prompt": "A cute cat running on grass",
          "duration": 5,
          "metadata": {
              "resolution": "768p",
              "prompt_optimizer": True,
              "fast_pretreatment": False,
              "watermark": False
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
      model: 'MiniMax-Hailuo-02',
      prompt: 'A cute cat running on grass',
      duration: 5,
      metadata: {
        resolution: '768p',
        prompt_optimizer: true,
        fast_pretreatment: false,
        watermark: false
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
    "model": "MiniMax-Hailuo-02",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "resolution": "768p"
    }
  }
  ```
</ResponseExample>
