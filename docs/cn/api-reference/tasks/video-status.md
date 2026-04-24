# 获取视频任务状态
Source: https://docs.toapis.com/docs/cn/api-reference/tasks/video-status

GET https://toapis.com/v1/videos/generations/{task_id}
查询视频生成任务的状态和结果

* 查询异步视频生成任务的执行状态和结果
* 实时状态更新和进度跟踪
* 任务完成时获取生成的视频
* 支持多语言返回（zh/en/ko/ja）

所有视频生成任务都是异步执行的。提交任务后，您需要通过查询接口获取任务状态和结果。

## Authorizations

<ParamField type="string">
  所有接口均需要使用 Bearer Token 进行认证

  获取 API Key：

  访问 [API Key 管理页面](https://toapis.com/console/token) 获取您的 API Key

  使用时在请求头中添加：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Path Parameters

<ParamField type="string">
  视频生成 API 返回的任务 ID
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
          
          print(f"状态: {status}, 进度: {progress}%")
          
          if status == 'completed':
              return result
          elif status == 'failed':
              raise Exception(f"任务失败: {result}")
          
          time.sleep(interval)
      
      raise Exception("任务超时")

  # 使用示例
  task_id = "video_7497f4d5-3a88-44c7-923a-967fa7d941a0"
  result = wait_for_video(task_id)
  print(f"视频URL: {result['result']['data'][0]['url']}")
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
      
      console.log(`状态: ${status}, 进度: ${progress}%`);
      
      if (status === 'completed') {
        return result;
      } else if (status === 'failed') {
        throw new Error(`任务失败: ${JSON.stringify(result)}`);
      }
      
      await new Promise(r => setTimeout(r, interval));
    }
    
    throw new Error('任务超时');
  }

  // 使用示例
  const taskId = 'video_7497f4d5-3a88-44c7-923a-967fa7d941a0';
  waitForVideo(taskId).then(result => {
    console.log('视频URL:', result.result.data[0].url);
    console.log('格式:', result.result.data[0].format);
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
      taskId := "video_7497f4d5-3a88-44c7-923a-967fa7d941a0"
      
      for i := 0; i < 60; i++ {
          result, _ := getVideoStatus(taskId)
          status := result["status"].(string)
          progress := int(result["progress"].(float64))
          
          fmt.Printf("状态: %s, 进度: %d%%\n", status, progress)
          
          if status == "completed" {
              fmt.Println("视频生成完成!")
              resultData := result["result"].(map[string]interface{})
              dataArray := resultData["data"].([]interface{})
              videoData := dataArray[0].(map[string]interface{})
              fmt.Println("视频URL:", videoData["url"])
              break
          }
          
          time.Sleep(10 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - 排队中 theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380222
  }
  ```

  ```json 200 - 处理中 theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "in_progress",
    "progress": 65,
    "created_at": 1768380222
  }
  ```

  ```json 200 - 已完成 theme={null}
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

  ```json 200 - 失败 theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "failed",
    "progress": 0,
    "created_at": 1768380222,
    "error": {
      "code": "generation_failed",
      "message": "生成失败: 内容违反了内容政策"
    }
  }
  ```

  ```json 404 theme={null}
  {
    "error": {
      "code": 404,
      "message": "任务不存在",
      "type": "not_found_error"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": 401,
      "message": "身份验证失败，请检查您的API密钥",
      "type": "authentication_error"
    }
  }
  ```
</ResponseExample>

## Response

<ResponseField name="id" type="string">
  任务唯一标识符
</ResponseField>

<ResponseField name="object" type="string">
  对象类型，固定为 `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  使用的视频生成模型
</ResponseField>

<ResponseField name="status" type="string">
  任务状态

  * `queued` - 排队等待处理
  * `in_progress` - 处理中
  * `completed` - 成功完成
  * `failed` - 失败
</ResponseField>

<ResponseField name="progress" type="integer">
  任务进度百分比（0-100）
</ResponseField>

<ResponseField name="created_at" type="integer">
  任务创建时间（Unix 时间戳）
</ResponseField>

<ResponseField name="completed_at" type="integer">
  任务完成时间（Unix 时间戳，仅完成时返回）
</ResponseField>

<ResponseField name="expires_at" type="integer">
  视频 URL 过期时间（Unix 时间戳，仅完成时返回）
</ResponseField>

<ResponseField name="result" type="object">
  任务结果（仅成功时返回）

  <Expandable title="属性">
    <ResponseField name="type" type="string">
      结果类型，固定为 `video`
    </ResponseField>

    <ResponseField name="data" type="array">
      视频数据数组

      <Expandable title="数组元素">
        <ResponseField name="url" type="string">
          生成的视频 URL
        </ResponseField>

        <ResponseField name="format" type="string">
          视频格式（如 `mp4`）
        </ResponseField>
      </Expandable>
    </ResponseField>
  </Expandable>
</ResponseField>

<ResponseField name="error" type="object">
  错误信息（仅失败时返回）

  <Expandable title="属性">
    <ResponseField name="code" type="string">
      错误代码
    </ResponseField>

    <ResponseField name="message" type="string">
      错误描述
    </ResponseField>
  </Expandable>
</ResponseField>

## 任务状态说明

| 状态            | 说明       | 是否终态 | 建议操作                       |
| ------------- | -------- | ---- | -------------------------- |
| `queued`      | 任务排队等待处理 | ❌    | 等待 5-10 秒后重试查询             |
| `in_progress` | 任务正在处理中  | ❌    | 等待 10-15 秒后重试查询            |
| `completed`   | 任务成功完成   | ✅    | 从 result.data\[0].url 获取视频 |
| `failed`      | 任务处理失败   | ✅    | 检查 error 信息                |

## 轮询策略建议

```
初始等待: 5 秒
轮询间隔: 10 秒
最大等待: 600 秒（10分钟）
典型耗时: 1-5 分钟
```

### Python 轮询示例

```python theme={null}
import time
import requests

def poll_video_task(task_id, api_key, max_wait=600):
    """轮询视频生成任务直到完成或超时"""
    start_time = time.time()
    interval = 10  # 10秒间隔
    
    # 首次等待5秒
    time.sleep(5)
    
    while time.time() - start_time < max_wait:
        response = requests.get(
            f'https://toapis.com/v1/videos/generations/{task_id}',
            headers={'Authorization': f'Bearer {api_key}'}
        )
        data = response.json()
        
        print(f"状态: {data['status']}, 进度: {data.get('progress', 0)}%")
        
        if data['status'] == 'completed':
            return {
                'url': data['result']['data'][0]['url'],
                'format': data['result']['data'][0].get('format'),
                'expires_at': data.get('expires_at')
            }
        elif data['status'] == 'failed':
            raise Exception(f"生成失败: {data['error']['message']}")
        
        time.sleep(interval)
    
    raise TimeoutError("任务超时")
```

## 视频资源有效期

<Warning>
  生成的视频 URL 有效期为 **24 小时**

  * 请在有效期内下载保存视频
  * `expires_at` 字段标识视频过期时间（Unix 时间戳）
  * 视频过期后无法访问，如需重新获取，需要重新提交生成任务
  * 缩略图与视频同步过期
</Warning>

## 常见错误

| 错误码 | 错误类型                       | 说明              |
| --- | -------------------------- | --------------- |
| 400 | `invalid_request`          | 请求参数无效          |
| 401 | `unauthorized`             | 认证失败，检查 API Key |
| 402 | `insufficient_quota`       | 余额不足            |
| 404 | `task_not_found`           | 任务不存在           |
| 422 | `content_policy_violation` | 内容违规            |
| 429 | `rate_limit_exceeded`      | 请求频率超限          |
| 500 | `internal_error`           | 服务器内部错误         |

## 性能建议

<Note>
  视频生成耗时较长，建议：

  1. **使用 Webhook 回调**：如果平台支持，配置回调URL可以避免频繁轮询
  2. **合理设置轮询间隔**：建议10秒，过于频繁会浪费请求配额
  3. **设置超时时间**：长视频生成可能需要5-10分钟，请设置合理的超时
  4. **及时下载保存**：视频24小时后过期，请务必及时保存到自己的存储
</Note>
