# Sora2 Create Character

> Extract characters from video for subsequent video generation

## Overview

The Sora2 character creation feature allows you to extract characters from existing videos. Once created, you can reuse the character in subsequent video generations to maintain character consistency.

<Note>
  **Important:**

  * Video must contain **audio** and **identifiable characters**
  * Time range limit: **minimum 1 second, maximum 3 seconds**
  * Either `url` or `from_task` is required - must provide one
  * `prompt` parameter is **not required** in this mode
  * After creation, the character task ID can be used for subsequent video generation
</Note>

## Authentication

<ParamField header="Authorization" type="string" required>
  Bearer Token authentication

  ```
  Authorization: Bearer YOUR_API_KEY
  ```

  Get your API Key from the [API Key Management Page](https://toapis.com/console/token)
</ParamField>

## Request Parameters

<ParamField body="model" type="string" default="sora-2" required>
  Video generation model name

  Supported models:

  * `sora-2` - Standard version
  * `sora-2-pro` - Professional version (higher quality)
  * `sora-2-vip` - VIP version, higher priority
</ParamField>

<ParamField body="timestamps" type="string" required>
  Character appearance timestamp range

  Unit is seconds, format is `"start_second,end_second"`

  **Constraints:**

  * Time range difference minimum: **1 second**
  * Time range difference maximum: **3 seconds**

  Example: `"1,3"` means the character appearing from second 1 to second 3 in the video
</ParamField>

<ParamField body="url" type="string">
  Video URL containing the character to extract

  **Requirements:**

  * Video must contain audio
  * Video must contain identifiable characters

  **Note:** Either `url` or `from_task` required

  Example: `"https://example.com/my-video.mp4"`
</ParamField>

<ParamField body="from_task" type="string">
  Task ID of a previously generated video

  Create character from an existing video generation task

  **Note:** Either `url` or `from_task` required

  Example: `"task_01KBYT59JDHB4A3KDDR9N9JVWP"`
</ParamField>

## Response Fields

<ResponseField name="id" type="string">
  Unique task identifier for querying character creation status

  After completion, this character task ID can be used in video generation via the `character_url` parameter
</ResponseField>

<ResponseField name="object" type="string">
  Object type, always `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Model name used
</ResponseField>

<ResponseField name="status" type="string">
  Task status:

  * `queued` - Queued for processing
  * `in_progress` - Processing
  * `completed` - Successfully completed
  * `failed` - Failed
</ResponseField>

<ResponseField name="progress" type="integer">
  Task progress percentage (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation time (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Task completion time (Unix timestamp, only available after completion)
</ResponseField>

<ResponseField name="result" type="object">
  Character creation result (only available after completion)

  Contains created character information such as character ID, name, profile picture, etc.
</ResponseField>

<RequestExample>
  ```bash cURL (From Video URL) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer YOUR_API_KEY' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "url": "https://example.com/character-video.mp4",
      "timestamps": "1,3"
    }'
  ```

  ```bash cURL (From Existing Task) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer YOUR_API_KEY' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "from_task": "task_01KBYT59JDHB4A3KDDR9N9JVWP",
      "timestamps": "1,3"
    }'
  ```

  ```python Python theme={null}
  import requests

  # Create character from video URL
  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer YOUR_API_KEY",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "url": "https://example.com/character-video.mp4",
          "timestamps": "1,3"
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")

  # Create character from existing task
  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer YOUR_API_KEY",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "from_task": "task_01KBYT59JDHB4A3KDDR9N9JVWP",
          "timestamps": "1,3"
      }
  )
  ```

  ```javascript JavaScript theme={null}
  // Create character from video URL
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer YOUR_API_KEY',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      url: 'https://example.com/character-video.mp4',
      timestamps: '1,3'
    })
  });

  const task = await response.json();
  console.log(`Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
  ```

  ```go Go theme={null}
  package main

  import (
      "bytes"
      "encoding/json"
      "fmt"
      "io"
      "net/http"
  )

  func main() {
      url := "https://toapis.com/v1/videos/generations"

      payload := map[string]interface{}{
          "model":      "sora-2",
          "url":        "https://example.com/character-video.mp4",
          "timestamps": "1,3",
      }

      jsonData, _ := json.Marshal(payload)

      req, _ := http.NewRequest("POST", url, bytes.NewBuffer(jsonData))
      req.Header.Set("Authorization", "Bearer YOUR_API_KEY")
      req.Header.Set("Content-Type", "application/json")

      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          panic(err)
      }
      defer resp.Body.Close()

      body, _ := io.ReadAll(resp.Body)
      fmt.Println(string(body))
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 (Submitted) theme={null}
  {
    "id": "task_01KBYT59JDHB4A3KDDR9N9JVWP",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```

  ```json 200 (Completed - Query Result) theme={null}
  {
    "id": "task_01KC0JZCMTMQ70D68XTM56Q5D0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "completed",
    "progress": 100,
    "created_at": 1765251461,
    "completed_at": 1765251507,
    "result": {
      "type": "character",
      "data": {
        "characters": [
          {
            "id": "ch_6937998961208191a45ef08447a554df",
            "display_name": "Turbo Whiskers",
            "profile_picture_url": "https://upload.toapis.com/f/image/character_task_xxx.jpg",
            "username": "duksvfkf.turbo_whis"
          }
        ]
      }
    },
    "metadata": {}
  }
  ```

  ```json 400 (Invalid Request) theme={null}
  {
    "error": {
      "code": 400,
      "message": "Invalid request: timestamps range must be between 1-3 seconds",
      "type": "invalid_request_error"
    }
  }
  ```

  ```json 401 (Authentication Failed) theme={null}
  {
    "error": {
      "code": 401,
      "message": "Authentication failed, please check your API key",
      "type": "authentication_error"
    }
  }
  ```

  ```json 402 (Insufficient Balance) theme={null}
  {
    "error": {
      "code": 402,
      "message": "Insufficient account balance, please top up",
      "type": "payment_required"
    }
  }
  ```
</ResponseExample>

## Usage Flow

<Steps>
  <Step title="Submit Character Creation Request">
    Call this API with a video URL or existing task ID containing the character, along with the time range
  </Step>

  <Step title="Get Task ID">
    API returns task ID with status `queued` or `in_progress`
  </Step>

  <Step title="Query Task Status">
    Use the [Query Video Task Status](/en/api-reference/tasks/video-status) API to poll task progress
  </Step>

  <Step title="Use Character in Video Generation">
    After character creation completes, use `character_url` parameter to reference the character task ID in subsequent video generation
  </Step>
</Steps>

## Best Practices

1. **Choose Clear Character Segments**: Select 1-3 second segments where character features are most prominent
2. **Ensure Video Quality**: High-definition video allows better character feature extraction
3. **Include Audio**: Video must contain an audio track
4. **Avoid Multiple Characters**: The selected time range should ideally contain only one main character
