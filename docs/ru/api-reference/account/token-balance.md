> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Баланс токена

> Запрос остатка и использованной квоты текущего API ключа

* Запрос остатка и использованной квоты текущего API ключа
* Мониторинг использования одного токена
* Поддержка CORS
* Мониторинг баланса в реальном времени

Получение остатка и использованного баланса текущего API ключа (токена). Этот эндпоинт используется для мониторинга использования одного токена.

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все эндпоинты требуют аутентификации Bearer Token

  Получить API ключ:

  Посетите [Страницу управления API ключами](https://toapis.com/console/token) для получения API ключа

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Эндпоинты

```
GET /v1/balance
GET /balance
```

Оба эндпоинта имеют одинаковую функциональность.

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
              print("Квота токена: Безлимитная")
          else:
              print(f"Остаток баланса токена: {data['remain_balance']}")
          print(f"Использовано: {data['used_balance']}")
      else:
          print(f"Ошибка запроса: {data.get('message')}")
      
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
        console.log('Квота токена: Безлимитная');
      } else {
        console.log(`Остаток баланса токена: ${data.remain_balance}`);
      }
      console.log(`Использовано: ${data.used_balance}`);
    } else {
      console.error('Ошибка запроса:', data.message);
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
              fmt.Println("Квота: Безлимитная")
          } else {
              fmt.Printf("Остаток: %.2f\n", result.RemainBalance)
          }
          fmt.Printf("Использовано: %.2f\n", result.UsedBalance)
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
          echo "Квота: Безлимитная\n";
      } else {
          echo "Остаток: " . $data['remain_balance'] . "\n";
      }
      echo "Использовано: " . $data['used_balance'] . "\n";
  } else {
      echo "Ошибка: " . $data['message'] . "\n";
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
      puts "Квота: Безлимитная"
    else
      puts "Остаток: #{data['remain_balance']}"
    end
    puts "Использовано: #{data['used_balance']}"
  else
    puts "Ошибка: #{data['message']}"
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
  ```json 200 - Успех theme={null}
  {
    "success": true,
    "remain_balance": 10.5,
    "used_balance": 2.3,
    "unlimited_quota": false
  }
  ```

  ```json 200 - Безлимитный токен theme={null}
  {
    "success": true,
    "remain_balance": -1,
    "used_balance": 2.3,
    "unlimited_quota": true
  }
  ```

  ```json 200 - Токен не найден theme={null}
  {
    "success": false,
    "message": "Не удалось получить информацию о токене: запись не найдена"
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": 401,
      "message": "Ошибка аутентификации, проверьте ваш API ключ",
      "type": "authentication_error"
    }
  }
  ```

  ```json 429 theme={null}
  {
    "error": {
      "code": 429,
      "message": "Слишком много запросов, попробуйте позже",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Ответ

<ResponseField name="success" type="boolean">
  Успешность запроса
</ResponseField>

<ResponseField name="message" type="string">
  Сообщение об ошибке (только при неудаче)
</ResponseField>

<ResponseField name="remain_balance" type="float">
  Остаток баланса токена (возвращается при успехе). Возвращает `-1` когда `unlimited_quota` равно `true`
</ResponseField>

<ResponseField name="used_balance" type="float">
  Использованный баланс токена (возвращается при успехе)
</ResponseField>

<ResponseField name="unlimited_quota" type="boolean">
  Имеет ли токен безлимитную квоту. `true` означает безлимитную, `false` означает ограниченную
</ResponseField>

## Применение

* Мониторинг потребления одного API ключа
* Отображение текущего баланса токена в приложении
* Настройка оповещений при низком балансе

<Note>
  **Информация о единицах баланса**

  Единица измерения баланса зависит от конфигурации системы:

  * **USD** - Доллары США
  * **CNY** - Китайские юани
  * **Tokens** - Количество токенов
</Note>

<Tip>
  **Безлимитный токен**

  Когда токен имеет безлимитную квоту:

  * Поле `unlimited_quota` возвращает `true`
  * Поле `remain_balance` возвращает `-1`
  * Токен не имеет ограничений квоты
</Tip>

## Частые ошибки

| Сообщение об ошибке                     | Причина                                 | Решение                                             |
| --------------------------------------- | --------------------------------------- | --------------------------------------------------- |
| Нет заголовка Authorization             | Заголовок Authorization не предоставлен | Добавьте заголовок `Authorization: Bearer sk-xxxxx` |
| Не удалось получить информацию о токене | Токен не существует или был удален      | Проверьте правильность ключа токена                 |

<Warning>
  **Примечание по безопасности**

  Ваш API ключ подобен паролю. Храните его в безопасности и не делитесь с другими. Всегда используйте HTTPS в продакшене.
</Warning>
