> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Grok Imagine 1.0 Image Editing

> Async image editing with grok-imagine-1.0-edit based on reference images

* Unified image editing endpoint
* Async task mode, returns a task ID
* Set `model` to `grok-imagine-1.0-edit`

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get API Key from [API Key Management](https://toapis.com/console/token)

  Header example:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" required>
  Model name, use:

  * `grok-imagine-1.0-edit`
</ParamField>

<ParamField body="prompt" type="string" required>
  Prompt for image editing, multilingual supported
</ParamField>

<ParamField body="image_urls" type="string[]" required>
  Source image URL array, at least one image

  Example:

  * `["https://example.com/original.png"]`

  You can upload first via [Upload Image API](/docs/en/api-reference/uploads/images)
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images, recommended range 1-10
</ParamField>

## Response

<ResponseField name="id" type="string">
  Unique task ID
</ResponseField>

<ResponseField name="object" type="string">
  Always `generation.task`
</ResponseField>

<ResponseField name="status" type="string">
  Task status: `queued` / `in_progress` / `completed` / `failed`
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/edits \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0-edit",
      "prompt": "Change the background to a starry sky, keep the main subject",
      "image_urls": ["https://example.com/original.png"],
      "n": 1
    }'
  ```

  ```python Python theme={null}
  import requests

  resp = requests.post(
      "https://toapis.com/v1/images/edits",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0-edit",
          "prompt": "Change the background to a starry sky, keep the main subject",
          "image_urls": ["https://example.com/original.png"],
          "n": 1,
      },
  )

  print(resp.status_code)
  print(resp.json())
  ```

  ```javascript JavaScript theme={null}
  const resp = await fetch('https://toapis.com/v1/images/edits', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0-edit',
      prompt: 'Change the background to a starry sky, keep the main subject',
      image_urls: ['https://example.com/original.png'],
      n: 1,
    }),
  });

  console.log(resp.status);
  console.log(await resp.json());
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_01JNXXXXXXXXXXXXXXXXXX",
    "object": "generation.task",
    "model": "grok-imagine-1.0-edit",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
