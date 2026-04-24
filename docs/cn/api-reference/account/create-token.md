---
title: "创建 API 令牌"
---
Source: https://docs.toapis.com/docs/cn/api-reference/account/create-token

POST https://toapis.com/v1/tokens
通过 API 创建新的 API 令牌

* 通过 API 创建新的 API 令牌
* 支持设置令牌名称和过期时间
* 支持配置额度限制和模型限制
* 新令牌密钥仅在创建时返回一次

<Warning>
  **安全提示**

  请妥善保管返回的令牌密钥（`key` 字段），它仅在创建时显示一次，之后无法再次获取。
</Warning>

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

## Body

<ParamField type="string">
  令牌名称，最大 50 个字符

  示例：`"我的新令牌"`
</ParamField>

<ParamField type="integer">
  令牌过期时间（Unix 时间戳，秒级）

  * 设置为 `-1` 表示永不过期
  * 设置为具体时间戳表示在该时间点后过期

  示例：`1738483200`（2025-02-02 00:00:00 UTC）
</ParamField>

<ParamField type="integer">
  令牌剩余额度（内部单位）

  换算关系：`500000` = \$1 USD

  示例：`500000`（相当于 \$1）
</ParamField>

<ParamField type="boolean">
  是否为无限额度

  * `true`：无限额度，不受额度限制
  * `false`：有限额度，使用 `remain_quota` 的值

  示例：`false`
</ParamField>

<ParamField type="boolean">
  是否启用模型限制

  * `true`：启用模型限制，仅允许使用 `model_limits` 中指定的模型
  * `false`：不限制可用模型

  示例：`false`
</ParamField>

<ParamField type="string">
  允许使用的模型列表（逗号分隔）

  仅当 `model_limits_enabled` 为 `true` 时有效

  示例：`"gpt-4o,gpt-4o-mini,claude-3-5-sonnet"`
</ParamField>

<ParamField type="string">
  令牌分组名称

  用于指定令牌使用的渠道分组

  示例：`"default"`
</ParamField>

## Response

<ResponseField name="success" type="boolean">
  请求是否成功
</ResponseField>

<ResponseField name="message" type="string">
  错误信息（仅失败时返回）
</ResponseField>

<ResponseField name="data" type="object">
  创建的令牌信息（成功时返回）

  <Expandable title="data 字段详情">
    <ResponseField name="id" type="integer">
      令牌 ID
    </ResponseField>

    <ResponseField name="key" type="string">
      令牌密钥（仅在创建时返回一次，请妥善保管）

      格式为 48 位随机字符串，使用时需加上 `sk-` 前缀
    </ResponseField>

    <ResponseField name="name" type="string">
      令牌名称
    </ResponseField>

    <ResponseField name="status" type="integer">
      令牌状态

      * `1`：启用
      * `2`：禁用
      * `3`：已过期
      * `4`：额度耗尽
    </ResponseField>

    <ResponseField name="user_id" type="integer">
      所属用户 ID
    </ResponseField>

    <ResponseField name="created_time" type="integer">
      创建时间（Unix 时间戳）
    </ResponseField>

    <ResponseField name="expired_time" type="integer">
      过期时间（Unix 时间戳），-1 表示永不过期
    </ResponseField>

    <ResponseField name="remain_quota" type="integer">
      剩余额度
    </ResponseField>

    <ResponseField name="unlimited_quota" type="boolean">
      是否为无限额度
    </ResponseField>

    <ResponseField name="used_quota" type="integer">
      已使用额度
    </ResponseField>

    <ResponseField name="model_limits_enabled" type="boolean">
      是否启用模型限制
    </ResponseField>

    <ResponseField name="model_limits" type="string">
      模型限制列表
    </ResponseField>

    <ResponseField name="group" type="string">
      分组名称
    </ResponseField>
  </Expandable>
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url 'https://toapis.com/v1/tokens' \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "name": "我的新令牌",
      "expired_time": -1,
      "remain_quota": 500000,
      "unlimited_quota": false
    }'
  ```

  ```python Python theme={null}
  import requests

  API_BASE = 'https://toapis.com'
  API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx'

  headers = {
      'Authorization': f'Bearer {API_KEY}',
      'Content-Type': 'application/json'
  }

  def create_token():
      payload = {
          'name': '我的新令牌',
          'expired_time': -1,  # 永不过期
          'remain_quota': 500000,
          'unlimited_quota': False
      }
      
      response = requests.post(
          f'{API_BASE}/v1/tokens',
          headers=headers,
          json=payload
      )
      data = response.json()
      
      if data.get('success'):
          token_data = data['data']
          print(f"令牌创建成功！")
          print(f"令牌名称: {token_data['name']}")
          print(f"令牌密钥: sk-{token_data['key']}")
          print("请妥善保管此密钥，它仅显示一次！")
      else:
          print(f"创建失败: {data.get('message')}")
      
      return data

  create_token()
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function createToken() {
    const payload = {
      name: '我的新令牌',
      expired_time: -1, // 永不过期
      remain_quota: 500000,
      unlimited_quota: false
    };

    const response = await fetch(`${API_BASE}/v1/tokens`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${API_KEY}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(payload)
    });
    
    const data = await response.json();
    
    if (data.success) {
      const tokenData = data.data;
      console.log('令牌创建成功！');
      console.log(`令牌名称: ${tokenData.name}`);
      console.log(`令牌密钥: sk-${tokenData.key}`);
      console.log('请妥善保管此密钥，它仅显示一次！');
    } else {
      console.error('创建失败:', data.message);
    }
    
    return data;
  }

  createToken();
  ```

  ```go Go theme={null}
  package main

  import (
      "bytes"
      "encoding/json"
      "fmt"
      "io/ioutil"
      "net/http"
  )

  type CreateTokenRequest struct {
      Name           string `json:"name"`
      ExpiredTime    int64  `json:"expired_time"`
      RemainQuota    int    `json:"remain_quota"`
      UnlimitedQuota bool   `json:"unlimited_quota"`
  }

  type TokenResponse struct {
      Success bool   `json:"success"`
      Message string `json:"message,omitempty"`
      Data    struct {
          Id             int    `json:"id"`
          Key            string `json:"key"`
          Name           string `json:"name"`
          Status         int    `json:"status"`
          CreatedTime    int64  `json:"created_time"`
          ExpiredTime    int64  `json:"expired_time"`
          RemainQuota    int    `json:"remain_quota"`
          UnlimitedQuota bool   `json:"unlimited_quota"`
      } `json:"data"`
  }

  func main() {
      url := "https://toapis.com/v1/tokens"
      apiKey := "sk-xxxxxxxxxxxxxxxxxxxxxx"

      payload := CreateTokenRequest{
          Name:           "我的新令牌",
          ExpiredTime:    -1,
          RemainQuota:    500000,
          UnlimitedQuota: false,
      }

      jsonData, _ := json.Marshal(payload)

      req, _ := http.NewRequest("POST", url, bytes.NewBuffer(jsonData))
      req.Header.Set("Authorization", "Bearer "+apiKey)
      req.Header.Set("Content-Type", "application/json")

      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          panic(err)
      }
      defer resp.Body.Close()

      body, _ := ioutil.ReadAll(resp.Body)

      var result TokenResponse
      json.Unmarshal(body, &result)

      if result.Success {
          fmt.Println("令牌创建成功！")
          fmt.Printf("令牌名称: %s\n", result.Data.Name)
          fmt.Printf("令牌密钥: sk-%s\n", result.Data.Key)
          fmt.Println("请妥善保管此密钥，它仅显示一次！")
      } else {
          fmt.Printf("创建失败: %s\n", result.Message)
      }
  }
  ```

  ```php PHP theme={null}
  <?php

  $api_key = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  $payload = [
      'name' => '我的新令牌',
      'expired_time' => -1,
      'remain_quota' => 500000,
      'unlimited_quota' => false
  ];

  $ch = curl_init('https://toapis.com/v1/tokens');
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
  curl_setopt($ch, CURLOPT_POST, true);
  curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($payload));
  curl_setopt($ch, CURLOPT_HTTPHEADER, [
      "Authorization: Bearer $api_key",
      "Content-Type: application/json"
  ]);

  $response = curl_exec($ch);
  curl_close($ch);

  $data = json_decode($response, true);

  if ($data['success']) {
      echo "令牌创建成功！\n";
      echo "令牌名称: " . $data['data']['name'] . "\n";
      echo "令牌密钥: sk-" . $data['data']['key'] . "\n";
      echo "请妥善保管此密钥，它仅显示一次！\n";
  } else {
      echo "创建失败: " . $data['message'] . "\n";
  }
  ?>
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - 创建成功 theme={null}
  {
    "success": true,
    "message": "",
    "data": {
      "id": 123,
      "user_id": 456,
      "key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "status": 1,
      "name": "我的新令牌",
      "created_time": 1738483200,
      "accessed_time": 1738483200,
      "expired_time": -1,
      "remain_quota": 500000,
      "unlimited_quota": false,
      "used_quota": 0,
      "model_limits_enabled": false,
      "model_limits": "",
      "group": "",
      "cross_group_retry": false
    }
  }
  ```

  ```json 200 - 名称过长 theme={null}
  {
    "success": false,
    "message": "令牌名称过长"
  }
  ```

  ```json 401 - 认证失败 theme={null}
  {
    "error": {
      "code": 401,
      "message": "身份验证失败，请检查您的API密钥",
      "type": "authentication_error"
    }
  }
  ```

  ```json 429 - 请求过于频繁 theme={null}
  {
    "error": {
      "code": 429,
      "message": "请求过于频繁，请稍后再试",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## 使用场景

* 自动化创建多个 API 令牌
* 为不同应用或服务创建独立的令牌
* 通过脚本批量管理令牌生命周期

<Note>
  **令牌密钥格式**

  创建成功后返回的 `key` 字段是 48 位随机字符串。使用时需要加上 `sk-` 前缀，完整格式为 `sk-xxxxxxxx...`。
</Note>

<Tip>
  **最佳实践**

  * 为不同用途创建不同的令牌，便于追踪和管理
  * 设置合理的过期时间，定期轮换令牌
  * 如果只需要访问特定模型，启用模型限制可以提高安全性
</Tip>

## 常见错误

| 错误信息   | 原因             | 解决方案            |
| ------ | -------------- | --------------- |
| 令牌名称过长 | 名称超过 50 个字符    | 使用较短的令牌名称       |
| 生成令牌失败 | 系统内部错误         | 稍后重试或联系支持       |
| 身份验证失败 | API Key 无效或已过期 | 检查 API Key 是否正确 |

<Warning>
  **安全提示**

  * 创建的令牌密钥仅在响应中返回一次，请立即保存
  * 不要在客户端代码中硬编码 API Key
  * 生产环境请务必使用 HTTPS
  * 建议为每个应用创建独立的令牌，便于管理和撤销
</Warning>
