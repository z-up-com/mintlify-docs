> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Flux 2.0 Image Generation

> Generate high-quality images using Flux 2.0 model, supporting text-to-image and image-to-image

* Flux 2.0 image generation model
* Choose `flux-2-flex` (faster, for quick iteration) or `flux-2-pro` (higher quality, better details) via the model parameter
* Supports text-to-image and image-to-image, up to 8 reference images
* Billed by resolution (1K/2K)
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

  * `flux-2-flex` - Faster, suitable for quick iteration
  * `flux-2-pro` - Higher quality, better details
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for image generation
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Image aspect ratio

  Supported aspect ratios:

  * `1:1` - Square (default)
  * `4:3` / `3:4` - Traditional display ratio
  * `16:9` / `9:16` - Widescreen/portrait
  * `3:2` / `2:3` - Standard photo
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL array for image-to-image

  **⚠️ URL format only (base64 not supported)**

  * Publicly accessible image URL (http\:// or https\://)
  * Use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limits:**

  * Maximum 8 reference images
</ParamField>

<ParamField body="metadata" type="object">
  Metadata parameters for additional configuration options

  <Expandable title="Supported metadata fields">
    <ParamField body="resolution" type="string" default="1K">
      Output image resolution

      Options:

      * `1K` - Standard resolution (default)
      * `2K` - High resolution

      **Resolution vs. aspect ratio pixel dimensions:**

      | Aspect Ratio | 1K Size   | 2K Size   |
      | ------------ | --------- | --------- |
      | 1:1          | 1024×1024 | 2048×2048 |
      | 4:3          | 1365×1024 | 2730×2048 |
      | 3:4          | 1024×1365 | 2048×2730 |
      | 16:9         | 1820×1024 | 3640×2048 |
      | 9:16         | 1024×1820 | 2048×3640 |
      | 3:2          | 1536×1024 | 3072×2048 |
      | 2:3          | 1024×1536 | 2048×3072 |

      **Note:** Different resolutions have different billing; 2K costs more than 1K.
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

### Case 1: Text-to-Image (Quick Iteration)

```json theme={null}
{
  "model": "flux-2-flex",
  "prompt": "A cute orange cat sunbathing on a windowsill",
  "size": "1:1",
  "metadata": {
    "resolution": "1K"
  }
}
```

### Case 2: Text-to-Image (High Quality Output)

```json theme={null}
{
  "model": "flux-2-pro",
  "prompt": "Future city skyline, neon lights, cyberpunk style, cinematic quality",
  "size": "16:9",
  "metadata": {
    "resolution": "2K"
  }
}
```

### Case 3: Image-to-Image (Multiple Reference Images)

```json theme={null}
{
  "model": "flux-2-pro",
  "prompt": "Keep character style, generate a scene of running in the forest",
  "size": "1:1",
  "image_urls": [
    "https://example.com/ref1.jpg",
    "https://example.com/ref2.jpg"
  ],
  "metadata": {
    "resolution": "2K"
  }
}
```

### Case 4: Portrait Poster

```json theme={null}
{
  "model": "flux-2-flex",
  "prompt": "Concert poster, minimalist style, dark background",
  "size": "9:16",
  "metadata": {
    "resolution": "1K"
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
      "model": "flux-2-flex",
      "prompt": "A cute orange cat sunbathing on a windowsill",
      "size": "1:1",
      "metadata": {
        "resolution": "1K"
      }
    }'
  ```

  ```bash cURL (Image-to-Image) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "flux-2-pro",
      "prompt": "Keep character style, generate a scene of running in the forest",
      "size": "1:1",
      "image_urls": ["https://example.com/ref1.jpg"],
      "metadata": {
        "resolution": "2K"
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
          "model": "flux-2-flex",
          "prompt": "A cute orange cat sunbathing on a windowsill",
          "size": "1:1",
          "metadata": {
              "resolution": "1K"
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
          "model": "flux-2-pro",
          "prompt": "Keep character style, generate a scene of running in the forest",
          "size": "1:1",
          "image_urls": ["https://example.com/ref1.jpg"],
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
      model: 'flux-2-flex',
      prompt: 'A cute orange cat sunbathing on a windowsill',
      size: '1:1',
      metadata: {
        resolution: '1K'
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
      model: 'flux-2-pro',
      prompt: 'Keep character style, generate a scene of running in the forest',
      size: '1:1',
      image_urls: ['https://example.com/ref1.jpg'],
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
    "model": "flux-2-flex",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
