> ## Documentation Index
>
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Kling Video O1 Video Generation

> Reasoning-enhanced model for highest quality video generation with `<<<image_N>>>` syntax

- Reasoning-enhanced model for highest quality video generation
- Async processing mode, returns task ID for subsequent queries
- Unified text-to-video and image-to-video interface
- Supports `<<<image_N>>>` syntax to reference images in prompts
- Supports standard mode (720P) and professional mode (1080P)
- Duration options: 5 or 10 seconds

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
  Video generation model name, fixed as `kling-video-o1`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text prompt describing the video content

Use `<<<image_N>>>` syntax to reference images in `image_urls`. `N` starts from 1.

Example: `"Make the person in <<<image_1>>> wave at the camera"`

  <Note>
    If you pass images but the prompt has no `<<<image_N>>>` reference, the system will automatically prepend `<<<image_1>>>` to the prompt.
  </Note>
</ParamField>

<ParamField body="mode" type="string" default="std">
  Generation mode

Options:

- `std` - Standard mode (720P)
- `pro` - Professional mode (1080P)

Default: `std`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Video duration (seconds)

Options: `5` or `10`

Default: `5`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Video aspect ratio

Options:

- `16:9` - Landscape (default)
- `9:16` - Portrait
- `1:1` - Square

Default: `16:9`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Image URL array for image references

Reference images in the prompt using `<<<image_N>>>` (N starts from 1)

Example: `["https://example.com/photo.jpg"]`

  <Warning>
    * Image URLs must be publicly accessible, no hotlink protection
    * In image-to-video mode, `aspect_ratio` may be overridden by the actual image ratio
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Extended parameters for Kling Video O1

  <Expandable title="Supported metadata fields">
    <ParamField body="watermark" type="boolean">
      Whether to add watermark
    </ParamField>
  </Expandable>
</ParamField>

## Image Reference Syntax

The Video O1 model uses `<<<image_N>>>` syntax in prompts to reference images:

| Syntax          | Description                              |
| --------------- | ---------------------------------------- |
| `<<<image_1>>>` | References the 1st image in `image_urls` |
| `<<<image_2>>>` | References the 2nd image in `image_urls` |

<Note>
  **Auto-reference**: If you pass `image_urls` but the prompt has no `<<<image_N>>>` reference, the system will automatically prepend `<<<image_1>>>` to the prompt.
</Note>

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

- `queued` - Waiting to be processed
- `in_progress` - Processing
- `completed` - Successfully completed
- `failed` - Failed

</ResponseField>

<ResponseField name="progress" type="integer">
  Task progress percentage (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation Unix timestamp
</ResponseField>

<ResponseField name="metadata" type="object">
  Task metadata
</ResponseField>

## Use Cases

### Case 1: Text-to-Video (Highest Quality)

```json theme={null}
{
  "model": "kling-video-o1",
  "prompt": "Golden hour city skyline, cinematic lens quality",
  "mode": "pro",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### Case 2: Image Reference (Single Image)

```json theme={null}
{
  "model": "kling-video-o1",
  "prompt": "Make the person in <<<image_1>>> wave at the camera",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "pro",
  "duration": 5
}
```

### Case 3: Multiple Image References

```json theme={null}
{
  "model": "kling-video-o1",
  "prompt": "The character in <<<image_1>>> walks toward the scene in <<<image_2>>>",
  "image_urls": [
    "https://example.com/character.jpg",
    "https://example.com/scene.jpg"
  ],
  "mode": "pro",
  "duration": 5
}
```

### Case 4: Images Without Explicit Reference (Auto-add)

```json theme={null}
{
  "model": "kling-video-o1",
  "prompt": "Person slowly turns head and smiles",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

> The system will automatically prepend `<<<image_1>>>` to the prompt, equivalent to `"<<<image_1>>> Person slowly turns head and smiles"`.

<Note>
  **Query Task Results**

Video generation is an async task that returns a task ID upon submission. Use the [Get Task Status](/en/api-reference/tasks/status) endpoint to query generation progress and results.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "kling-video-o1",
      "prompt": "Make the person in <<<image_1>>> wave at the camera",
      "image_urls": ["https://example.com/portrait.jpg"],
      "mode": "std",
      "duration": 5,
      "aspect_ratio": "16:9"
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
        "model": "kling-video-o1",
        "prompt": "Make the person in <<<image_1>>> wave at the camera",
        "image_urls": ["https://example.com/portrait.jpg"],
        "mode": "std",
        "duration": 5,
        "aspect_ratio": "16:9"
    }
)

task = response.json()
print(f"Task ID: {task['id']}")
print(f"Status: {task['status']}")
```

```javascript JavaScript theme={null}
const response = await fetch("https://toapis.com/v1/videos/generations", {
  method: "POST",
  headers: {
    Authorization: "Bearer your-ToAPIs-key",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    model: "kling-video-o1",
    prompt: "Make the person in <<<image_1>>> wave at the camera",
    image_urls: ["https://example.com/portrait.jpg"],
    mode: "std",
    duration: 5,
    aspect_ratio: "16:9",
  }),
});

const task = await response.json();
console.log(`Task ID: ${task.id}`);
console.log(`Status: ${task.status}`);
```

</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "kling-video-o1",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
