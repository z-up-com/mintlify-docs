> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Wan2.6 Video Generation

> Generate high-quality videos using Alibaba Cloud Wan2.6 model — supports text-to-video, image-to-video and reference-video

* Alibaba Cloud Wanxiang video generation model
* Supports **three modes**: Text-to-Video, Image-to-Video, and Reference Video (r2v)
* Server automatically routes to the appropriate upstream model based on your request parameters
* Supports 720p/1080p resolution, 5/10/15 second duration
* Audio is always included in the generated output

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `image_urls`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) first to upload images and get URLs, then call this endpoint.
</Warning>

## Routing Logic

The server automatically selects the upstream model based on what parameters you provide:

| Parameters Provided                    | Upstream Mode         |
| -------------------------------------- | --------------------- |
| `metadata.reference_urls` (video URLs) | Reference Video (r2v) |
| `image_urls` (image)                   | Image-to-Video (i2v)  |
| `prompt` only                          | Text-to-Video (t2v)   |

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
  Video generation model name, fixed as `wan2.6`
</ParamField>

<ParamField body="prompt" type="string" required>
  Video content description

  Required for text-to-video mode; optional for image-to-video and reference video modes (describe expected motion or style)

  Example: `"A cute cat stretching in the sunshine"`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL array for image-to-video mode (only supports 1 image)

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  Example: `["https://example.com/image.jpg"]`

  **Note:** Cannot be combined with `metadata.reference_urls`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Video aspect ratio (applies to text-to-video and reference video modes)

  Available values:

  * `16:9` - Landscape (default)
  * `9:16` - Portrait
  * `1:1` - Square
  * `4:3` - Landscape
  * `3:4` - Portrait

  Default: `16:9`

  **Note:** Not supported in image-to-video mode
</ParamField>

<ParamField body="resolution" type="string" default="1080p">
  Video resolution

  Available values:

  * `720p` - Standard definition
  * `1080p` - High definition (default)

  Default: `1080p`

  480p is not supported. Billed per second; different resolutions have different prices — refer to the model marketplace for details.
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

  Supported values: `5`, `10`, `15`

  Default: `5`
</ParamField>

<ParamField body="negative_prompt" type="string">
  Negative prompt — describe content you do not want in the video

  Example: `"blurry, low quality, deformed"`
</ParamField>

<ParamField body="seed" type="integer">
  Random seed for reproducibility

  Example: `12345`
</ParamField>

<ParamField body="prompt_extend" type="boolean" default="true">
  Auto-extend prompt

  When enabled, the system automatically optimizes and enriches your prompt. **Enabled by default** — set to `false` to disable.
</ParamField>

<ParamField body="audio" type="boolean">
  Include audio in the generated video

  Non-flash Wan2.6 models always include audio by default. Set to `true` to explicitly enable.
</ParamField>

<ParamField body="shot_type" type="string">
  Shot type (applies to text-to-video and reference video modes)

  Available values:

  * `single` - Single continuous shot
  * `multi` - Multi-shot (cinematic cuts)
</ParamField>

<ParamField body="watermark" type="boolean">
  Add an Alibaba Cloud watermark to the generated video
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters

  <Expandable title="Show metadata fields">
    <ParamField body="reference_urls" type="string[]">
      **Reference Video (r2v) mode** — array of reference video URLs

      When provided, the server routes to the reference video upstream model (wan2.6-r2v). The model uses these videos to guide generation of consistent characters or scenes.

      * Each entry must be a publicly accessible video URL (http\:// or https\://)
      * Up to several reference videos are supported

      Example: `["https://cdn.example.com/ref1.mp4"]`

      **Note:** Cannot be combined with `image_urls`
    </ParamField>
  </Expandable>
</ParamField>

## Resolution and Aspect Ratio Combinations

| Aspect Ratio | Description         | 720p Size | 1080p Size |
| ------------ | ------------------- | --------- | ---------- |
| 16:9         | Landscape (default) | 1280×720  | 1920×1080  |
| 9:16         | Portrait            | 720×1280  | 1080×1920  |
| 1:1          | Square              | 960×960   | 1440×1440  |
| 4:3          | Landscape           | 1088×832  | 1632×1248  |
| 3:4          | Portrait            | 832×1088  | 1248×1632  |

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

### Scenario 1: Text-to-Video

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "A cute cat running on grass, sunny day",
  "aspect_ratio": "16:9",
  "resolution": "1080p",
  "duration": 5
}
```

### Scenario 2: Image-to-Video

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "The cat starts running playfully",
  "image_urls": ["https://example.com/cat.jpg"],
  "resolution": "1080p",
  "duration": 10
}
```

### Scenario 3: Reference Video (r2v)

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "The character waves and smiles at the camera",
  "metadata": {
    "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
  },
  "shot_type": "single",
  "resolution": "1080p",
  "duration": 5
}
```

### Scenario 4: Text-to-Video (Full Parameters)

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "A golden retriever runs through a field of sunflowers",
  "negative_prompt": "blurry, low quality, deformed",
  "aspect_ratio": "16:9",
  "resolution": "1080p",
  "duration": 10,
  "seed": 12345,
  "prompt_extend": true,
  "shot_type": "multi",
  "watermark": false
}
```

<Note>
  **Querying Task Results**

  Video generation is an asynchronous task. After submission, a `task_id` is returned. Use the [Get Task Status](/en/api-reference/tasks/status) interface to query generation progress and results.
</Note>

<RequestExample>
  ```bash cURL (Text-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6",
      "prompt": "A cute cat running on grass",
      "aspect_ratio": "16:9",
      "resolution": "1080p",
      "duration": 5
    }'
  ```

  ```bash cURL (Image-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6",
      "prompt": "The cat starts running playfully",
      "image_urls": ["https://example.com/cat.jpg"],
      "resolution": "1080p",
      "duration": 10
    }'
  ```

  ```bash cURL (Reference Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6",
      "prompt": "The character waves and smiles",
      "metadata": {
        "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
      },
      "resolution": "1080p",
      "duration": 5
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
          "model": "wan2.6",
          "prompt": "A cute cat running on grass",
          "aspect_ratio": "16:9",
          "resolution": "1080p",
          "duration": 5
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
      model: 'wan2.6',
      prompt: 'A cute cat running on grass',
      aspect_ratio: '16:9',
      resolution: '1080p',
      duration: 5
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
    "model": "wan2.6",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "aspect_ratio": "16:9"
    }
  }
  ```
</ResponseExample>
