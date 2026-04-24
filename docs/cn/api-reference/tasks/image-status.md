---
title: "获取图片任务状态"
---
Source: https://docs.toapis.com/docs/cn/api-reference/tasks/image-status

GET https://toapis.com/v1/images/generations/{task_id}
查询图片生成任务的状态和结果

* 查询异步图片生成任务的执行状态和结果
* 实时状态更新和进度跟踪
* 任务完成时获取生成的图片
* 支持多语言返回（zh/en/ko/ja）

所有图片生成任务都是异步执行的。提交任务后，您需要通过查询接口获取任务状态和结果。

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
  图片生成 API 返回的任务 ID
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
          
          print(f"状态: {status}")
          
          if status == 'completed':
              return result
          elif status == 'failed':
              raise Exception(f"任务失败: {result}")
          
          time.sleep(interval)
      
      raise Exception("任务超时")

  # 使用示例
  task_id = "task_01KA040M0HP1GJWBJYZMKX1XS1"
  result = wait_for_image(task_id)
  print(f"图片URL: {result['url']}")
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
      
      console.log(`状态: ${status}`);
      
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
  const taskId = 'task_01KA040M0HP1GJWBJYZMKX1XS1';
  waitForImage(taskId).then(result => {
    console.log('图片URL:', result.url);
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
          
          fmt.Printf("状态: %s\n", status)
          
          if status == "completed" {
              fmt.Println("图片生成完成!")
              fmt.Println("图片URL:", result["url"])
              break
          }
          
          time.Sleep(3 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - 处理中 theme={null}
  {
    "id": "img_5b8b19afe5c24ab3a92df996f1a33931",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview",
    "status": "in_progress",
    "progress": 50,
    "created_at": 1768381010
  }
  ```

  ```json 200 - 已完成 theme={null}
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

  ```json 200 - 失败 theme={null}
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
  使用的图片生成模型
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
  图片 URL 过期时间（Unix 时间戳，仅完成时返回）
</ResponseField>

<ResponseField name="result" type="object">
  任务结果（仅成功时返回）

  <Expandable title="属性">
    <ResponseField name="type" type="string">
      结果类型，固定为 `image`
    </ResponseField>

    <ResponseField name="data" type="array">
      图片数据数组

      <Expandable title="数组元素">
        <ResponseField name="url" type="string">
          生成的图片 URL
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
| `queued`      | 任务排队等待处理 | ❌    | 等待 2-3 秒后重试查询              |
| `in_progress` | 任务正在处理中  | ❌    | 等待 3-5 秒后重试查询              |
| `completed`   | 任务成功完成   | ✅    | 从 result.data\[0].url 获取图片 |
| `failed`      | 任务处理失败   | ✅    | 检查 error 信息                |

## 轮询策略建议

```
初始等待: 2 秒
轮询间隔: 3 秒
最大等待: 120 秒
典型耗时: 5-30 秒
```

### Python 轮询示例

```python theme={null}
import time
import requests

def poll_image_task(task_id, api_key, max_wait=120):
    """轮询图片生成任务直到完成或超时"""
    start_time = time.time()
    interval = 3  # 3秒间隔
    
    while time.time() - start_time < max_wait:
        response = requests.get(
            f'https://toapis.com/v1/images/generations/{task_id}',
            headers={'Authorization': f'Bearer {api_key}'}
        )
        data = response.json()
        
        if data['status'] == 'completed':
            return data['url']
        elif data['status'] == 'failed':
            raise Exception(f"生成失败: {data['error']['message']}")
        
        time.sleep(interval)
    
    raise TimeoutError("任务超时")
```

## 图片资源有效期

<Warning>
  生成的图片 URL 有效期为 **24 小时**

  * 请在有效期内下载保存图片
  * `expires_at` 字段标识图片过期时间（Unix 时间戳）
  * 图片过期后无法访问，如需重新获取，需要重新提交生成任务
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
