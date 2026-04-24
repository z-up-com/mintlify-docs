# Grok Imagine 1.0 Image Generation

> Async image generation with grok-imagine-1.0

* Unified image generation endpoint
* Async task mode, returns a task ID
* Set `model` to `grok-imagine-1.0`

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

  * `grok-imagine-1.0`
</ParamField>

<ParamField body="prompt" type="string" required>
  Prompt for image generation, multilingual supported
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Image generation size

  Supported formats:

  * `1:1` - Square (default)
  * `16:9` - Landscape widescreen
  * `9:16` - Portrait tall screen
  * `3:2` - Landscape
  * `2:3` - Portrait
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images to generate

  Range: 1-10 (minimum 1, maximum 10)

  ⚠️ Note: use a plain number (for example `1`), do not wrap it in quotes
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
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0",
      "prompt": "An orange cat sitting on a sunny windowsill, oil painting style",
      "size": "1:1",
      "n": 1
    }'
  ```

  ```python Python theme={null}
  import requests

  resp = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0",
          "prompt": "An orange cat sitting on a sunny windowsill, oil painting style",
          "size": "1:1",
          "n": 1,
      },
  )

  print(resp.status_code)
  print(resp.json())
  ```

  ```javascript JavaScript theme={null}
  const resp = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0',
      prompt: 'An orange cat sitting on a sunny windowsill, oil painting style',
      size: '1:1',
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
    "model": "grok-imagine-1.0",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
