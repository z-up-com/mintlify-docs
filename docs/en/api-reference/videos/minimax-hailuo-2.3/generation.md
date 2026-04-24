> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# MiniMax-Hailuo-2.3 Video Generation

> Generate high-quality videos using MiniMax Hailuo 2.3 model with camera directives support

* Async processing mode, returns task ID for subsequent queries
* Supports text-to-video and image-to-video (first frame)
* Supports 6-second and 10-second durations, 768p/1080p resolution
* Supports 15 camera directives for cinematic control
* Supports prompt optimization and watermark control

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `first_frame_image`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) first to upload images and get URLs, then call this endpoint.
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
  Video generation model name, fixed as `MiniMax-Hailuo-2.3`
</ParamField>

<ParamField body="prompt" type="string" required>
  Video content description (max 2000 characters)

  Describe scene, action, and style in detail for better results. Supports camera directives (see Camera Directives table below).

  Example: `"A cute cat running on grass, sunny day"`
</ParamField>

<ParamField body="duration" type="integer" default="6">
  Video duration (seconds)

  Options:

  * `6` - 6-second video
  * `10` - 10-second video

  Default: `6`

  <Warning>
    **1080p limitation**: When using 1080p resolution, only 6-second duration is supported.
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters for MiniMax-Hailuo-2.3

  <Expandable title="Supported metadata fields">
    <ParamField body="resolution" type="string" default="768p">
      Video resolution

      Options:

      * `768p` - HD (default)
      * `1080p` - Full HD (6-second duration only)

      Default: `768p`
    </ParamField>

    <ParamField body="first_frame_image" type="string">
      Video starting frame image

      **⚠️ URL format only (base64 not supported)**

      * Publicly accessible image URL (http\:// or https\://)
      * Use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

      Used to specify the starting frame of the video
    </ParamField>

    <ParamField body="prompt_optimizer" type="boolean" default={true}>
      Auto-optimize prompt for better generation results

      Default: `true`
    </ParamField>

    <ParamField body="fast_pretreatment" type="boolean" default={false}>
      Shorten prompt optimization time

      When enabled, speeds up processing but may slightly affect optimization quality

      Default: `false`
    </ParamField>

    <ParamField body="watermark" type="boolean" default={false}>
      Whether to add watermark

      Default: `false`
    </ParamField>
  </Expandable>
</ParamField>

## Resolution and Duration Combinations

| Resolution | Supported Durations | Note              |
| ---------- | ------------------- | ----------------- |
| 768p       | 6s, 10s             | All supported     |
| 1080p      | 6s                  | 10s not supported |

## Camera Directives

Use `[directive]` syntax in the `prompt` to control camera movement. Supports 15 directive types:

| Category  | Directives                                  |
| --------- | ------------------------------------------- |
| Pan       | `[Pan left]` `[Pan right]`                  |
| Tilt      | `[Tilt up]` `[Tilt down]`                   |
| Push/Pull | `[Push in]` `[Pull out]`                    |
| Pedestal  | `[Pedestal up]` `[Pedestal down]`           |
| Truck     | `[Truck left]` `[Truck right]`              |
| Zoom      | `[Zoom in]` `[Zoom out]`                    |
| Other     | `[Shake]` `[Tracking shot]` `[Static shot]` |

**Usage tips:**

* Combine up to 3 directives in one bracket for simultaneous movement (e.g., `[Pan left, Pedestal up]`)
* Sequence directives in the prompt for ordered camera moves
* Explicit commands yield more accurate results than natural language descriptions

**Example:**

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "[Push in] A cat running in a garden, camera slowly pushes in for a close-up",
  "duration": 6,
  "metadata": {
    "resolution": "768p"
  }
}
```

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

### Case 1: Quick Text-to-Video Generation

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "A cute cat running on grass, sunny day"
}
```

### Case 2: High-Quality 1080p Video

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "City night scene, neon lights flickering, traffic flowing",
  "duration": 6,
  "metadata": {
    "resolution": "1080p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### Case 3: First Frame-Based Video Generation

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "Person slowly turning around, smiling",
  "duration": 6,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/portrait.jpg"
  }
}
```

### Case 4: Using Camera Directives

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "[Push in] A cat running in a garden, camera slowly pushes in for a close-up",
  "duration": 6,
  "metadata": {
    "resolution": "768p"
  }
}
```

### Case 5: Fast Pretreatment Mode

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "Waves hitting the beach at sunset",
  "duration": 10,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "fast_pretreatment": true
  }
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
      "model": "MiniMax-Hailuo-2.3",
      "prompt": "A cute cat running on grass",
      "duration": 6,
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
          "model": "MiniMax-Hailuo-2.3",
          "prompt": "A cute cat running on grass",
          "duration": 6,
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
      model: 'MiniMax-Hailuo-2.3',
      prompt: 'A cute cat running on grass',
      duration: 6,
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
    "model": "MiniMax-Hailuo-2.3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "resolution": "768p"
    }
  }
  ```
</ResponseExample>
