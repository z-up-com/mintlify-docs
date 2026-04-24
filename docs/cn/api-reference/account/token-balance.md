# 查询令牌余额
Source: https://docs.toapis.com/docs/cn/api-reference/account/token-balance

GET https://toapis.com/v1/balance
查询当前 API Key 的剩余额度和已使用额度

* 查询当前 API Key 的剩余额度和已使用额度
* 监控单个令牌的使用情况
* 支持 CORS 跨域请求
* 实时余额监控

获取当前 API Key（令牌）的剩余余额和已使用余额。此接口用于监控单个令牌的使用情况。

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
GET /v1/balance
GET /balance
```

两个端点功能相同，可以任选其一使用。

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/balance' \
    --header 'Authorization: Bearer <token>'
  ```

  ```python Python theme={null}
  import requests

  API_BASE = 'https://toapis.com'
  API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx'

  headers = {
      'Authorization': f'Bearer {API_KEY}'
  }

  def get_token_balance():
      response = requests.get(f'{API_BASE}/v1/balance', headers=headers)
      data = response.json()
      
      if data.get('success'):
          if data.get('unlimited_quota'):
              print("令牌额度: 无限")
          else:
              print(f"令牌剩余余额: {data['remain_balance']}")
          print(f"令牌已使用: {data['used_balance']}")
      else:
          print(f"查询失败: {data.get('message')}")
      
      return data

  get_token_balance()
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function getTokenBalance() {
    const response = await fetch(`${API_BASE}/v1/balance`, {
      headers: {
        'Authorization': `Bearer ${API_KEY}`
      }
    });
    
    const data = await response.json();
    
    if (data.success) {
      if (data.unlimited_quota) {
        console.log('令牌额度: 无限');
      } else {
        console.log(`令牌剩余余额: ${data.remain_balance}`);
      }
      console.log(`令牌已使用: ${data.used_balance}`);
    } else {
      console.error('查询失败:', data.message);
    }
    
    return data;
  }

  getTokenBalance();
  ```

  ```go Go theme={null}
  package main

  import (
      "encoding/json"
      "fmt"
      "io/ioutil"
      "net/http"
  )

  type BalanceResponse struct {
      Success        bool    `json:"success"`
      Message        string  `json:"message,omitempty"`
      RemainBalance  float64 `json:"remain_balance"`
      UsedBalance    float64 `json:"used_balance"`
      UnlimitedQuota bool    `json:"unlimited_quota"`
  }

  func main() {
      url := "https://toapis.com/v1/balance"

      req, _ := http.NewRequest("GET", url, nil)
      req.Header.Set("Authorization", "Bearer <token>")

      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          panic(err)
      }
      defer resp.Body.Close()

      body, _ := ioutil.ReadAll(resp.Body)
      
      var result BalanceResponse
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
          String url = "https://toapis.com/v1/balance";

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

  $ch = curl_init('https://toapis.com/v1/balance');
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

  url = URI("https://toapis.com/v1/balance")

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
  let url = URL(string: "https://toapis.com/v1/balance")!

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
    "remain_balance": 10.5,
    "used_balance": 2.3,
    "unlimited_quota": false
  }
  ```

  ```json 200 - 无限额度令牌 theme={null}
  {
    "success": true,
    "remain_balance": -1,
    "used_balance": 2.3,
    "unlimited_quota": true
  }
  ```

  ```json 200 - 令牌不存在 theme={null}
  {
    "success": false,
    "message": "获取令牌信息失败: record not found"
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
  令牌剩余余额（成功时返回）。当 `unlimited_quota` 为 `true` 时，该值为 `-1`
</ResponseField>

<ResponseField name="used_balance" type="float">
  令牌已使用余额（成功时返回）
</ResponseField>

<ResponseField name="unlimited_quota" type="boolean">
  是否为无限额度令牌。`true` 表示无限额度，`false` 表示有限额度
</ResponseField>

## 使用场景

* 监控单个 API Key 的消耗情况
* 在应用中显示当前令牌的余额
* 设置余额告警，当余额低于阈值时通知

<Note>
  **余额单位说明**

  余额数值的单位取决于系统配置：

  * **USD** - 美元
  * **CNY** - 人民币
  * **Tokens** - Token 数量
</Note>

<Tip>
  **无限额度令牌**

  当令牌被设置为无限额度时：

  * `unlimited_quota` 字段返回 `true`
  * `remain_balance` 字段返回 `-1`
  * 该令牌不受额度限制，可无限使用
</Tip>

## 常见错误

| 错误信息              | 原因                    | 解决方案                                    |
| ----------------- | --------------------- | --------------------------------------- |
| 无 Authorization 头 | 未提供 Authorization 请求头 | 添加 `Authorization: Bearer sk-xxxxx` 请求头 |
| 获取令牌信息失败          | 令牌不存在或已删除             | 检查令牌 key 是否正确                           |

<Warning>
  **安全提示**

  API Key 相当于密码，请妥善保管，不要泄露给他人。生产环境请务必使用 HTTPS。
</Warning>
