# Получить статус задачи изображения

> Запрос статуса и результатов задачи генерации изображений

* Запрос статуса выполнения и результатов асинхронной задачи генерации изображений
* Обновление статуса и отслеживание прогресса в реальном времени
* Получение сгенерированных изображений при завершении задачи
* Поддержка многоязычности (zh/en/ko/ja)

Все задачи генерации изображений выполняются асинхронно. После отправки задачи необходимо запросить статус и результаты через API запроса.

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

## Параметры пути

<ParamField path="task_id" type="string" required>
  ID задачи, возвращенный API генерации изображений
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
          
          print(f"Статус: {status}")
          
          if status == 'completed':
              return result
          elif status == 'failed':
              raise Exception(f"Задача не выполнена: {result}")
          
          time.sleep(interval)
      
      raise Exception("Таймаут задачи")

  # Пример использования
  task_id = "task_01KA040M0HP1GJWBJYZMKX1XS1"
  result = wait_for_image(task_id)
  print(f"URL изображения: {result['url']}")
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
      
      console.log(`Статус: ${status}`);
      
      if (status === 'completed') {
        return result;
      } else if (status === 'failed') {
        throw new Error(`Задача не выполнена: ${JSON.stringify(result)}`);
      }
      
      await new Promise(r => setTimeout(r, interval));
    }
    
    throw new Error('Таймаут задачи');
  }

  // Пример использования
  const taskId = 'task_01KA040M0HP1GJWBJYZMKX1XS1';
  waitForImage(taskId).then(result => {
    console.log('URL изображения:', result.url);
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
          
          fmt.Printf("Статус: %s\n", status)
          
          if status == "completed" {
              fmt.Println("Генерация изображения завершена!")
              fmt.Println("URL изображения:", result["url"])
              break
          }
          
          time.Sleep(3 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - Обрабатывается theme={null}
  {
    "id": "img_5b8b19afe5c24ab3a92df996f1a33931",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview",
    "status": "in_progress",
    "progress": 50,
    "created_at": 1768381010
  }
  ```

  ```json 200 - Завершено theme={null}
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

  ```json 200 - Ошибка theme={null}
  {
    "id": "img_73c450923a9a43e4aabf426e1c681d64",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview",
    "status": "failed",
    "progress": 0,
    "created_at": 1768215312,
    "error": {
      "code": "generation_failed",
      "message": "вызов upstream API не удался: upstream вернул статус 422"
    }
  }
  ```

  ```json 404 theme={null}
  {
    "error": {
      "code": 404,
      "message": "Задача не найдена",
      "type": "not_found_error"
    }
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
</ResponseExample>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `submitted` - Отправлена, ожидает обработки
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="model" type="string">
  Используемая модель генерации изображений
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Время завершения задачи (Unix timestamp, возвращается только при завершении)
</ResponseField>

<ResponseField name="url" type="string">
  URL сгенерированного изображения (возвращается только при успехе)
</ResponseField>

<ResponseField name="revised_prompt" type="string">
  Оптимизированный промпт (поддерживается некоторыми моделями)
</ResponseField>

<ResponseField name="expires_at" type="integer">
  Время истечения URL изображения (Unix timestamp)
</ResponseField>

<ResponseField name="error" type="object">
  Информация об ошибке (только при неудаче)

  <Expandable title="Свойства">
    <ResponseField name="code" type="string">
      Код ошибки
    </ResponseField>

    <ResponseField name="message" type="string">
      Описание ошибки
    </ResponseField>
  </Expandable>
</ResponseField>

## Справка по статусам задач

| Статус        | Описание                             | Финальный | Рекомендуемое действие                   |
| ------------- | ------------------------------------ | --------- | ---------------------------------------- |
| `submitted`   | Задача отправлена, ожидает обработки | ❌         | Подождите 2-3 секунды и повторите запрос |
| `in_progress` | Задача обрабатывается                | ❌         | Подождите 3-5 секунд и повторите запрос  |
| `completed`   | Задача успешно завершена             | ✅         | Получите изображение из поля url         |
| `failed`      | Обработка задачи не удалась          | ✅         | Проверьте информацию об ошибке           |

## Стратегия опроса

```
Начальное ожидание: 2 секунды
Интервал опроса: 3 секунды
Максимальное ожидание: 120 секунд
Типичное время: 5-30 секунд
```

### Пример опроса на Python

```python theme={null}
import time
import requests

def poll_image_task(task_id, api_key, max_wait=120):
    """Опрос задачи генерации изображения до завершения или таймаута"""
    start_time = time.time()
    interval = 3  # Интервал 3 секунды
    
    while time.time() - start_time < max_wait:
        response = requests.get(
            f'https://toapis.com/v1/images/generations/{task_id}',
            headers={'Authorization': f'Bearer {api_key}'}
        )
        data = response.json()
        
        if data['status'] == 'completed':
            return data['url']
        elif data['status'] == 'failed':
            raise Exception(f"Генерация не удалась: {data['error']['message']}")
        
        time.sleep(interval)
    
    raise TimeoutError("Таймаут задачи")
```

## Срок действия ресурсов

<Warning>
  URL сгенерированных изображений действительны **24 часа**

  * Загрузите и сохраните изображения в течение срока действия
  * Поле `expires_at` указывает время истечения изображения (Unix timestamp)
  * После истечения срока изображения недоступны; для повторного получения отправьте новую задачу
</Warning>

## Коды ошибок

| HTTP код | Тип ошибки                 | Описание                                 |
| -------- | -------------------------- | ---------------------------------------- |
| 400      | `invalid_request`          | Неверные параметры запроса               |
| 401      | `unauthorized`             | Ошибка аутентификации, проверьте API Key |
| 402      | `insufficient_quota`       | Недостаточно средств                     |
| 404      | `task_not_found`           | Задача не найдена                        |
| 422      | `content_policy_violation` | Нарушение политики контента              |
| 429      | `rate_limit_exceeded`      | Превышен лимит запросов                  |
| 500      | `internal_error`           | Внутренняя ошибка сервера                |
