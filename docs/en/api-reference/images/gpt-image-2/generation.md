# GPT-Image-2 Image Generation

> Generate images with gpt-image-2, supporting text-to-image and reference_images based image generation

* Unified image generation API
* Select the `gpt-image-2` model via the `model` parameter
* Supports text-to-image, single-reference, and multi-reference image generation
* Async task management with task ID based result lookup

<Warning>
  **Important Change**: For better performance and cost control, base64 image data is no longer supported in `image_urls` or `reference_images`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) first to upload images and obtain a URL.
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

<ParamField body="model" type="string" default="gpt-image-2" required>
  Image generation model name

  Example: `"gpt-image-2"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text prompt for image generation

  Maximum 4000 characters
</ParamField>

<ParamField body="size" type="string" default="1024x1024">
  Output image size

  Common values:

  * `1024x1024`
  * `1536x1024`
  * `1024x1536`
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images to generate

  Default: 1
</ParamField>

<ParamField body="response_format" type="string" default="url">
  Response format

  The API returns image URLs, so `url` is the recommended value
</ParamField>

<ParamField body="reference_images" type="string[]">
  Reference image URLs for image-to-image generation

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible URLs (`http://` or `https://`)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local files and get URLs
  * Supports single-image and multi-image reference
</ParamField>

<ParamField body="image_urls" type="string[]">
  Backward-compatible reference image field

  ToAPIs automatically normalizes it to `reference_images`
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

  * `queued`
  * `in_progress`
  * `completed`
  * `failed`
</ResponseField>

<ResponseField name="progress" type="integer">
  Task progress percentage (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation timestamp (Unix timestamp)
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-image-2",
      "prompt": "Generate a futuristic city night poster with neon lighting and cinematic composition",
      "n": 1,
      "size": "1024x1024",
      "response_format": "url"
    }'
  ```

  ```bash cURL (Image-to-Image) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-image-2",
      "prompt": "Keep the core structure and restyle it into a cyberpunk scene with stronger lighting and details",
      "reference_images": [
        "https://example.com/source.png"
      ],
      "response_format": "url"
    }'
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gpt-image-2',
      prompt: 'Generate a futuristic city night poster with neon lighting and cinematic composition',
      n: 1,
      size: '1024x1024',
      response_format: 'url'
    })
  });

  const task = await response.json();
  console.log(task.id, task.status);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gpt-image-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
