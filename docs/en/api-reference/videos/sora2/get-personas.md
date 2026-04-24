# Sora2 Query Character Status

> Query character creation task status and results

## Overview

Query the status and result of character creation tasks. Retrieve detailed information about created characters (character ID, display name, username, etc.) for use in subsequent video generation.

<Note>
  **Usage Notes:**

  * Character creation is an async task - poll this endpoint for results
  * Task completion returns character details
  * Character `username` can be referenced in prompts using `@username` format
  * Character task ID can be used via `character_url` parameter in video generation
</Note>

## Path Parameters

<ParamField path="task_id" type="string" required>
  Character creation task ID

  This is the task ID returned from the [Create Character](/en/api-reference/videos/sora2/create-persona) API

  Example: `"task_01KBZ0TZZ7C7M3WS1TJM0PH8J4"`
</ParamField>

## Authentication

<ParamField header="Authorization" type="string" required>
  Bearer Token authentication

  ```
  Authorization: Bearer YOUR_API_KEY
  ```

  Get your API Key from the [API Key Management Page](https://toapis.com/console/token)
</ParamField>

## Response Fields

<ResponseField name="id" type="string">
  Unique task identifier (character creation task ID)
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

<ResponseField name="expires_at" type="integer">
  Result expiration time (Unix timestamp, only available after completion)
</ResponseField>

<ResponseField name="result" type="object">
  Task result (only available when status is completed)

  `result.type` is `character`, and `result.data.characters` contains the character list
</ResponseField>

<ResponseField name="error" type="object">
  Error information (only available when status is failed)
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/characters_tasks/task_01KBZ0TZZ7C7M3WS1TJM0PH8J4' \
    --header 'Authorization: Bearer YOUR_API_KEY'
  ```

  ```python Python theme={null}
  import requests
  import time

  def query_character_task(task_id):
      """Query character creation task status"""
      url = f"https://toapis.com/v1/characters_tasks/{task_id}"
      headers = {"Authorization": "Bearer YOUR_API_KEY"}
      
      response = requests.get(url, headers=headers)
      return response.json()

  def wait_for_character(task_id, max_wait=300, interval=5):
      """Poll and wait for character creation to complete"""
      start_time = time.time()
      
      while time.time() - start_time < max_wait:
          result = query_character_task(task_id)

          status = result['status']
          progress = result.get('progress', 0)
          print(f"Status: {status}, Progress: {progress}%")
          
          if status == 'completed':
              characters = result['result']['data']['characters']
              for char in characters:
                  print(f"Character ID: {char['id']}")
                  print(f"Display Name: {char['display_name']}")
                  print(f"Username: {char['username']}")
              return result
          
          if status == 'failed':
              raise Exception("Character creation failed")
          
          time.sleep(interval)
      
      raise Exception("Timeout waiting for character creation")

  # Usage example
  task_id = "task_01KBZ0TZZ7C7M3WS1TJM0PH8J4"
  result = wait_for_character(task_id)
  ```

  ```javascript JavaScript theme={null}
  async function queryCharacterTask(taskId) {
    const response = await fetch(
      `https://toapis.com/v1/characters_tasks/${taskId}`,
      {
        method: 'GET',
        headers: {
          'Authorization': 'Bearer YOUR_API_KEY'
        }
      }
    );
    return await response.json();
  }

  async function waitForCharacter(taskId, maxWait = 300000, interval = 5000) {
    const startTime = Date.now();
    
    while (Date.now() - startTime < maxWait) {
      const result = await queryCharacterTask(taskId);

      const { status, progress } = result;
      console.log(`Status: ${status}, Progress: ${progress}%`);
      
      if (status === 'completed') {
        const characters = result.result.data.characters;
        characters.forEach(char => {
          console.log(`Character ID: ${char.id}`);
          console.log(`Display Name: ${char.display_name}`);
          console.log(`Username: ${char.username}`);
        });
        return result;
      }
      
      if (status === 'failed') {
        throw new Error('Character creation failed');
      }
      
      await new Promise(resolve => setTimeout(resolve, interval));
    }
    
    throw new Error('Timeout waiting for character creation');
  }

  // Usage example
  const taskId = 'task_01KBZ0TZZ7C7M3WS1TJM0PH8J4';
  waitForCharacter(taskId).then(result => console.log(result));
  ```

  ```go Go theme={null}
  package main

  import (
      "encoding/json"
      "fmt"
      "io"
      "net/http"
      "time"
  )

  type CharacterResponse struct {
      ID        string `json:"id"`
      Object    string `json:"object"`
      Model     string `json:"model"`
      Status    string `json:"status"`
      Progress  int    `json:"progress"`
      CreatedAt int64  `json:"created_at"`
      Result    struct {
          Type string `json:"type"`
          Data struct {
              Characters []struct {
                  ID                string `json:"id"`
                  DisplayName       string `json:"display_name"`
                  ProfilePictureURL string `json:"profile_picture_url"`
                  Username          string `json:"username"`
              } `json:"characters"`
          } `json:"data"`
      } `json:"result"`
  }

  func queryCharacterTask(taskID string) (*CharacterResponse, error) {
      url := "https://toapis.com/v1/characters_tasks/" + taskID
      
      req, _ := http.NewRequest("GET", url, nil)
      req.Header.Set("Authorization", "Bearer YOUR_API_KEY")
      
      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          return nil, err
      }
      defer resp.Body.Close()
      
      body, _ := io.ReadAll(resp.Body)
      
      var result CharacterResponse
      json.Unmarshal(body, &result)
      return &result, nil
  }

  func main() {
      taskID := "task_01KBZ0TZZ7C7M3WS1TJM0PH8J4"
      
      for i := 0; i < 60; i++ {
          result, err := queryCharacterTask(taskID)
          if err != nil {
              panic(err)
          }
          
          fmt.Printf("Status: %s, Progress: %d%%\n", result.Status, result.Progress)
          
          if result.Status == "completed" {
              for _, char := range result.Result.Data.Characters {
                  fmt.Printf("Character ID: %s\n", char.ID)
                  fmt.Printf("Display Name: %s\n", char.DisplayName)
                  fmt.Printf("Username: %s\n", char.Username)
              }
              break
          }
          
          time.Sleep(5 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 (In Progress) theme={null}
  {
    "id": "task_01KC0JZCMTMQ70D68XTM56Q5D0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "in_progress",
    "progress": 45,
    "created_at": 1765251461,
    "metadata": {}
  }
  ```

  ```json 200 (Completed) theme={null}
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
      "message": "Invalid request parameters",
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

  ```json 404 (Task Not Found) theme={null}
  {
    "error": {
      "code": 404,
      "message": "Task not found",
      "type": "not_found_error"
    }
  }
  ```

  ```json 429 (Rate Limited) theme={null}
  {
    "error": {
      "code": 429,
      "message": "Too many requests, please try again later",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Using Character in Video Generation

After character creation completes, you can use the character in video generation. There are two ways to reference:

### Method 1: Use @username in prompt

```bash theme={null}
curl --request POST \
  --url https://toapis.com/v1/videos/generations \
  --header 'Authorization: Bearer YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "model": "sora-2",
    "prompt": "@duksvfkf.turbo_whis is dancing in the rain"
  }'
```

### Method 2: Use character\_url parameter

```bash theme={null}
curl --request POST \
  --url https://toapis.com/v1/videos/generations \
  --header 'Authorization: Bearer YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "model": "sora-2",
    "prompt": "A cute cat is dancing in the rain",
    "character_url": "task_01KC0JZCMTMQ70D68XTM56Q5D0"
  }'
```

<Tip>
  **Tip:** `username` is obtained from the `characters[].username` field in query results, `character_url` uses the character creation task's `task_id`.
</Tip>

## Polling Recommendations

| Parameter        | Recommended Value                |
| ---------------- | -------------------------------- |
| Polling Interval | 5 seconds                        |
| Max Wait Time    | 5 minutes                        |
| Timeout Handling | Resubmit task or contact support |
