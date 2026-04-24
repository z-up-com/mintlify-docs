# Veo3 Official Video Generation

> Generate high-quality videos using Google Veo3 official direct-connect models, with advanced features like audio generation and keyframe control

* Asynchronous processing mode, returns a task ID for subsequent status queries
* Direct connection to Google Vertex AI, supporting all native Veo parameters
* Supports text-to-video, image-to-video, and keyframe interpolation generation modes
* Generated video links are valid for 24 hours, please save them promptly

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `image_urls`. Please use the [Upload Image endpoint](/docs/en/api-reference/uploads/images) to upload images first, then use the returned URL in this API.
</Warning>

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get API Key: Visit the [API Key Management page](https://toapis.com/console/token) to obtain your API Key

  Add to request headers:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="Veo3.1-quality-official" required>
  Video generation model name

  Available models:

  * `Veo3.1-quality-official` - High-quality generation model, ideal for final production
  * `Veo3.1-fast-official` - Fast generation model, ideal for quick previews and iteration

  Example: `"Veo3.1-quality-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for video generation
</ParamField>

<ParamField body="duration" type="integer">
  Video duration in seconds

  Supported values:

  * `4` - 4 seconds
  * `6` - 6 seconds
  * `8` - 8 seconds (default)
</ParamField>

<ParamField body="size" type="string">
  Video aspect ratio

  Supported formats:

  * `16:9` (landscape, default)
  * `9:16` (portrait)
</ParamField>

<ParamField body="resolution" type="string">
  Video resolution

  Supported values:

  * `720p` (default)
  * `1080p`
</ParamField>

<ParamField body="image_urls" type="string[]">
  **First frame reference image** for image-to-video generation. The video will begin from this image. Only the first URL in the array is used.

  **⚠️ Only URL format is supported (base64 is no longer supported)**

  * Publicly accessible image URLs (http\:// or https\://)
  * Use the [Upload Image endpoint](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limits:**

  * Maximum file size: 10MB
  * Supported formats: .jpeg, .jpg, .png, .webp

  <Warning>
    `image_urls` and `metadata.referenceImages` **cannot be used together**. If both are provided, `image_urls` (first frame) takes priority and `referenceImages` will be ignored.
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Veo3 Official specific extension parameters

  <Expandable title="Show metadata fields">
    <ParamField body="generateAudio" type="boolean">
      Whether to generate audio. Default: `false`

      When enabled, the video will include AI-generated sound effects and ambient audio
    </ParamField>

    <ParamField body="negativePrompt" type="string">
      Negative prompt describing content you don't want in the video

      Example: `"blurry, low quality, distorted"`
    </ParamField>

    <ParamField body="personGeneration" type="string">
      Person generation safety setting

      Supported values:

      * `allow_adult` - Allow generating adult people
      * `dont_allow` - Don't allow generating people
    </ParamField>

    <ParamField body="lastFrame" type="string">
      Last frame image URL for keyframe interpolation video generation

      **Must** be used together with `image_urls` (first frame) to generate a transition video from the first frame to the last frame. Has no effect without `image_urls`.
    </ParamField>

    <ParamField body="referenceImages" type="string[]">
      Style/asset reference image URL array. The video will incorporate the visual style and elements from these images (they are NOT used as the starting frame).

      **Limit: maximum 3 images**

      **⚠️ Cannot be used together with `image_urls`** — these are mutually exclusive in the Veo API. Use `image_urls` if you need first-frame control.
    </ParamField>

    <ParamField body="compressionQuality" type="string">
      Video compression quality

      Supported values:

      * `optimized` (default) - Optimized compression
      * `lossless` - Lossless compression
    </ParamField>

    <ParamField body="resizeMode" type="string">
      Image resize mode for image-to-video generation

      Supported values:

      * `pad` - Pad mode, preserves original aspect ratio
      * `crop` - Crop mode, fills the frame
    </ParamField>
  </Expandable>
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique task identifier for querying task status
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
      "model": "Veo3.1-quality-official",
      "prompt": "A dolphin leaping through crystal blue ocean waters, splashing water droplets, sunlight glistening on the surface",
      "duration": 8,
      "size": "16:9",
      "resolution": "1080p",
      "metadata": {
        "generateAudio": true,
        "negativePrompt": "blurry, low quality"
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
          "model": "Veo3.1-quality-official",
          "prompt": "A dolphin leaping through crystal blue ocean waters, splashing water droplets, sunlight glistening on the surface",
          "duration": 8,
          "size": "16:9",
          "resolution": "1080p",
          "metadata": {
              "generateAudio": True,
              "negativePrompt": "blurry, low quality"
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
      model: 'Veo3.1-quality-official',
      prompt: 'A dolphin leaping through crystal blue ocean waters, splashing water droplets, sunlight glistening on the surface',
      duration: 8,
      size: '16:9',
      resolution: '1080p',
      metadata: {
        generateAudio: true,
        negativePrompt: 'blurry, low quality'
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
    "model": "Veo3.1-quality-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
