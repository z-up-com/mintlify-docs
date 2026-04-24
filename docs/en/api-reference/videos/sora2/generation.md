---
title: "Sora2 Video Generation"
---
> Generate high-quality videos using OpenAI Sora2 model

* OpenAI Sora2 video generation model
* Use model parameter to select `sora-2` or `sora-2-pro` model
* Supports text-to-video, image-to-video, and character reference
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

<ParamField body="model" type="string" default="sora-2" required>
  Video generation model name

  Supported models:

  * `sora-2` - Standard version
  * `sora-2-pro` - Professional version, supports longer duration
  * `sora-2-vip` - VIP version, higher priority

  Example: `"sora-2"` or `"sora-2-pro"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for video generation

  **💡 @Character Reference Feature:**

  You can use `@username` format in the prompt to reference previously created characters and include them in the generated video.

  * The character's `username` can be obtained via the [Query Character API](/en/api-reference/videos/sora2/get-personas)
  * Supports referencing multiple characters at once

  Example: `"A cat and a dog driving a car together @duksvfkf.cruisingki @zdqwahgj.baronbarki"`
</ParamField>

<ParamField body="duration" type="integer">
  Video duration in seconds

  * `sora-2`: Supports 10 or 15 seconds
  * `sora-2-pro`: Supports 15 seconds (HD) or 25 seconds

  Example: `10`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Video aspect ratio

  Supported formats:

  * `16:9` (Landscape)
  * `9:16` (Portrait)
</ParamField>

<ParamField body="image_urls" type="string[]">
  Array of reference image URLs for image-to-video generation

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs
</ParamField>

<ParamField body="thumbnail" type="boolean">
  Whether to generate a video thumbnail

  * `true` - Generate a video thumbnail for preview and display

  If you don't need a thumbnail, you can omit this parameter
</ParamField>

<ParamField body="metadata.n" type="integer" default="1">
  Number of different variants to generate

  Range: 1-4

  Default: `1`
</ParamField>

<ParamField body="metadata.watermark" type="boolean" default="false">
  Whether to add a watermark to the generated video

  * `false` - No watermark
  * `true` - Add Sora official watermark to the video

  Default: `false`
</ParamField>

<ParamField body="metadata.hd" type="boolean" default="false">
  Whether to generate high-definition video

  * `true` - Generate HD video
  * `false` - Standard definition

  **⚠️ Note:** Requires `sora-2-pro` model and duration cannot be 25 seconds

  Default: `false`
</ParamField>

<ParamField body="metadata.private" type="boolean" default="false">
  Whether to enable privacy mode

  * `true` - Video will not be published and cannot be remixed (re-edited)
  * `false` - Video can be published and remixed

  Default: `false`
</ParamField>

<ParamField body="metadata.style" type="string">
  Video style

  Supported values:

  * `thanksgiving` - Thanksgiving style
  * `comic` - Comic style
  * `news` - News style
  * `selfie` - Selfie style
  * `nostalgic` - Nostalgic/Retro style
  * `anime` - Anime style

  Example: `"anime"`
</ParamField>

<ParamField body="metadata.storyboard" type="boolean">
  Whether to use storyboard for finer control over video generation details

  * `true` - Enable storyboard feature
  * `false` - Do not use storyboard

  Example: `true`
</ParamField>

<ParamField body="metadata.character_url" type="string">
  Reference video URL for character extraction

  Used to reuse characters from a reference video in the generated video

  Example: `"https://filesystem.site/cdn/20251030/javYrU4etHVFDqg8by7mViTWHlMOZy.mp4"`

  **⚠️ Note:** When using this parameter, the generated video duration will be reduced by 1 second
</ParamField>

<ParamField body="metadata.character_timestamps" type="string">
  Character appearance timestamps

  Specifies the time range in the reference video where the character appears, only supports 2-second segments

  Format: `"start_second,end_second"`

  Example: `"1,3"` means the character from second 1 to second 3 in the reference video

  **⚠️ Note:** When using this parameter, the generated video duration will be reduced by 1 second
</ParamField>

<ParamField body="metadata.character_create" type="boolean" default="false">
  Automatically create a character from the generated video after completion

  * `true` - Automatically create character
  * `false` - Do not create character

  Default: `false`
</ParamField>

<ParamField body="metadata.character_from_task" type="string">
  Create a character from an already generated task ID

  Reference a previously generated video task ID to extract characters from that video

  Example: `"video_01K8SGYNNNVBQTXNR4MM964S7K"`
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
  ```bash cURL (Text-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "A golden retriever running on grass, sunny day",
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL (Image-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Make this dog come alive, running on grass",
      "image_urls": ["https://example.com/dog.jpg"],
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL (With Style) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "A cute cartoon elephant",
      "aspect_ratio": "16:9",
      "duration": 10,
      "metadata": {
        "style": "anime",
        "watermark": false
      }
    }'
  ```

  ```bash cURL (With Character Reference) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "A cat and a dog driving a car together @duksvfkf.cruisingki @zdqwahgj.baronbarki",
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```python Python (with metadata) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "prompt": "A golden retriever running on grass, sunny day",
          "duration": 10,
          "aspect_ratio": "16:9",
          "metadata": {
              "style": "anime",
              "watermark": False,
              "n": 2
          }
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```javascript JavaScript (with metadata) theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      prompt: 'A golden retriever running on grass, sunny day',
      duration: 10,
      aspect_ratio: '16:9',
      metadata: {
        style: 'anime',
        watermark: false,
        n: 2
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
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "720x720"
    }
  }
  ```
</ResponseExample>
