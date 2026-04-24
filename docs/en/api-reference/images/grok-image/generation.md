---
title: "Grok Image Generation"
---
> Generate images using Grok models, supporting text-to-image and image-to-image

* Unified image generation API
* Use model parameter to select `grok-4-1-image` or `grok-4-2-image` model
* Supports text-to-image and image-to-image
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

<ParamField body="model" type="string" required>
  Image generation model name

  Supported models:

  * `grok-4-1-image` - Grok 4.1 image generation model
  * `grok-4-2-image` - Grok 4.2 image generation model

  Example: `"grok-4-1-image"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for image generation

  Maximum 1000 characters
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Image aspect ratio

  Supported formats:

  * `1:1` - Square (default)
  * `2:3` - Portrait
  * `3:2` - Landscape
  * `16:9` - Widescreen landscape
  * `9:16` - Widescreen portrait
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images to generate

  Supports 1, 2, 4. Charges will be pre-deducted based on the number

  Default: 1

  **⚠️ Note:** Must enter a plain number (e.g., `1`), do not use quotes or it will cause an error
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL list for image-to-image generation

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limitations:**

  * Maximum 5 images
  * Single image up to 10MB
  * Supported formats: jpeg, jpg, png, webp
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
      "model": "grok-4-1-image",
      "prompt": "A cyberpunk futuristic city at night",
      "size": "16:9",
      "n": 1
    }'
  ```

  ```bash cURL (Image-to-Image) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-4-1-image",
      "prompt": "Transform this image into watercolor painting style",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/photo.jpg"
      ]
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
          "model": "grok-4-1-image",
          "prompt": "A cyberpunk futuristic city at night",
          "size": "16:9",
          "n": 1
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
      model: 'grok-4-1-image',
      prompt: 'A cyberpunk futuristic city at night',
      size: '16:9',
      n: 1
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
    "id": "img_abc123def456",
    "object": "generation.task",
    "model": "grok-4-1-image",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```

  ```json 400 theme={null}
  {
    "error": {
      "code": "invalid_request",
      "message": "The 'prompt' field is required.",
      "param": "prompt",
      "type": "invalid_request_error"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": "unauthorized",
      "message": "Invalid API key",
      "type": "authentication_error"
    }
  }
  ```
</ResponseExample>
