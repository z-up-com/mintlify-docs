---
title: "Seedream-4.5 Image Generation"
---
> Generate high-quality images using Seedream 4.5 model, latest version

* Seedream 4.5 latest high-quality image generation model
* Use model parameter to select `doubao-seedream-4-5` model
* Supports text-to-image generation with superior quality
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

<ParamField body="model" type="string" default="doubao-seedream-4-5" required>
  Image generation model name

  Example: `"doubao-seedream-4-5"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for image generation
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Image aspect ratio

  Supported aspect ratios:

  * `1:1` - Square (default)
  * `4:3` - Landscape 4:3
  * `3:4` - Portrait 3:4
  * `16:9` - Landscape widescreen
  * `9:16` - Portrait vertical
  * `3:2` - Landscape 3:2
  * `2:3` - Portrait 2:3
  * `21:9` - Ultra-wide
  * `9:21` - Ultra-tall
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images to generate

  Range: 1-15 (minimum 1, maximum 15)

  Default: 1

  **Note:**

  * The actual number of images that can be generated is affected by both this parameter and the number of input reference images. The total of reference images + generated images must be ≤ 15
  * **Must enter a plain number (e.g., `1`), do not use quotes or it will cause an error**

  Charges will be pre-deducted based on the number
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL list for image-to-image or image editing

  **⚠️ URL format only (base64 no longer supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * You can use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limitations:**

  * Maximum 10 images
  * Image formats: jpeg, png
  * Aspect ratio (width/height) range: \[1/3, 3]
  * Width and height (px) > 14
  * Size: not exceeding 10MB
  * Total pixels: not exceeding 6000×6000 px
</ParamField>

<ParamField body="metadata" type="object">
  Channel-specific parameters for advanced Seedream 4.5 model configuration

  <Expandable title="metadata properties">
    <ParamField body="metadata.resolution" type="string" default="2K">
      Image resolution

      Supported resolutions:

      * `2K` - Standard resolution (default)
      * `4K` - High definition

      > **Note:** Seedream-4.5 does not support 1K resolution

      **Resolution reference sizes:**

      | Resolution | 1:1 Size  | 16:9 Size |
      | ---------- | --------- | --------- |
      | 2K         | 2048x2048 | 2560x1440 |
      | 4K         | 4096x4096 | 5404x3040 |
    </ParamField>

    <ParamField body="metadata.optimize_prompt_options" type="object">
      Prompt optimization options

      **Properties:**

      * `mode` (string): Optimization mode
        * `standard`: Standard mode, generates higher quality content with longer processing time (default)
        * `fast`: Fast mode, generates content in shorter time with regular quality

      **Example:**

      ```json theme={null}
      "optimize_prompt_options": { "mode": "standard" }
      ```
    </ParamField>

    <ParamField body="metadata.sequential_image_generation" type="string" default="disabled">
      Sequential image generation mode (Doubao-specific feature)

      Controls whether to generate multiple images:

      * `disabled`: Disable sequential mode, generates only 1 image even with multiple reference images (default)
      * `auto`: Enable sequential mode, can generate multiple images

      **Usage Notes:**

      * ✅ Set `n: 3` or use `sequential_image_generation: "auto"` + `max_images: 3`
      * ✅ Supports text-to-image: up to 15 images from text prompt only
      * ✅ Supports image-to-image: provide `image_urls` to generate multiple variations
      * ⚠️ When `n > 1`, it will automatically be set to `auto`

      **Note:**

      * Total reference images + generated images must be ≤ 15
    </ParamField>

    <ParamField body="metadata.sequential_image_generation_options" type="object">
      Sequential image generation options

      Available when `sequential_image_generation` is set to `auto`

      **Properties:**

      * `max_images` (integer): Specify the number of images to generate, Range: 1-15

      **Example:**

      ```json theme={null}
      "sequential_image_generation_options": { "max_images": 3 }
      ```
    </ParamField>

    <ParamField body="metadata.watermark" type="boolean" default={true}>
      Whether to add a watermark to the generated image

      * `true`: Add watermark (default)
      * `false`: No watermark
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
      "model": "doubao-seedream-4-5",
      "prompt": "A cute panda playing in a bamboo forest",
      "size": "1:1",
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
      "model": "doubao-seedream-4-5",
      "prompt": "A cute panda playing in a bamboo forest",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/panda.jpg"
      ],
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Advanced Features) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedream-4-5",
      "prompt": "A cute panda playing in a bamboo forest",
      "size": "1:1",
      "n": 3,
      "image_urls": [
        "https://example.com/panda.jpg"
      ],
      "metadata": {
        "resolution": "4K",
        "optimize_prompt_options": { "mode": "standard" },
        "sequential_image_generation": "auto",
        "sequential_image_generation_options": { "max_images": 3 },
        "watermark": false
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
          "model": "doubao-seedream-4-5",
          "prompt": "A cute panda playing in a bamboo forest",
          "size": "1:1",
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
          "model": "doubao-seedream-4-5",
          "prompt": "A cute panda playing in a bamboo forest",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/panda.jpg"],
          "metadata": {
              "resolution": "2K"
          }
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```python Python (Advanced Features) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "doubao-seedream-4-5",
          "prompt": "A cute panda playing in a bamboo forest",
          "size": "1:1",
          "n": 3,
          "image_urls": ["https://example.com/panda.jpg"],
          "metadata": {
              "resolution": "4K",
              "optimize_prompt_options": {"mode": "standard"},
              "sequential_image_generation": "auto",
              "sequential_image_generation_options": {"max_images": 3},
              "watermark": False
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
      model: 'doubao-seedream-4-5',
      prompt: 'A cute panda playing in a bamboo forest',
      size: '1:1',
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
      model: 'doubao-seedream-4-5',
      prompt: 'A cute panda playing in a bamboo forest',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/panda.jpg'],
      metadata: {
        resolution: '2K'
      }
    })
  });

  const task = await response.json();
  console.log(`Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
  ```

  ```javascript JavaScript (Advanced Features) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'doubao-seedream-4-5',
      prompt: 'A cute panda playing in a bamboo forest',
      size: '1:1',
      n: 3,
      image_urls: ['https://example.com/panda.jpg'],
      metadata: {
        resolution: '4K',
        optimize_prompt_options: { mode: 'standard' },
        sequential_image_generation: 'auto',
        sequential_image_generation_options: { max_images: 3 },
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
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "doubao-seedream-4-5",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
