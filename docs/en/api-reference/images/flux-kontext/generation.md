---
title: "Flux Kontext Image Generation"
---
> Generate and edit images using Flux Kontext model, supporting text-to-image and image editing

* Flux Kontext image generation and editing model
* Choose `flux-kontext-pro` (Pro) or `flux-kontext-max` (Max, higher quality) via the model parameter
* Supports text-to-image and image editing modes
* With reference image = image editing; without = text-to-image
* Fixed billing per request
* Async task mode, returns `task_id` for querying results

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
  Image generation model name

  Options:

  * `flux-kontext-pro` - Pro version
  * `flux-kontext-max` - Max version, higher quality
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for image generation
</ParamField>

<ParamField body="size" type="string">
  Image aspect ratio

  Default:

  * With reference image: `match_input_image` (match input image)
  * Without reference image: `16:9`

  Supported aspect ratios:

  | Value               | Description                                  |
  | ------------------- | -------------------------------------------- |
  | `match_input_image` | Match input image ratio (image editing only) |
  | `1:1`               | Square                                       |
  | `4:3` / `3:4`       | Traditional display ratio                    |
  | `16:9` / `9:16`     | Widescreen/portrait                          |
  | `3:2` / `2:3`       | Standard photo                               |
  | `21:9` / `9:21`     | Ultrawide/ultra-narrow                       |
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL array for image editing

  **⚠️ URL format only (base64 not supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * Use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limits:**

  * Maximum 1 image
  * With image = image editing mode; without = text-to-image mode
</ParamField>

<ParamField body="metadata" type="object">
  Metadata parameters for additional configuration options

  <Expandable title="Supported metadata fields">
    <ParamField body="response_format" type="string" default="png">
      Output image format

      Options: `png`, `jpg`
    </ParamField>

    <ParamField body="safety_tolerance" type="integer" default={2}>
      Safety filter tolerance

      Range: `0` - `6`, higher values = higher tolerance
    </ParamField>

    <ParamField body="prompt_upsampling" type="boolean" default={false}>
      Enable prompt enhancement

      * `true`: Auto-optimize/expand prompt
      * `false`: Use original prompt (default)
    </ParamField>
  </Expandable>
</ParamField>

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
  Task creation timestamp (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Task metadata
</ResponseField>

## Use Cases

### Case 1: Text-to-Image

```json theme={null}
{
  "model": "flux-kontext-pro",
  "prompt": "Cyberpunk city at night, neon lights flickering",
  "size": "16:9",
  "metadata": {
    "response_format": "png",
    "safety_tolerance": 2
  }
}
```

### Case 2: Image Editing

```json theme={null}
{
  "model": "flux-kontext-max",
  "prompt": "Transform this photo into watercolor style",
  "size": "match_input_image",
  "image_urls": ["https://example.com/photo.jpg"],
  "metadata": {
    "response_format": "jpg",
    "prompt_upsampling": true
  }
}
```

### Case 3: High-Quality Text-to-Image (Max Model)

```json theme={null}
{
  "model": "flux-kontext-max",
  "prompt": "A cute orange cat sunbathing on a windowsill, photorealistic style",
  "size": "1:1",
  "metadata": {
    "response_format": "png",
    "safety_tolerance": 3
  }
}
```

<Note>
  **Query Task Results**

  Image generation is an async task. After submission, a task ID is returned. Use the [Get Task Status](/en/api-reference/tasks/status) endpoint to query generation progress and results.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "flux-kontext-pro",
      "prompt": "Cyberpunk city at night, neon lights flickering",
      "size": "16:9",
      "metadata": {
        "response_format": "png"
      }
    }'
  ```

  ```bash cURL (Image Editing) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "flux-kontext-max",
      "prompt": "Transform this photo into watercolor style",
      "size": "match_input_image",
      "image_urls": ["https://example.com/photo.jpg"],
      "metadata": {
        "response_format": "jpg"
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
          "model": "flux-kontext-pro",
          "prompt": "Cyberpunk city at night, neon lights flickering",
          "size": "16:9",
          "metadata": {
              "response_format": "png"
          }
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```python Python (Image Editing) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "flux-kontext-max",
          "prompt": "Transform this photo into watercolor style",
          "size": "match_input_image",
          "image_urls": ["https://example.com/photo.jpg"],
          "metadata": {
              "response_format": "jpg"
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
      model: 'flux-kontext-pro',
      prompt: 'Cyberpunk city at night, neon lights flickering',
      size: '16:9',
      metadata: {
        response_format: 'png'
      }
    })
  });

  const task = await response.json();
  console.log(`Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
  ```

  ```javascript JavaScript (Image Editing) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'flux-kontext-max',
      prompt: 'Transform this photo into watercolor style',
      size: 'match_input_image',
      image_urls: ['https://example.com/photo.jpg'],
      metadata: {
        response_format: 'jpg'
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
    "model": "flux-kontext-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
