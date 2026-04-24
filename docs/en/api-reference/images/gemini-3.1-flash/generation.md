---
title: "Gemini-3.1-Flash Image Generation"
---
> Generate images using Google Gemini 3.1 Flash model with extreme aspect ratios and Google Search enhancement

* Google Gemini 3.1 Flash image generation model (Nano banana2)
* Use model parameter to select `gemini-3.1-flash-image-preview` model
* Supports text-to-image and image-to-image, up to 4K resolution output
* Up to 14 reference images to maintain style/character consistency
* Supports extreme aspect ratios (1:4, 4:1, 1:8, 8:1)
* Integrated Google Search enhancement for more accurate real-world images
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

<ParamField body="model" type="string" default="gemini-3.1-flash-image-preview" required>
  Image generation model name

  Example: `"gemini-3.1-flash-image-preview"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for image generation
</ParamField>

<ParamField body="size" type="string">
  Image aspect ratio

  Supported ratios:

  | Value           | Use Case                          |
  | --------------- | --------------------------------- |
  | `1:1`           | Square, avatar, social media      |
  | `3:2` / `2:3`   | Standard photo                    |
  | `4:3` / `3:4`   | Traditional display ratio         |
  | `16:9` / `9:16` | Widescreen / vertical video cover |
  | `5:4` / `4:5`   | Instagram images                  |
  | `21:9`          | Ultra-wide banner                 |
  | `1:4` / `4:1`   | Long poster / banner              |
  | `1:8` / `8:1`   | Extreme long image / ad banner    |
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images to generate

  **⚠️ Note:** Must be a pure number (e.g. `1`), do not add quotes, otherwise it will error
</ParamField>

<ParamField body="image_urls" type="object[]">
  List of reference image URLs for image-to-image or image editing

  <Expandable title="Detailed field description">
    <ParamField body="url" type="string" required>
      Image URL address

      **⚠️ URL format only (base64 no longer supported)**

      * Publicly accessible image URL (http\:// or https\://)
      * Example: `https://example.com/image.jpg`
      * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

      **Limitations:**

      * Single image must not exceed 10MB
      * Supported formats: .jpeg, .jpg, .png, .webp
    </ParamField>
  </Expandable>

  **Limit:** Maximum 14 images (recommended: up to 10 object references + 4 character references)
</ParamField>

<ParamField body="metadata" type="object">
  Metadata parameters for passing additional configuration options

  <Expandable title="Supported metadata fields">
    <ParamField body="resolution" type="string" default="1K">
      Output image resolution

      Supported values:

      * `0.5K` - \~512px, low-resolution preview
      * `1K` - \~1024px, standard resolution (default)
      * `2K` - \~2048px, high resolution
      * `4K` - \~4096px, ultra-high resolution

      **Note:** Pricing varies by resolution; 4K costs more than 1K
    </ParamField>

    <ParamField body="google_search" type="boolean" default="false">
      Enable Google text search enhancement

      * `true`: The model will first search the web for text information to assist in image generation, suitable for scenarios requiring real-world accuracy
      * `false`: Disabled (default)
    </ParamField>

    <ParamField body="google_image_search" type="boolean" default="false">
      Enable Google image search enhancement

      * `true`: In addition to text search, also searches for reference images to assist generation, suitable for scenarios requiring visual references
      * `false`: Disabled (default)

      **Note:** Must be used together with `google_search: true`
    </ParamField>
  </Expandable>
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
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "A cyberpunk city at night with flickering neon lights",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Image-to-Image) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "Transform this photo into a watercolor painting style",
      "size": "1:1",
      "n": 1,
      "image_urls": ["https://example.com/photo.jpg"],
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Google Search Enhancement) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "Latest iPhone product promotional image",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "2K",
        "google_search": true,
        "google_image_search": true
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gemini-3.1-flash-image-preview",
          "prompt": "A cyberpunk city at night with flickering neon lights",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "resolution": "2K"
          }
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```python Python (Image-to-Image) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gemini-3.1-flash-image-preview",
          "prompt": "Transform this photo into a watercolor painting style",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/photo.jpg"],
          "metadata": {
              "resolution": "2K"
          }
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gemini-3.1-flash-image-preview',
      prompt: 'A cyberpunk city at night with flickering neon lights',
      size: '16:9',
      n: 1,
      metadata: {
        resolution: '2K'
      }
    })
  });

  const task = await response.json();
  console.log(`Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
  ```

  ```javascript JavaScript (Image-to-Image) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gemini-3.1-flash-image-preview',
      prompt: 'Transform this photo into a watercolor painting style',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/photo.jpg'],
      metadata: {
        resolution: '2K'
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
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gemini-3.1-flash-image-preview",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
