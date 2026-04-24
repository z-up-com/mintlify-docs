> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Grok Video Generation

> Generate high-quality videos using Grok Video model

* Grok Video generation model
* Select `grok-video-3` model via the model parameter
* Supports text-to-video and image-to-video
* Async task management, query results via task ID

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `images`. Please use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload images first, then call this API with the returned URL.
</Warning>

## Authorizations

<ParamField header="Authorization" type="string" required>
  All API requests require Bearer Token authentication

  Get API Key: Visit the [API Key Management Page](https://toapis.com/console/token) to obtain your API Key

  Add to request headers:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="grok-video-3" required>
  Video generation model name

  Supported models:

  * `grok-video-3` - Grok video generation model

  Example: `"grok-video-3"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for video generation
</ParamField>

<ParamField body="duration" type="integer" default="10">
  Video duration in seconds

  Supported values:

  * `10` (default)
  * `15`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Video aspect ratio

  Supported formats:

  * `16:9` (landscape)
  * `9:16` (portrait)
</ParamField>

<ParamField body="images" type="string[]">
  Array of reference image URLs for image-to-video generation

  **⚠️ Only URL format is supported (base64 is no longer supported)**

  * Publicly accessible image URLs (http\:// or https\://)
  * Use the [Upload Image API](/docs/en/api-reference/uploads/images) to upload local images and get URLs
</ParamField>

<ParamField body="metadata" type="object">
  Task metadata

  <Expandable title="metadata properties">
    <ParamField body="resolution" type="string">
      Video resolution

      Supported values:

      * `720P`
      * `1080P`
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

<RequestExample>
  ```bash cURL (Text-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "A golden retriever running on a green field in sunny weather",
      "duration": 10,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "1080P"
      }
    }'
  ```

  ```bash cURL (Image-to-Video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "Animate this dog running across the field",
      "images": ["https://example.com/dog.jpg"],
      "duration": 10,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720P"
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
          "model": "grok-video-3",
          "prompt": "A golden retriever running on a green field in sunny weather",
          "duration": 10,
          "aspect_ratio": "16:9",
          "metadata": {
              "resolution": "1080P"
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
      model: 'grok-video-3',
      prompt: 'A golden retriever running on a green field in sunny weather',
      duration: 10,
      aspect_ratio: '16:9',
      metadata: {
        resolution: '1080P'
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
    "id": "video_abc123def456",
    "object": "generation.task",
    "model": "grok-video-3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
