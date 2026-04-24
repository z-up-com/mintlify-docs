> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Get Video Task Status

> Query video generation task status and results

* Query async video generation task execution status and results
* Real-time status updates and progress tracking
* Get generated videos when task completes
* Multi-language support (zh/en/ko/ja)

All video generation tasks are executed asynchronously. After submitting a task, you need to query the status and results via the query endpoint.

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
  Task ID returned by the video generation API
</ParamField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/videos/generations/task_01K9S419324DREZFBWNSVXYR6H' \
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

  def get_video_status(task_id):
      response = requests.get(f'{API_BASE}/v1/videos/generations/{task_id}', headers=headers)
      return response.json()

  def wait_for_video(task_id, max_attempts=60, interval=10):
      for _ in range(max_attempts):
          result = get_video_status(task_id)
          status = result.get('status')
          progress = result.get('progress', 0)
          
          print(f"Status: {status}, Progress: {progress}%")
          
          if status == 'completed':
              return result
          elif status == 'failed':
              raise Exception(f"Task failed: {result}")
          
          time.sleep(interval)
      
      raise Exception("Task timeout")

  # Usage example
  task_id = "task_01K9S419324DREZFBWNSVXYR6H"
  result = wait_for_video(task_id)
  print(f"Video URL: {result['url']}")
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function getVideoStatus(taskId) {
    const response = await fetch(`${API_BASE}/v1/videos/generations/${taskId}`, {
      headers: {
        'Authorization': `Bearer ${API_KEY}`
      }
    });
    return response.json();
  }

  async function waitForVideo(taskId, maxAttempts = 60, interval = 10000) {
    for (let i = 0; i < maxAttempts; i++) {
      const result = await getVideoStatus(taskId);
      const status = result.status;
      const progress = result.progress || 0;
      
      console.log(`Status: ${status}, Progress: ${progress}%`);
      
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
  const taskId = 'task_01K9S419324DREZFBWNSVXYR6H';
  waitForVideo(taskId).then(result => {
    console.log('Video URL:', result.url);
    console.log('Thumbnail:', result.thumbnail);
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

  func getVideoStatus(taskId string) (map[string]interface{}, error) {
      url := fmt.Sprintf("https://toapis.com/v1/videos/generations/%s", taskId)

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
      taskId := "task_01K9S419324DREZFBWNSVXYR6H"
      
      for i := 0; i < 60; i++ {
          result, _ := getVideoStatus(taskId)
          status := result["status"].(string)
          progress := int(result["progress"].(float64))
          
          fmt.Printf("Status: %s, Progress: %d%%\n", status, progress)
          
          if status == "completed" {
              fmt.Println("Video generation completed!")
              fmt.Println("Video URL:", result["url"])
              break
          }
          
          time.Sleep(10 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - Queued theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380222
  }
  ```

  ```json 200 - In Progress theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "in_progress",
    "progress": 65,
    "created_at": 1768380222
  }
  ```

  ```json 200 - Completed theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "completed",
    "progress": 100,
    "created_at": 1768380222,
    "completed_at": 1768380514,
    "expires_at": 1768466914,
    "result": {
      "type": "video",
      "data": [
        {
          "url": "https://files.dashlyai.cc/sora/7712af45-ca35-4a15-b800-f20ea623665b.mp4",
          "format": "mp4"
        }
      ]
    }
  }
  ```

  ```json 200 - Failed theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "failed",
    "progress": 0,
    "created_at": 1768380222,
    "error": {
      "code": "generation_failed",
      "message": "Generation failed: Content violates the content policy"
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

<ResponseField name="progress" type="integer">
  Task progress percentage (0-100)
</ResponseField>

<ResponseField name="model" type="string">
  Video generation model used
</ResponseField>

<ResponseField name="created_at" type="integer">
  Task creation time (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Task completion time (Unix timestamp, only returned when completed)
</ResponseField>

<ResponseField name="expires_at" type="integer">
  Video URL expiration time (Unix timestamp, only returned on completion)
</ResponseField>

<ResponseField name="result" type="object">
  Task result (only returned on success)

  <Expandable title="Properties">
    <ResponseField name="type" type="string">
      Result type, always `video`
    </ResponseField>

    <ResponseField name="data" type="array">
      Video data array

      <Expandable title="Array Elements">
        <ResponseField name="url" type="string">
          Generated video URL
        </ResponseField>

        <ResponseField name="format" type="string">
          Video format (e.g., `mp4`)
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

| Status        | Description                      | Is Final | Recommended Action                   |
| ------------- | -------------------------------- | -------- | ------------------------------------ |
| `submitted`   | Task submitted, waiting in queue | ❌        | Wait 5-10 seconds, then query again  |
| `in_progress` | Task is processing               | ❌        | Wait 10-15 seconds, then query again |
| `completed`   | Task completed successfully      | ✅        | Get video from url field             |
| `failed`      | Task processing failed           | ✅        | Check error info                     |

## Polling Strategy

```
Initial wait: 5 seconds
Polling interval: 10 seconds
Max wait: 600 seconds (10 minutes)
Typical time: 1-5 minutes
```

### Python Polling Example

```python theme={null}
import time
import requests

def poll_video_task(task_id, api_key, max_wait=600):
    """Poll video generation task until completion or timeout"""
    start_time = time.time()
    interval = 10  # 10 second interval
    
    # Initial 5 second wait
    time.sleep(5)
    
    while time.time() - start_time < max_wait:
        response = requests.get(
            f'https://toapis.com/v1/videos/generations/{task_id}',
            headers={'Authorization': f'Bearer {api_key}'}
        )
        data = response.json()
        
        print(f"Status: {data['status']}, Progress: {data.get('progress', 0)}%")
        
        if data['status'] == 'completed':
            return {
                'url': data['url'],
                'thumbnail': data.get('thumbnail'),
                'duration': data.get('duration')
            }
        elif data['status'] == 'failed':
            raise Exception(f"Generation failed: {data['error']['message']}")
        
        time.sleep(interval)
    
    raise TimeoutError("Task timeout")
```

## Resource Expiration

<Warning>
  Generated video URLs are valid for **24 hours**

  * Please download and save videos within the validity period
  * `expires_at` field indicates video expiration time (Unix timestamp)
  * Expired videos cannot be accessed; to regenerate, submit a new task
  * Thumbnails expire simultaneously with videos
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

## Performance Tips

<Note>
  Video generation takes longer, recommendations:

  1. **Use Webhook Callbacks**: If supported, configure callback URL to avoid frequent polling
  2. **Set Reasonable Polling Interval**: Recommended 10 seconds, too frequent wastes request quota
  3. **Set Timeout**: Long videos may take 5-10 minutes, set reasonable timeout
  4. **Download Promptly**: Videos expire after 24 hours, save to your storage immediately
</Note>
