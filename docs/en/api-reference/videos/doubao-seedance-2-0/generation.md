---
title: "Seedance 2.0 Video Generation"
---
> Generate videos using ByteDance Seedance 2.0 and Seedance 2.0 Fast models

* ByteDance's next-generation video generation models
* Supports `doubao-seedance-2-0` and `doubao-seedance-2-0-fast`
* Supports text-to-video, first-frame image-to-video, first-and-last-frame image-to-video, and multimodal reference-to-video
* Supports reference images, reference videos, and reference audio
* Supports synced audio generation, web search tools, and returning the last frame
* Async task workflow with task ID based status queries

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get your API Key from the [API Key Management Page](https://toapis.com/console/token)

  Add to the request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="doubao-seedance-2-0" required>
  Video generation model name

  Available models:

  * `doubao-seedance-2-0` - Standard version focused on higher quality, supports `4-15` second output
  * `doubao-seedance-2-0-fast` - Faster version for preview and iteration, supports `4-12` second output
</ParamField>

<ParamField body="prompt" type="string">
  Video prompt

  Supports both Chinese and English input. Describe scene, camera movement, subject actions, style, and audio atmosphere as clearly as possible.

  Recommendations:

  * Keep Chinese prompts within about `500` characters
  * Keep English prompts within about `1000` words
  * When referring to uploaded assets, use labels like "image 1", "video 1", or "audio 1"

  Example: `"Use video 1 for the POV composition throughout, start from image 1, end on image 2, and preserve the rhythm and mood from audio 1"`
</ParamField>

<ParamField body="duration" type="integer" default={5}>
  Video duration in seconds

  Allowed values:

  * `doubao-seedance-2-0`: `4-15`
  * `doubao-seedance-2-0-fast`: `4-12`
  * `-1`: automatic duration selected by the model

  <Warning>
    `doubao-seedance-2-0-fast` does not support durations longer than `12` seconds.
  </Warning>
</ParamField>

<ParamField body="aspect_ratio" type="string" default="adaptive">
  Video aspect ratio

  Options:

  * `21:9`
  * `16:9`
  * `4:3`
  * `1:1`
  * `3:4`
  * `9:16`
  * `adaptive`

  `adaptive` behavior:

  * Text-to-video: the model chooses the most suitable ratio from the prompt
  * First-frame or first-and-last-frame image-to-video: derived from the first frame
  * Multimodal reference-to-video: typically prioritizes reference video, then reference image
</ParamField>

<ParamField body="image_urls" type="string[]">
  Image URL array in compatibility mode

  `image_with_roles` is recommended for explicit control.

  `image_urls` and `image_with_roles` should not be used together.
</ParamField>

<ParamField body="image_with_roles" type="array">
  Image array with roles

  Supported patterns:

  * First-frame image-to-video: one `first_frame`
  * First-and-last-frame image-to-video: one `first_frame` plus one `last_frame`
  * Multimodal reference-to-video: `reference_image` entries, `1-9` items

  <Expandable title="Field Description">
    <ParamField body="url" type="string" required>
      Image URL, Base64 data, or uploaded asset URI

      Supported formats:

      * `https://...`
      * `data:image/<format>;base64,...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField body="role" type="string" required>
      Image role

      Options:

      * `first_frame`
      * `last_frame`
      * `reference_image`
    </ParamField>
  </Expandable>

  Image requirements:

  * Formats: `jpeg`, `png`, `webp`, `bmp`, `tiff`, `gif`
  * Per-image size: less than `30MB`
  * Total request size: recommended within `64MB`
  * Aspect ratio: about `0.4` to `2.5`
  * Dimensions: about `300px` to `6000px`

  <Warning>
    - First-frame and first-and-last-frame modes cannot be mixed with `reference_image`, `reference_video`, or `reference_audio`
    - Only one `first_frame` and one `last_frame` are allowed
    - In multimodal reference mode, all images should use `reference_image`
  </Warning>
</ParamField>

<ParamField body="video_with_roles" type="array">
  Video array with roles

  Currently only `reference_video` is supported for multimodal reference mode.

  <Expandable title="Field Description">
    <ParamField body="url" type="string" required>
      Video URL or uploaded asset URI

      Supported formats:

      * `https://...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField body="role" type="string" required>
      Fixed value:

      * `reference_video`
    </ParamField>
  </Expandable>

  Video requirements:

  * Formats: `mp4`, `mov`
  * Resolution: `480p` or `720p`
  * Per-video duration: `2-15` seconds
  * Maximum count: `3`
  * Total reference video duration: no more than `15` seconds
  * Per-video size: less than `50MB`
  * Frame rate: about `24-60 FPS`
</ParamField>

<ParamField body="audio_with_roles" type="array">
  Audio array with roles

  Currently only `reference_audio` is supported for multimodal reference mode.

  <Expandable title="Field Description">
    <ParamField body="url" type="string" required>
      Audio URL, Base64 data, or uploaded asset URI

      Supported formats:

      * `https://...`
      * `data:audio/<format>;base64,...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField body="role" type="string" required>
      Fixed value:

      * `reference_audio`
    </ParamField>
  </Expandable>

  Audio requirements:

  * Formats: `wav`, `mp3`
  * Per-audio duration: `2-15` seconds
  * Maximum count: `3`
  * Total reference audio duration: no more than `15` seconds
  * Per-audio size: less than `15MB`

  <Warning>
    `audio_with_roles` cannot be used alone. At least one image or video reference is also required.
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters

  <Expandable title="Field Description">
    <ParamField body="resolution" type="string" default="720p">
      Video resolution

      Options:

      * `480p`
      * `720p`
    </ParamField>

    <ParamField body="generate_audio" type="boolean" default={true}>
      Whether to generate synced audio

      * `true`: returns a video with audio
      * `false`: returns a silent video

      The model can generate dialogue, sound effects, and background music from the prompt and visual cues.
    </ParamField>

    <ParamField body="return_last_frame" type="boolean" default={false}>
      Whether to also return the last frame image in the result
    </ParamField>

    <ParamField body="tools" type="array">
      Tool configuration

      Currently supported:

      * `[{ "type": "web_search" }]`

      Notes:

      * Best suited for text-to-video requests
      * The model will search only when needed, which may improve freshness but also add latency
    </ParamField>

    <ParamField body="seed" type="integer">
      Random seed for generation control
    </ParamField>
  </Expandable>
</ParamField>

## Input Combination Rules

Typical supported combinations:

* Text only: text-to-video
* Text + one first-frame image: first-frame image-to-video
* Text + first-frame image + last-frame image: first-and-last-frame image-to-video
* Text + reference images: multimodal reference-to-video
* Text + reference videos: video-guided reference-to-video
* Text + reference images + reference audio: multimodal reference-to-video
* Text + reference images + reference videos + reference audio: multimodal reference-to-video

<Warning>
  These three modes are mutually exclusive:

  * First-frame image-to-video
  * First-and-last-frame image-to-video
  * Multimodal reference-to-video

  If you need strict first-frame and last-frame control, prefer `first_frame` and `last_frame`. If you need broader reference guidance, use `reference_image`, `reference_video`, and `reference_audio`.
</Warning>

## Resolution and Aspect Ratio Pixel Map

| Resolution | Aspect Ratio | Pixel Size |
| ---------- | ------------ | ---------- |
| `480p`     | `16:9`       | `864x496`  |
| `480p`     | `4:3`        | `752x560`  |
| `480p`     | `1:1`        | `640x640`  |
| `480p`     | `3:4`        | `560x752`  |
| `480p`     | `9:16`       | `496x864`  |
| `480p`     | `21:9`       | `992x432`  |
| `720p`     | `16:9`       | `1280x720` |
| `720p`     | `4:3`        | `1112x834` |
| `720p`     | `1:1`        | `960x960`  |
| `720p`     | `3:4`        | `834x1112` |
| `720p`     | `9:16`       | `720x1280` |
| `720p`     | `21:9`       | `1470x630` |

## Capabilities and Constraints

| Item              | Seedance 2.0                                     | Seedance 2.0 Fast                                |
| ----------------- | ------------------------------------------------ | ------------------------------------------------ |
| Positioning       | Higher quality                                   | Faster generation and lower cost                 |
| Duration          | `4-15` seconds, or `-1` auto                     | `4-12` seconds, or `-1` auto                     |
| Resolution        | `480p` / `720p`                                  | `480p` / `720p`                                  |
| Image roles       | `first_frame` / `last_frame` / `reference_image` | `first_frame` / `last_frame` / `reference_image` |
| Video roles       | `reference_video`                                | `reference_video`                                |
| Audio roles       | `reference_audio`                                | `reference_audio`                                |
| Audio generation  | `metadata.generate_audio`                        | `metadata.generate_audio`                        |
| Tools             | `metadata.tools`                                 | `metadata.tools`                                 |
| Return last frame | `metadata.return_last_frame`                     | `metadata.return_last_frame`                     |

<Info>
  Pricing is billed per second. Final display prices may vary by model version, resolution, and marketplace strategy. Please refer to the [model pricing page](https://toapis.com/models).
</Info>

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

  * `queued` - Queued
  * `in_progress` - Processing
  * `completed` - Completed successfully
  * `failed` - Failed
</ResponseField>

<ResponseField name="progress" type="integer">
  Task progress percentage (`0-100`)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation timestamp (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Task metadata
</ResponseField>

<Info>
  Video generation is asynchronous. The create call returns a task ID, and you can use [Get Video Task Status](/en/api-reference/tasks/video-status) to poll for progress and results.
</Info>

<RequestExample>
  ```bash cURL (Multimodal reference-to-video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "Use video 1 for the POV composition throughout, use audio 1 as the background bed, start from image 1, and end on image 2 with a fresh commercial tone.",
      "duration": 11,
      "aspect_ratio": "16:9",
      "image_with_roles": [
        {"url": "https://example.com/ref-image-1.jpg", "role": "reference_image"},
        {"url": "https://example.com/ref-image-2.jpg", "role": "reference_image"}
      ],
      "video_with_roles": [
        {"url": "https://example.com/ref-video-1.mp4", "role": "reference_video"}
      ],
      "audio_with_roles": [
        {"url": "https://example.com/ref-audio-1.mp3", "role": "reference_audio"}
      ],
      "metadata": {
        "resolution": "720p",
        "generate_audio": true
      }
    }'
  ```

  ```bash cURL (First and last frame image-to-video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "The camera slowly moves from a bouquet near the window toward the dining table, with warm and gentle lighting.",
      "duration": 5,
      "aspect_ratio": "adaptive",
      "image_with_roles": [
        {"url": "https://example.com/first-frame.png", "role": "first_frame"},
        {"url": "https://example.com/last-frame.png", "role": "last_frame"}
      ],
      "metadata": {
        "resolution": "720p",
        "generate_audio": false,
        "return_last_frame": true
      }
    }'
  ```

  ```bash cURL (Text-to-video with web search) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "A macro documentary shot of a glass frog on a leaf, shifting focus toward its transparent abdomen and visible heartbeat.",
      "duration": 11,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p",
        "generate_audio": true,
        "tools": [{"type": "web_search"}]
      }
    }'
  ```

  ```bash cURL (Fast preview) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0-fast",
      "prompt": "A Shiba Inu runs down a neon-lit street after rain, with colorful reflections on the wet pavement.",
      "duration": 5,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p"
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "doubao-seedance-2-0",
          "prompt": "A smooth push-in from a cafe interior to the city street outside, preserving the speaking rhythm and atmosphere from the reference",
          "duration": -1,
          "aspect_ratio": "adaptive",
          "image_with_roles": [
              {"url": "https://example.com/ref.png", "role": "reference_image"}
          ],
          "metadata": {
              "resolution": "720p",
              "generate_audio": True,
              "tools": [{"type": "web_search"}],
          },
      },
  )

  print(response.json())
  ```
</RequestExample>
