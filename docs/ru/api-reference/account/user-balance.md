# Баланс пользователя

> Запрос остатка и использованной квоты аккаунта пользователя

* Запрос общего баланса аккаунта пользователя
* Мониторинг использования на уровне пользователя
* Поддержка CORS
* Мониторинг баланса в реальном времени

Получение остатка и использованного баланса аккаунта пользователя. Этот эндпоинт возвращает информацию о балансе на уровне пользователя, независимо от конкретных токенов.

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
GET /v1/user/balance
GET /user/balance
```

Оба эндпоинта имеют одинаковую функциональность.

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
              print("Квота пользователя: Безлимитная")
          else:
              print(f"Остаток баланса: {data['remain_balance']}")
          print(f"Использовано: {data['used_balance']}")
      else:
          print(f"Ошибка запроса: {data.get('message')}")
      
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
        console.log('Квота пользователя: Безлимитная');
      } else {
        console.log(`Остаток баланса: ${data.remain_balance}`);
      }
      console.log(`Использовано: ${data.used_balance}`);
    } else {
      console.error('Ошибка запроса:', data.message);
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

  url = URI("https://toapis.com/v1/user/balance")

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
  ```json 200 - Успех theme={null}
  {
    "success": true,
    "remain_balance": 100.0,
    "used_balance": 25.5,
    "unlimited_quota": false
  }
  ```

  ```json 200 - Безлимитный пользователь theme={null}
  {
    "success": true,
    "remain_balance": -1,
    "used_balance": 25.5,
    "unlimited_quota": true
  }
  ```

  ```json 200 - Ошибка запроса квоты theme={null}
  {
    "success": false,
    "message": "Не удалось получить квоту пользователя"
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
  Остаток баланса пользователя (возвращается при успехе). Возвращает `-1` когда `unlimited_quota` равно `true`
</ResponseField>

<ResponseField name="used_balance" type="float">
  Использованный баланс пользователя (возвращается при успехе)
</ResponseField>

<ResponseField name="unlimited_quota" type="boolean">
  Имеет ли пользователь безлимитную квоту. `true` означает безлимитную, `false` означает ограниченную
</ResponseField>

## Баланс токена vs Баланс пользователя

| Сравнение       | Баланс токена (`/v1/balance`)             | Баланс пользователя (`/v1/user/balance`) |
| --------------- | ----------------------------------------- | ---------------------------------------- |
| Область         | Один токен                                | Весь аккаунт пользователя                |
| Источник данных | RemainQuota и UsedQuota токена            | quota и used\_quota пользователя         |
| Применение      | Мониторинг использования одного API ключа | Просмотр общего баланса аккаунта         |
| Ограничен       | Лимитами квоты токена                     | Лимитами квоты пользователя              |

## Применение

* Просмотр общего баланса аккаунта пользователя
* Настройка напоминаний о пополнении и оповещений о балансе
* Отображение баланса аккаунта в панели управления

<Note>
  **Информация о единицах баланса**

  Единица измерения баланса зависит от конфигурации системы:

  * **USD** - Доллары США
  * **CNY** - Китайские юани
  * **Tokens** - Количество токенов
</Note>

<Tip>
  **Безлимитный пользователь**

  Когда пользователь имеет безлимитную квоту:

  * Поле `unlimited_quota` возвращает `true`
  * Поле `remain_balance` возвращает `-1`
  * Пользователь не имеет ограничений квоты
</Tip>

## Частые ошибки

| Сообщение об ошибке                      | Причина                                 | Решение                                                  |
| ---------------------------------------- | --------------------------------------- | -------------------------------------------------------- |
| Нет заголовка Authorization              | Заголовок Authorization не предоставлен | Добавьте заголовок `Authorization: Bearer sk-xxxxx`      |
| Не удалось получить квоту пользователя   | Пользователь не существует              | Проверьте существует ли пользователь связанный с токеном |
| Не удалось получить использованную квоту | Ошибка запроса к базе данных            | Свяжитесь с администратором                              |

<Warning>
  **Примечание по безопасности**

  Ваш API ключ подобен паролю. Храните его в безопасности и не делитесь с другими. Всегда используйте HTTPS в продакшене.
</Warning>
