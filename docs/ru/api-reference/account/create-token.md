# Создать API токен

> Создание нового API токена через API

* Создание нового API токена через API
* Настройка имени токена и времени истечения
* Настройка лимитов квоты и ограничений моделей
* Ключ токена возвращается только один раз при создании

<Warning>
  **Уведомление о безопасности**

  Пожалуйста, надежно сохраните возвращенный ключ токена (поле `key`). Он отображается только один раз при создании и не может быть получен позже.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все конечные точки требуют аутентификации Bearer Token

  Получите ваш API Key:

  Посетите [страницу управления API Key](https://toapis.com/console/token) чтобы получить ваш API Key

  Добавьте его в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="name" type="string" required>
  Имя токена, максимум 50 символов

  Пример: `"Мой новый токен"`
</ParamField>

<ParamField body="expired_time" type="integer" default="-1">
  Время истечения токена (Unix timestamp в секундах)

  * Установите `-1` для бессрочного токена
  * Установите конкретный timestamp для истечения в это время

  Пример: `1738483200` (2025-02-02 00:00:00 UTC)
</ParamField>

<ParamField body="remain_quota" type="integer" default="0">
  Оставшаяся квота токена (внутренние единицы)

  Конвертация: `500000` = \$1 USD

  Пример: `500000` (эквивалент \$1)
</ParamField>

<ParamField body="unlimited_quota" type="boolean" default="false">
  Имеет ли токен неограниченную квоту

  * `true`: Неограниченная квота, без ограничений
  * `false`: Ограниченная квота, используется значение `remain_quota`

  Пример: `false`
</ParamField>

<ParamField body="model_limits_enabled" type="boolean" default="false">
  Включить ли ограничения моделей

  * `true`: Включить ограничения, доступны только модели из `model_limits`
  * `false`: Без ограничений моделей

  Пример: `false`
</ParamField>

<ParamField body="model_limits" type="string" default="">
  Список разрешенных моделей (через запятую)

  Действует только когда `model_limits_enabled` равно `true`

  Пример: `"gpt-4o,gpt-4o-mini,claude-3-5-sonnet"`
</ParamField>

<ParamField body="group" type="string" default="">
  Имя группы токена

  Используется для указания группы каналов для этого токена

  Пример: `"default"`
</ParamField>

## Ответ

<ResponseField name="success" type="boolean">
  Успешен ли запрос
</ResponseField>

<ResponseField name="message" type="string">
  Сообщение об ошибке (возвращается только при неудаче)
</ResponseField>

<ResponseField name="data" type="object">
  Информация о созданном токене (возвращается при успехе)

  <Expandable title="Детали поля data">
    <ResponseField name="id" type="integer">
      ID токена
    </ResponseField>

    <ResponseField name="key" type="string">
      Ключ токена (возвращается только один раз при создании, сохраните надежно)

      Формат - случайная строка из 48 символов, используйте с префиксом `sk-`
    </ResponseField>

    <ResponseField name="name" type="string">
      Имя токена
    </ResponseField>

    <ResponseField name="status" type="integer">
      Статус токена

      * `1`: Включен
      * `2`: Отключен
      * `3`: Истек
      * `4`: Квота исчерпана
    </ResponseField>

    <ResponseField name="user_id" type="integer">
      ID владельца
    </ResponseField>

    <ResponseField name="created_time" type="integer">
      Время создания (Unix timestamp)
    </ResponseField>

    <ResponseField name="expired_time" type="integer">
      Время истечения (Unix timestamp), -1 означает бессрочный
    </ResponseField>

    <ResponseField name="remain_quota" type="integer">
      Оставшаяся квота
    </ResponseField>

    <ResponseField name="unlimited_quota" type="boolean">
      Неограниченная ли квота
    </ResponseField>

    <ResponseField name="used_quota" type="integer">
      Использованная квота
    </ResponseField>

    <ResponseField name="model_limits_enabled" type="boolean">
      Включены ли ограничения моделей
    </ResponseField>

    <ResponseField name="model_limits" type="string">
      Список ограничений моделей
    </ResponseField>

    <ResponseField name="group" type="string">
      Имя группы
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
      "name": "Мой новый токен",
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
          'name': 'Мой новый токен',
          'expired_time': -1,  # Бессрочный
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
          print(f"Токен успешно создан!")
          print(f"Имя токена: {token_data['name']}")
          print(f"Ключ токена: sk-{token_data['key']}")
          print("Сохраните этот ключ надежно, он показывается только один раз!")
      else:
          print(f"Ошибка создания: {data.get('message')}")
      
      return data

  create_token()
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function createToken() {
    const payload = {
      name: 'Мой новый токен',
      expired_time: -1, // Бессрочный
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
      console.log('Токен успешно создан!');
      console.log(`Имя токена: ${tokenData.name}`);
      console.log(`Ключ токена: sk-${tokenData.key}`);
      console.log('Сохраните этот ключ надежно, он показывается только один раз!');
    } else {
      console.error('Ошибка создания:', data.message);
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
          Name:           "Мой новый токен",
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
          fmt.Println("Токен успешно создан!")
          fmt.Printf("Имя токена: %s\n", result.Data.Name)
          fmt.Printf("Ключ токена: sk-%s\n", result.Data.Key)
          fmt.Println("Сохраните этот ключ надежно, он показывается только один раз!")
      } else {
          fmt.Printf("Ошибка создания: %s\n", result.Message)
      }
  }
  ```

  ```php PHP theme={null}
  <?php

  $api_key = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  $payload = [
      'name' => 'Мой новый токен',
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
      echo "Токен успешно создан!\n";
      echo "Имя токена: " . $data['data']['name'] . "\n";
      echo "Ключ токена: sk-" . $data['data']['key'] . "\n";
      echo "Сохраните этот ключ надежно, он показывается только один раз!\n";
  } else {
      echo "Ошибка создания: " . $data['message'] . "\n";
  }
  ?>
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - Успех theme={null}
  {
    "success": true,
    "message": "",
    "data": {
      "id": 123,
      "user_id": 456,
      "key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "status": 1,
      "name": "Мой новый токен",
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

  ```json 200 - Имя слишком длинное theme={null}
  {
    "success": false,
    "message": "令牌名称过长"
  }
  ```

  ```json 401 - Ошибка аутентификации theme={null}
  {
    "error": {
      "code": 401,
      "message": "Ошибка аутентификации, проверьте ваш API ключ",
      "type": "authentication_error"
    }
  }
  ```

  ```json 429 - Превышен лимит запросов theme={null}
  {
    "error": {
      "code": 429,
      "message": "Слишком много запросов, попробуйте позже",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Сценарии использования

* Автоматизация создания нескольких API токенов
* Создание отдельных токенов для разных приложений или сервисов
* Пакетное управление жизненным циклом токенов через скрипты

<Note>
  **Формат ключа токена**

  Поле `key`, возвращаемое при успехе - случайная строка из 48 символов. При использовании добавьте префикс `sk-` для полного формата: `sk-xxxxxxxx...`
</Note>

<Tip>
  **Лучшие практики**

  * Создавайте разные токены для разных целей для удобства отслеживания и управления
  * Устанавливайте разумное время истечения и регулярно ротируйте токены
  * Включайте ограничения моделей, если вам нужен доступ только к определенным моделям для повышения безопасности
</Tip>

## Частые ошибки

| Сообщение об ошибке   | Причина                          | Решение                                    |
| --------------------- | -------------------------------- | ------------------------------------------ |
| 令牌名称过长                | Имя превышает 50 символов        | Используйте более короткое имя токена      |
| 生成令牌失败                | Внутренняя ошибка системы        | Повторите позже или обратитесь в поддержку |
| Ошибка аутентификации | API Key недействителен или истек | Проверьте правильность API Key             |

<Warning>
  **Уведомление о безопасности**

  * Созданный ключ токена возвращается только один раз в ответе, сохраните его немедленно
  * Не храните API Keys в клиентском коде
  * Всегда используйте HTTPS в продакшене
  * Создавайте отдельные токены для каждого приложения для удобства управления и отзыва
</Warning>
