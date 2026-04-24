> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Query User Balance

> Query the remaining and used quota of the current user account

* Query the overall balance of the current user account
* Monitor user-level usage
* CORS cross-origin support
* Real-time balance monitoring

Get the remaining and used balance of the current user account. This endpoint returns user-level balance information, independent of specific tokens, for viewing the overall account balance.

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

## Endpoints

```
GET /v1/user/balance
GET /user/balance
```

Both endpoints have the same functionality, you can use either one.

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
              print("User quota: Unlimited")
          else:
              print(f"User remaining balance: {data['remain_balance']}")
          print(f"User used: {data['used_balance']}")
      else:
          print(f"Query failed: {data.get('message')}")
      
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
        console.log('User quota: Unlimited');
      } else {
        console.log(`User remaining balance: ${data.remain_balance}`);
      }
      console.log(`User used: ${data.used_balance}`);
    } else {
      console.error('Query failed:', data.message);
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
              fmt.Println("Quota: Unlimited")
          } else {
              fmt.Printf("Remaining balance: %.2f\n", result.RemainBalance)
          }
          fmt.Printf("Used: %.2f\n", result.UsedBalance)
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
          echo "Quota: Unlimited\n";
      } else {
          echo "Remaining balance: " . $data['remain_balance'] . "\n";
      }
      echo "Used: " . $data['used_balance'] . "\n";
  } else {
      echo "Query failed: " . $data['message'] . "\n";
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
      puts "Quota: Unlimited"
    else
      puts "Remaining balance: #{data['remain_balance']}"
    end
    puts "Used: #{data['used_balance']}"
  else
    puts "Query failed: #{data['message']}"
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
  ```json 200 - Success theme={null}
  {
    "success": true,
    "remain_balance": 100.0,
    "used_balance": 25.5,
    "unlimited_quota": false
  }
  ```

  ```json 200 - Unlimited Quota User theme={null}
  {
    "success": true,
    "remain_balance": -1,
    "used_balance": 25.5,
    "unlimited_quota": true
  }
  ```

  ```json 200 - User Quota Query Failed theme={null}
  {
    "success": false,
    "message": "Failed to get user quota"
  }
  ```

  ```json 200 - Used Quota Query Failed theme={null}
  {
    "success": false,
    "message": "Failed to get used quota"
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

  ```json 429 theme={null}
  {
    "error": {
      "code": 429,
      "message": "Too many requests, please try again later",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Response

<ResponseField name="success" type="boolean">
  Whether the request was successful
</ResponseField>

<ResponseField name="message" type="string">
  Error message (only returned on failure)
</ResponseField>

<ResponseField name="remain_balance" type="float">
  User remaining balance (returned on success). Returns `-1` when `unlimited_quota` is `true`
</ResponseField>

<ResponseField name="used_balance" type="float">
  User used balance (returned on success)
</ResponseField>

<ResponseField name="unlimited_quota" type="boolean">
  Whether the user has unlimited quota. `true` means unlimited, `false` means limited
</ResponseField>

## Token Balance vs User Balance

| Comparison  | Token Balance (`/v1/balance`)     | User Balance (`/v1/user/balance`) |
| ----------- | --------------------------------- | --------------------------------- |
| Scope       | Single token                      | Entire user account               |
| Data Source | Token's RemainQuota and UsedQuota | User's quota and used\_quota      |
| Use Case    | Monitor single API Key usage      | View overall account balance      |
| Limited By  | Token-level quota limits          | User-level quota limits           |

## Use Cases

* View overall user account balance
* Set up recharge reminders and balance alerts
* Display account balance in user dashboard

<Note>
  **Balance Unit Information**

  The unit of the balance value depends on system configuration:

  * **USD** - US Dollars
  * **CNY** - Chinese Yuan
  * **Tokens** - Token count
</Note>

<Tip>
  **Unlimited Quota User**

  When a user is set to unlimited quota:

  * `unlimited_quota` field returns `true`
  * `remain_balance` field returns `-1`
  * The user has no quota restrictions and can use the API without limits
</Tip>

## Common Errors

| Error Message            | Cause                             | Solution                                           |
| ------------------------ | --------------------------------- | -------------------------------------------------- |
| No Authorization header  | Authorization header not provided | Add `Authorization: Bearer sk-xxxxx` header        |
| Failed to get user quota | User doesn't exist                | Check if the user associated with the token exists |
| Failed to get used quota | Database query error              | Contact admin to check system status               |

<Warning>
  **Security Note**

  Your API Key is like a password. Keep it secure and don't share it with others. Always use HTTPS in production.
</Warning>
