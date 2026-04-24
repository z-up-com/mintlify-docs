> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Create API Token

> Create a new API token via API

* Create a new API token via API
* Customize token name and expiration time
* Configure quota limits and model restrictions
* Token key is only returned once upon creation

<Warning>
  **Security Notice**

  Please securely store the returned token key (`key` field). It is only displayed once upon creation and cannot be retrieved later.
</Warning>

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

## Body

<ParamField body="name" type="string" required>
  Token name, maximum 50 characters

  Example: `"My New Token"`
</ParamField>

<ParamField body="expired_time" type="integer" default="-1">
  Token expiration time (Unix timestamp in seconds)

  * Set to `-1` for never expires
  * Set to a specific timestamp to expire at that time

  Example: `1738483200` (2025-02-02 00:00:00 UTC)
</ParamField>

<ParamField body="remain_quota" type="integer" default="0">
  Token remaining quota (internal units)

  Conversion: `500000` = \$1 USD

  Example: `500000` (equivalent to \$1)
</ParamField>

<ParamField body="unlimited_quota" type="boolean" default="false">
  Whether the token has unlimited quota

  * `true`: Unlimited quota, no restrictions
  * `false`: Limited quota, uses `remain_quota` value

  Example: `false`
</ParamField>

<ParamField body="model_limits_enabled" type="boolean" default="false">
  Whether to enable model restrictions

  * `true`: Enable model restrictions, only models in `model_limits` can be used
  * `false`: No model restrictions

  Example: `false`
</ParamField>

<ParamField body="model_limits" type="string" default="">
  List of allowed models (comma-separated)

  Only effective when `model_limits_enabled` is `true`

  Example: `"gpt-4o,gpt-4o-mini,claude-3-5-sonnet"`
</ParamField>

<ParamField body="group" type="string" default="">
  Token group name

  Used to specify the channel group for this token

  Example: `"default"`
</ParamField>

## Response

<ResponseField name="success" type="boolean">
  Whether the request was successful
</ResponseField>

<ResponseField name="message" type="string">
  Error message (only returned on failure)
</ResponseField>

<ResponseField name="data" type="object">
  Created token information (returned on success)

  <Expandable title="data field details">
    <ResponseField name="id" type="integer">
      Token ID
    </ResponseField>

    <ResponseField name="key" type="string">
      Token key (only returned once upon creation, please store securely)

      Format is a 48-character random string, use with `sk-` prefix
    </ResponseField>

    <ResponseField name="name" type="string">
      Token name
    </ResponseField>

    <ResponseField name="status" type="integer">
      Token status

      * `1`: Enabled
      * `2`: Disabled
      * `3`: Expired
      * `4`: Quota exhausted
    </ResponseField>

    <ResponseField name="user_id" type="integer">
      Owner user ID
    </ResponseField>

    <ResponseField name="created_time" type="integer">
      Creation time (Unix timestamp)
    </ResponseField>

    <ResponseField name="expired_time" type="integer">
      Expiration time (Unix timestamp), -1 means never expires
    </ResponseField>

    <ResponseField name="remain_quota" type="integer">
      Remaining quota
    </ResponseField>

    <ResponseField name="unlimited_quota" type="boolean">
      Whether it has unlimited quota
    </ResponseField>

    <ResponseField name="used_quota" type="integer">
      Used quota
    </ResponseField>

    <ResponseField name="model_limits_enabled" type="boolean">
      Whether model limits are enabled
    </ResponseField>

    <ResponseField name="model_limits" type="string">
      Model limits list
    </ResponseField>

    <ResponseField name="group" type="string">
      Group name
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
      "name": "My New Token",
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
          'name': 'My New Token',
          'expired_time': -1,  # Never expires
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
          print(f"Token created successfully!")
          print(f"Token name: {token_data['name']}")
          print(f"Token key: sk-{token_data['key']}")
          print("Please store this key securely, it's only shown once!")
      else:
          print(f"Creation failed: {data.get('message')}")
      
      return data

  create_token()
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function createToken() {
    const payload = {
      name: 'My New Token',
      expired_time: -1, // Never expires
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
      console.log('Token created successfully!');
      console.log(`Token name: ${tokenData.name}`);
      console.log(`Token key: sk-${tokenData.key}`);
      console.log("Please store this key securely, it's only shown once!");
    } else {
      console.error('Creation failed:', data.message);
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
          Name:           "My New Token",
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
          fmt.Println("Token created successfully!")
          fmt.Printf("Token name: %s\n", result.Data.Name)
          fmt.Printf("Token key: sk-%s\n", result.Data.Key)
          fmt.Println("Please store this key securely, it's only shown once!")
      } else {
          fmt.Printf("Creation failed: %s\n", result.Message)
      }
  }
  ```

  ```php PHP theme={null}
  <?php

  $api_key = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  $payload = [
      'name' => 'My New Token',
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
      echo "Token created successfully!\n";
      echo "Token name: " . $data['data']['name'] . "\n";
      echo "Token key: sk-" . $data['data']['key'] . "\n";
      echo "Please store this key securely, it's only shown once!\n";
  } else {
      echo "Creation failed: " . $data['message'] . "\n";
  }
  ?>
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - Success theme={null}
  {
    "success": true,
    "message": "",
    "data": {
      "id": 123,
      "user_id": 456,
      "key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "status": 1,
      "name": "My New Token",
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

  ```json 200 - Name Too Long theme={null}
  {
    "success": false,
    "message": "令牌名称过长"
  }
  ```

  ```json 401 - Authentication Failed theme={null}
  {
    "error": {
      "code": 401,
      "message": "Authentication failed, please check your API key",
      "type": "authentication_error"
    }
  }
  ```

  ```json 429 - Rate Limited theme={null}
  {
    "error": {
      "code": 429,
      "message": "Too many requests, please try again later",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Use Cases

* Automate creation of multiple API tokens
* Create separate tokens for different applications or services
* Batch manage token lifecycle through scripts

<Note>
  **Token Key Format**

  The `key` field returned upon success is a 48-character random string. When using, add the `sk-` prefix for the complete format: `sk-xxxxxxxx...`
</Note>

<Tip>
  **Best Practices**

  * Create different tokens for different purposes for easier tracking and management
  * Set reasonable expiration times and rotate tokens regularly
  * Enable model limits if you only need access to specific models for enhanced security
</Tip>

## Common Errors

| Error Message         | Cause                         | Solution                        |
| --------------------- | ----------------------------- | ------------------------------- |
| 令牌名称过长                | Name exceeds 50 characters    | Use a shorter token name        |
| 生成令牌失败                | Internal system error         | Retry later or contact support  |
| Authentication failed | API Key is invalid or expired | Check if the API Key is correct |

<Warning>
  **Security Notice**

  * The created token key is only returned once in the response, save it immediately
  * Do not hardcode API Keys in client-side code
  * Always use HTTPS in production
  * Create separate tokens for each application for easier management and revocation
</Warning>
