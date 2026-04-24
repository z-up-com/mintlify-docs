# Get Image Task Status

> Query image generation task status and results

* Query async image generation task execution status and results
* Real-time status updates and progress tracking
* Get generated images when task completes
* Multi-language support (zh/en/ko/ja)

All image generation tasks are executed asynchronously. After submitting a task, you need to query the status and results via the query endpoint.

## Authorizations

<ParamField header="Authorization" type="string" required>
  All endpoints require Bearer Token authentication

  Get your API Key:

  Visit the [API Key Management Page](https://toapis.com/console/token) to get your API Key

  Add it to the request header:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Path Parameters

<ParamField path="task_id" type="string" required>
  Task ID returned by the image generation API
</ParamField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/images/generations/task_01KA040M0HP1GJWBJYZMKX1XS1' \
    --header 'Authorization: Bearer <token>'
  ```

  ```python Python theme={null}
  import requests
  import time

  API_BASE = 'https://toapis.com'
  API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx'

  headers = {
      'Authorization': f'Bearer {API_KEY}'
  }

  def get_image_status(task_id):
      response = requests.get(f'{API_BASE}/v1/images/generations/{task_id}', headers=headers)
      return response.json()

  def wait_for_image(task_id, max_attempts=60, interval=3):
      for _ in range(max_attempts):
          result = get_image_status(task_id)
          status = result.get('status')
          
          print(f"Status: {status}")
          
          if status == 'completed':
              return result
          elif status == 'failed':
              raise Exception(f"Task failed: {result}")
          
          time.sleep(interval)
      
      raise Exception("Task timeout")

  # Usage example
  task_id = "task_01KA040M0HP1GJWBJYZMKX1XS1"
  result = wait_for_image(task_id)
  print(f"Image URL: {result['url']}")
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function getImageStatus(taskId) {
    const response = await fetch(`${API_BASE}/v1/images/generations/${taskId}`, {
      headers: {
        'Authorization': `Bearer ${API_KEY}`
      }
    });
    return response.json();
  }

  async function waitForImage(taskId, maxAttempts = 60, interval = 3000) {
    for (let i = 0; i < maxAttempts; i++) {
      const result = await getImageStatus(taskId);
      const status = result.status;
      
      console.log(`Status: ${status}`);
      
      if (status === 'completed') {
        return result;
      } else if (status === 'failed') {
        throw new Error(`Task failed: ${JSON.stringify(result)}`);
      }
      
      await new Promise(r => setTimeout(r, interval));
    }
    
    throw new Error('Task timeout');
  }

  // Usage example
  const taskId = 'task_01KA040M0HP1GJWBJYZMKX1XS1';
  waitForImage(taskId).then(result => {
    console.log('Image URL:', result.url);
  });
  ```

  ```go Go theme={null}
  package main

  import (
      "encoding/json"
      "fmt"
      "io/ioutil"
      "net/http"
      "time"
  )

  func getImageStatus(taskId string) (map[string]interface{}, error) {
      url := fmt.Sprintf("https://toapis.com/v1/images/generations/%s", taskId)

      req, _ := http.NewRequest("GET", url, nil)
      req.Header.Set("Authorization", "Bearer <token>")

      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          return nil, err
      }
      defer resp.Body.Close()

      body, _ := ioutil.ReadAll(resp.Body)
      
      var result map[string]interface{}
      json.Unmarshal(body, &result)
      
      return result, nil
  }

  func main() {
      taskId := "task_01KA040M0HP1GJWBJYZMKX1XS1"
      
      for i := 0; i < 60; i++ {
          result, _ := getImageStatus(taskId)
          status := result["status"].(string)
          
          fmt.Printf("Status: %s\n", status)
          
          if status == "completed" {
              fmt.Println("Image generation completed!")
              fmt.Println("Image URL:", result["url"])
              break
          }
          
          time.Sleep(3 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - In Progress theme={null}
  {
    "id": "img_5b8b19afe5c24ab3a92df996f1a33931",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview",
    "status": "in_progress",
    "progress": 50,
    "created_at": 1768381010
  }
  ```

  ```json 200 - Completed theme={null}
  {
    "id": "img_5b8b19afe5c24ab3a92df996f1a33931",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview",
    "status": "completed",
    "progress": 100,
    "created_at": 1768381010,
    "completed_at": 1768381063,
    "expires_at": 1768467463,
    "result": {
      "type": "image",
      "data": [
        {
          "url": "https://files.dashlyai.cc/generated/1768381061_c55c1bbb.jpg"
        }
      ]
    }
  }
  ```

  ```json 200 - Failed theme={null}
  {
    "id": "img_73c450923a9a43e4aabf426e1c681d64",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview",
    "status": "failed",
    "progress": 0,
    "created_at": 1768215312,
    "error": {
      "code": "generation_failed",
      "message": "call upstream API failed: upstream returned status 422"
    }
  }
  ```

  ```json 404 theme={null}
  {
    "error": {
      "code": 404,
      "message": "Task not found",
      "type": "not_found_error"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": 401,
      "message": "Authentication failed, please check your API key",
      "type": "authentication_error"
    }
  }
  ```
</ResponseExample>

## Response

<ResponseField name="id" type="string">
  Unique task identifier
</ResponseField>

<ResponseField name="object" type="string">
  Object type, always `generation.task`
</ResponseField>

<ResponseField name="status" type="string">
  Task status

  * `queued` - Queued for processing
  * `in_progress` - Processing
  * `completed` - Successfully completed
  * `failed` - Failed
</ResponseField>

<ResponseField name="model" type="string">
  Image generation model used
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation time (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Task completion time (Unix timestamp, only returned when completed)
</ResponseField>

<ResponseField name="url" type="string">
  Generated image URL (only returned on success)
</ResponseField>

<ResponseField name="expires_at" type="integer">
  Image URL expiration time (Unix timestamp, only returned on completion)
</ResponseField>

<ResponseField name="result" type="object">
  Task result (only returned on success)

  <Expandable title="Properties">
    <ResponseField name="type" type="string">
      Result type, always `image`
    </ResponseField>

    <ResponseField name="data" type="array">
      Image data array

      <Expandable title="Array Elements">
        <ResponseField name="url" type="string">
          Generated image URL
        </ResponseField>
      </Expandable>
    </ResponseField>
  </Expandable>
</ResponseField>

<ResponseField name="error" type="object">
  Error information (only returned on failure)

  <Expandable title="Properties">
    <ResponseField name="code" type="string">
      Error code
    </ResponseField>

    <ResponseField name="message" type="string">
      Error description
    </ResponseField>
  </Expandable>
</ResponseField>

## Task Status Reference

| Status        | Description                            | Is Final | Recommended Action                 |
| ------------- | -------------------------------------- | -------- | ---------------------------------- |
| `submitted`   | Task submitted, waiting for processing | ❌        | Wait 2-3 seconds, then query again |
| `in_progress` | Task is processing                     | ❌        | Wait 3-5 seconds, then query again |
| `completed`   | Task completed successfully            | ✅        | Get image from url field           |
| `failed`      | Task processing failed                 | ✅        | Check error info                   |

## Polling Strategy

```
Initial wait: 2 seconds
Polling interval: 3 seconds
Max wait: 120 seconds
Typical time: 5-30 seconds
```

### Python Polling Example

```python theme={null}
import time
import requests

def poll_image_task(task_id, api_key, max_wait=120):
    """Poll image generation task until completion or timeout"""
    start_time = time.time()
    interval = 3  # 3 second interval
    
    while time.time() - start_time < max_wait:
        response = requests.get(
            f'https://toapis.com/v1/images/generations/{task_id}',
            headers={'Authorization': f'Bearer {api_key}'}
        )
        data = response.json()
        
        if data['status'] == 'completed':
            return data['url']
        elif data['status'] == 'failed':
            raise Exception(f"Generation failed: {data['error']['message']}")
        
        time.sleep(interval)
    
    raise TimeoutError("Task timeout")
```

## Resource Expiration

<Warning>
  Generated image URLs are valid for **24 hours**

  * Please download and save images within the validity period
  * `expires_at` field indicates image expiration time (Unix timestamp)
  * Expired images cannot be accessed; to regenerate, submit a new task
</Warning>

## Error Codes

| HTTP Code | Error Type                 | Description                          |
| --------- | -------------------------- | ------------------------------------ |
| 400       | `invalid_request`          | Invalid request parameters           |
| 401       | `unauthorized`             | Authentication failed, check API Key |
| 402       | `insufficient_quota`       | Insufficient balance                 |
| 404       | `task_not_found`           | Task not found                       |
| 422       | `content_policy_violation` | Content policy violation             |
| 429       | `rate_limit_exceeded`      | Rate limit exceeded                  |
| 500       | `internal_error`           | Internal server error                |
