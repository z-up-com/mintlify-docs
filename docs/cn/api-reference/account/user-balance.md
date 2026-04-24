---
title: "查询用户余额"
---
Source: https://docs.toapis.com/docs/cn/api-reference/account/user-balance

GET https://toapis.com/v1/user/balance
查询当前用户账户的剩余额度和已使用额度

* 查询当前用户账户的总体余额
* 监控用户级别的使用情况
* 支持 CORS 跨域请求
* 实时余额监控

获取当前用户账户的剩余余额和已使用余额。此接口返回用户级别的余额信息，与具体令牌无关，用于查看用户账户的总体余额。

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

## 接口端点

```
GET /v1/user/balance
GET /user/balance
```

两个端点功能相同，可以任选其一使用。

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/user/balance' \
    --header 'Authorization: Bearer <token>'
  ```

  ```python Python theme={null}
  import requests

  API_BASE = 'https://toapis.com'
  API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx'

  headers = {
      'Authorization': f'Bearer {API_KEY}'
  }

  def get_user_balance():
      response = requests.get(f'{API_BASE}/v1/user/balance', headers=headers)
      data = response.json()
      
      if data.get('success'):
          if data.get('unlimited_quota'):
              print("用户额度: 无限")
          else:
              print(f"用户剩余余额: {data['remain_balance']}")
          print(f"用户已使用: {data['used_balance']}")
      else:
          print(f"查询失败: {data.get('message')}")
      
      return data

  get_user_balance()
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function getUserBalance() {
    const response = await fetch(`${API_BASE}/v1/user/balance`, {
      headers: {
        'Authorization': `Bearer ${API_KEY}`
      }
    });
    
    const data = await response.json();
    
    if (data.success) {
      if (data.unlimited_quota) {
        console.log('用户额度: 无限');
      } else {
        console.log(`用户剩余余额: ${data.remain_balance}`);
      }
      console.log(`用户已使用: ${data.used_balance}`);
    } else {
      console.error('查询失败:', data.message);
    }
    
    return data;
  }

  getUserBalance();
  ```

  ```go Go theme={null}
  package main

  import (
      "encoding/json"
      "fmt"
      "io/ioutil"
      "net/http"
  )

  type UserBalanceResponse struct {
      Success        bool    `json:"success"`
      Message        string  `json:"message,omitempty"`
      RemainBalance  float64 `json:"remain_balance"`
      UsedBalance    float64 `json:"used_balance"`
      UnlimitedQuota bool    `json:"unlimited_quota"`
  }

  func main() {
      url := "https://toapis.com/v1/user/balance"

      req, _ := http.NewRequest("GET", url, nil)
      req.Header.Set("Authorization", "Bearer <token>")

      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          panic(err)
      }
      defer resp.Body.Close()

      body, _ := ioutil.ReadAll(resp.Body)
      
      var result UserBalanceResponse
      json.Unmarshal(body, &result)
      
      if result.Success {
          if result.UnlimitedQuota {
              fmt.Println("额度: 无限")
          } else {
              fmt.Printf("剩余余额: %.2f\n", result.RemainBalance)
          }
          fmt.Printf("已使用: %.2f\n", result.UsedBalance)
      }
  }
  ```

  ```java Java theme={null}
  import java.net.http.HttpClient;
  import java.net.http.HttpRequest;
  import java.net.http.HttpResponse;
  import java.net.URI;

  public class Main {
      public static void main(String[] args) throws Exception {
          String url = "https://toapis.com/v1/user/balance";

          HttpClient client = HttpClient.newHttpClient();
          HttpRequest request = HttpRequest.newBuilder()
              .uri(URI.create(url))
              .header("Authorization", "Bearer <token>")
              .GET()
              .build();

          HttpResponse<String> response = client.send(request,
              HttpResponse.BodyHandlers.ofString());

          System.out.println(response.body());
      }
  }
  ```

  ```php PHP theme={null}
  <?php

  $api_key = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  $ch = curl_init('https://toapis.com/v1/user/balance');
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
  curl_setopt($ch, CURLOPT_HTTPHEADER, [
      "Authorization: Bearer $api_key"
  ]);

  $response = curl_exec($ch);
  curl_close($ch);

  $data = json_decode($response, true);

  if ($data['success']) {
      if ($data['unlimited_quota']) {
          echo "额度: 无限\n";
      } else {
          echo "剩余余额: " . $data['remain_balance'] . "\n";
      }
      echo "已使用: " . $data['used_balance'] . "\n";
  } else {
      echo "查询失败: " . $data['message'] . "\n";
  }
  ?>
  ```

  ```ruby Ruby theme={null}
  require 'net/http'
  require 'json'
  require 'uri'

  api_key = 'sk-xxxxxxxxxxxxxxxxxxxxxx'

  url = URI("https://toapis.com/v1/user/balance")

  http = Net::HTTP.new(url.host, url.port)
  http.use_ssl = true

  request = Net::HTTP::Get.new(url)
  request["Authorization"] = "Bearer #{api_key}"

  response = http.request(request)
  data = JSON.parse(response.body)

  if data['success']
    if data['unlimited_quota']
      puts "额度: 无限"
    else
      puts "剩余余额: #{data['remain_balance']}"
    end
    puts "已使用: #{data['used_balance']}"
  else
    puts "查询失败: #{data['message']}"
  end
  ```

  ```swift Swift theme={null}
  import Foundation

  let apiKey = "sk-xxxxxxxxxxxxxxxxxxxxxx"
  let url = URL(string: "https://toapis.com/v1/user/balance")!

  var request = URLRequest(url: url)
  request.httpMethod = "GET"
  request.setValue("Bearer \(apiKey)", forHTTPHeaderField: "Authorization")

  let task = URLSession.shared.dataTask(with: request) { data, response, error in
      if let error = error {
          print("Error: \(error)")
          return
      }
      
      if let data = data, let responseString = String(data: data, encoding: .utf8) {
          print(responseString)
      }
  }

  task.resume()
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - 查询成功 theme={null}
  {
    "success": true,
    "remain_balance": 100.0,
    "used_balance": 25.5,
    "unlimited_quota": false
  }
  ```

  ```json 200 - 无限额度用户 theme={null}
  {
    "success": true,
    "remain_balance": -1,
    "used_balance": 25.5,
    "unlimited_quota": true
  }
  ```

  ```json 200 - 用户额度查询失败 theme={null}
  {
    "success": false,
    "message": "获取用户额度失败"
  }
  ```

  ```json 200 - 已使用额度查询失败 theme={null}
  {
    "success": false,
    "message": "获取已使用额度失败"
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

  ```json 429 theme={null}
  {
    "error": {
      "code": 429,
      "message": "请求过于频繁，请稍后再试",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Response

<ResponseField name="success" type="boolean">
  请求是否成功
</ResponseField>

<ResponseField name="message" type="string">
  错误信息（仅失败时返回）
</ResponseField>

<ResponseField name="remain_balance" type="float">
  用户剩余余额（成功时返回）。当 `unlimited_quota` 为 `true` 时，该值为 `-1`
</ResponseField>

<ResponseField name="used_balance" type="float">
  用户已使用余额（成功时返回）
</ResponseField>

<ResponseField name="unlimited_quota" type="boolean">
  是否为无限额度用户。`true` 表示无限额度，`false` 表示有限额度
</ResponseField>

## 令牌余额 vs 用户余额

| 对比项  | 令牌余额 (`/v1/balance`)            | 用户余额 (`/v1/user/balance`)  |
| ---- | ------------------------------- | -------------------------- |
| 作用范围 | 单个令牌                            | 整个用户账户                     |
| 数据来源 | Token 的 RemainQuota 和 UsedQuota | User 的 quota 和 used\_quota |
| 使用场景 | 监控单个 API Key 的使用情况              | 查看用户账户总体余额                 |
| 受限于  | 令牌级别的额度限制                       | 用户级别的额度限制                  |

## 使用场景

* 查看用户账户的总体余额
* 用于充值提醒和余额告警
* 在用户控制面板显示账户余额

<Note>
  **余额单位说明**

  余额数值的单位取决于系统配置：

  * **USD** - 美元
  * **CNY** - 人民币
  * **Tokens** - Token 数量
</Note>

<Tip>
  **无限额度用户**

  当用户被设置为无限额度时：

  * `unlimited_quota` 字段返回 `true`
  * `remain_balance` 字段返回 `-1`
  * 该用户不受额度限制，可无限使用
</Tip>

## 常见错误

| 错误信息              | 原因                    | 解决方案                                    |
| ----------------- | --------------------- | --------------------------------------- |
| 无 Authorization 头 | 未提供 Authorization 请求头 | 添加 `Authorization: Bearer sk-xxxxx` 请求头 |
| 获取用户额度失败          | 用户不存在                 | 检查令牌关联的用户是否存在                           |
| 获取已使用额度失败         | 数据库查询错误               | 联系管理员检查系统状态                             |

<Warning>
  **安全提示**

  API Key 相当于密码，请妥善保管，不要泄露给他人。生产环境请务必使用 HTTPS。
</Warning>
