> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Gemini-3.1-Flash Official Image Generation

> Generate images using Google Gemini 3.1 Flash official direct-connect model with thinking mode and extreme aspect ratios

* Direct connection to Google Vertex AI using the Gemini 3.1 Flash Image native API
* Select the `gemini-3.1-flash-image-preview-official` model via the model parameter
* Supports text-to-image and image-to-image generation
* Supports native generation parameters like temperature, topP, and maxOutputTokens
* Supports resolution (1K/2K/4K), personGeneration, and imageOutputOptions image configuration
* Supports thinkingConfig thinking mode for improved complex scene generation
* Supports safetySettings for content safety control
* Supports extreme aspect ratios (4:1, 8:1)
* Asynchronous task management with task ID for result querying

<Warning>
  **Important Change**: For better performance and cost control, we no longer support passing base64 image data directly in `image_urls`. Please use the [Upload Image endpoint](/docs/en/api-reference/uploads/images) to upload images first, then use the returned URL in this API.
</Warning>

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get API Key: Visit the [API Key Management page](https://toapis.com/console/token) to obtain your API Key

  Add to request headers:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" default="gemini-3.1-flash-image-preview-official" required>
  Image generation model name

  Example: `"gemini-3.1-flash-image-preview-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Text description for image generation
</ParamField>

<ParamField body="size" type="string">
  Image aspect ratio

  Supported formats:

  * `1:1` - Square
  * `3:2` / `2:3`
  * `3:4` / `4:3`
  * `4:5` / `5:4`
  * `9:16` / `16:9`
  * `21:9`
  * `4:1` / `8:1` - Extreme widescreen ratios
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Number of images to generate

  Fixed at 1
</ParamField>

<ParamField body="image_urls" type="string[]">
  Reference image URL array for image-to-image generation or editing

  **⚠️ Only URL format is supported (base64 is no longer supported)**

  * Publicly accessible image URLs (http\:// or https\://)
  * Use the [Upload Image endpoint](/docs/en/api-reference/uploads/images) to upload local images and get URLs

  **Limits:**

  * Maximum 14 images
  * Maximum file size per image: 10MB
  * Supported formats: .jpeg, .jpg, .png, .webp
</ParamField>

<ParamField body="metadata" type="object">
  Vertex AI native extension parameters

  <Expandable title="Show metadata fields">
    <ParamField body="temperature" type="number">
      Generation temperature, controls output randomness

      Range: `0.0` - `2.0`, default `1.0`
    </ParamField>

    <ParamField body="topP" type="number">
      Top-P sampling parameter

      Range: `0.0` - `1.0`, default `0.95`
    </ParamField>

    <ParamField body="maxOutputTokens" type="integer">
      Maximum output tokens

      Default `32768`
    </ParamField>

    <ParamField body="resolution" type="string">
      Output image resolution, automatically mapped to Vertex AI native imageSize

      Options: `1K`, `2K`, `4K`, default `1K`
    </ParamField>

    <ParamField body="personGeneration" type="string">
      Person generation control

      Options:

      * `ALLOW_ALL` - Allow generating all people (including adults and children)
      * `ALLOW_ADULT` - Allow generating adults only
      * `ALLOW_NONE` - Disallow generating people
    </ParamField>

    <ParamField body="imageOutputOptions" type="object">
      Image output format configuration

      <Expandable title="imageOutputOptions fields">
        <ParamField body="mimeType" type="string">
          Output image format

          Options: `image/png`, `image/jpeg`, `image/webp`
        </ParamField>

        <ParamField body="compressionQuality" type="integer">
          Compression quality (JPEG only)
        </ParamField>
      </Expandable>
    </ParamField>

    <ParamField body="thinkingConfig" type="object">
      Thinking mode configuration. When enabled, the model reasons before generating images, improving results for complex scenes

      <Expandable title="thinkingConfig fields">
        <ParamField body="thinkingBudget" type="integer">
          Thinking token budget, controls the depth of model reasoning

          Range: `0` - `24576`, default is determined by the model
        </ParamField>

        <ParamField body="thinkingLevel" type="string">
          Thinking level

          Options: `LOW`, `MEDIUM`, `HIGH`, `MINIMAL`
        </ParamField>
      </Expandable>
    </ParamField>

    <ParamField body="safetySettings" type="array">
      Safety settings array for content safety filtering

      <Expandable title="safetySettings elements">
        <ParamField body="category" type="string">
          Safety category

          Options: `HARM_CATEGORY_HATE_SPEECH`, `HARM_CATEGORY_DANGEROUS_CONTENT`, `HARM_CATEGORY_SEXUALLY_EXPLICIT`, `HARM_CATEGORY_HARASSMENT`
        </ParamField>

        <ParamField body="threshold" type="string">
          Filtering threshold

          Options: `OFF`, `BLOCK_LOW_AND_ABOVE`, `BLOCK_MEDIUM_AND_ABOVE`, `BLOCK_ONLY_HIGH`
        </ParamField>
      </Expandable>
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
      "model": "gemini-3.1-flash-image-preview-official",
      "prompt": "An ink wash painting of mountains and rivers, layered peaks with swirling mist",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "temperature": 1.0,
        "topP": 0.95,
        "resolution": "1K",
        "personGeneration": "ALLOW_ALL",
        "thinkingConfig": {
          "thinkingLevel": "HIGH"
        }
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
          "model": "gemini-3.1-flash-image-preview-official",
          "prompt": "An ink wash painting of mountains and rivers, layered peaks with swirling mist",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "temperature": 1.0,
              "topP": 0.95,
              "resolution": "1K",
              "personGeneration": "ALLOW_ALL",
              "thinkingConfig": {
                  "thinkingLevel": "HIGH"
              }
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
      model: 'gemini-3.1-flash-image-preview-official',
      prompt: 'An ink wash painting of mountains and rivers, layered peaks with swirling mist',
      size: '16:9',
      n: 1,
      metadata: {
        temperature: 1.0,
        topP: 0.95,
        resolution: '1K',
        personGeneration: 'ALLOW_ALL',
        thinkingConfig: {
          thinkingLevel: 'HIGH'
        }
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
    "model": "gemini-3.1-flash-image-preview-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
