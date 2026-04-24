> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Получить статус задачи видео

> Запрос статуса и результатов задачи генерации видео

* Запрос статуса выполнения и результатов асинхронной задачи генерации видео
* Обновление статуса и отслеживание прогресса в реальном времени
* Получение сгенерированных видео при завершении задачи
* Поддержка многоязычности (zh/en/ko/ja)

Все задачи генерации видео выполняются асинхронно. После отправки задачи необходимо запросить статус и результаты через API запроса.

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
  ID задачи, возвращенный API генерации видео
</ParamField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/videos/generations/task_01K9S419324DREZFBWNSVXYR6H' \
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

  def get_video_status(task_id):
      response = requests.get(f'{API_BASE}/v1/videos/generations/{task_id}', headers=headers)
      return response.json()

  def wait_for_video(task_id, max_attempts=60, interval=10):
      for _ in range(max_attempts):
          result = get_video_status(task_id)
          status = result.get('status')
          progress = result.get('progress', 0)
          
          print(f"Статус: {status}, Прогресс: {progress}%")
          
          if status == 'completed':
              return result
          elif status == 'failed':
              raise Exception(f"Задача не выполнена: {result}")
          
          time.sleep(interval)
      
      raise Exception("Таймаут задачи")

  # Пример использования
  task_id = "task_01K9S419324DREZFBWNSVXYR6H"
  result = wait_for_video(task_id)
  print(f"URL видео: {result['url']}")
  ```

  ```javascript JavaScript theme={null}
  const API_BASE = 'https://toapis.com';
  const API_KEY = 'sk-xxxxxxxxxxxxxxxxxxxxxx';

  async function getVideoStatus(taskId) {
    const response = await fetch(`${API_BASE}/v1/videos/generations/${taskId}`, {
      headers: {
        'Authorization': `Bearer ${API_KEY}`
      }
    });
    return response.json();
  }

  async function waitForVideo(taskId, maxAttempts = 60, interval = 10000) {
    for (let i = 0; i < maxAttempts; i++) {
      const result = await getVideoStatus(taskId);
      const status = result.status;
      const progress = result.progress || 0;
      
      console.log(`Статус: ${status}, Прогресс: ${progress}%`);
      
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
  const taskId = 'task_01K9S419324DREZFBWNSVXYR6H';
  waitForVideo(taskId).then(result => {
    console.log('URL видео:', result.url);
    console.log('Превью:', result.thumbnail);
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

  func getVideoStatus(taskId string) (map[string]interface{}, error) {
      url := fmt.Sprintf("https://toapis.com/v1/videos/generations/%s", taskId)

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
      taskId := "task_01K9S419324DREZFBWNSVXYR6H"
      
      for i := 0; i < 60; i++ {
          result, _ := getVideoStatus(taskId)
          status := result["status"].(string)
          progress := int(result["progress"].(float64))
          
          fmt.Printf("Статус: %s, Прогресс: %d%%\n", status, progress)
          
          if status == "completed" {
              fmt.Println("Генерация видео завершена!")
              fmt.Println("URL видео:", result["url"])
              break
          }
          
          time.Sleep(10 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 - В очереди theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380222
  }
  ```

  ```json 200 - Обрабатывается theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "in_progress",
    "progress": 65,
    "created_at": 1768380222
  }
  ```

  ```json 200 - Завершено theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "completed",
    "progress": 100,
    "created_at": 1768380222,
    "completed_at": 1768380514,
    "expires_at": 1768466914,
    "result": {
      "type": "video",
      "data": [
        {
          "url": "https://files.dashlyai.cc/sora/7712af45-ca35-4a15-b800-f20ea623665b.mp4",
          "format": "mp4"
        }
      ]
    }
  }
  ```

  ```json 200 - Ошибка theme={null}
  {
    "id": "video_7497f4d5-3a88-44c7-923a-967fa7d941a0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "failed",
    "progress": 0,
    "created_at": 1768380222,
    "error": {
      "code": "generation_failed",
      "message": "Генерация не удалась: Контент нарушает политику контента"
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

<ResponseField name="progress" type="integer">
  Прогресс задачи в процентах (0-100)
</ResponseField>

<ResponseField name="model" type="string">
  Используемая модель генерации видео
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Время завершения задачи (Unix timestamp, возвращается только при завершении)
</ResponseField>

<ResponseField name="estimated_time" type="integer">
  Ожидаемое время завершения (секунды)
</ResponseField>

<ResponseField name="url" type="string">
  URL сгенерированного видео (возвращается только при успехе)
</ResponseField>

<ResponseField name="thumbnail" type="string">
  URL превью видео (поддерживается некоторыми моделями)
</ResponseField>

<ResponseField name="duration" type="integer">
  Длительность видео (секунды)
</ResponseField>

<ResponseField name="size" type="string">
  Разрешение видео (например, `1920x1080`)
</ResponseField>

<ResponseField name="expires_at" type="integer">
  Время истечения URL видео (Unix timestamp)
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

| Статус        | Описание                             | Финальный | Рекомендуемое действие                    |
| ------------- | ------------------------------------ | --------- | ----------------------------------------- |
| `submitted`   | Задача отправлена, ожидает в очереди | ❌         | Подождите 5-10 секунд и повторите запрос  |
| `in_progress` | Задача обрабатывается                | ❌         | Подождите 10-15 секунд и повторите запрос |
| `completed`   | Задача успешно завершена             | ✅         | Получите видео из поля url                |
| `failed`      | Обработка задачи не удалась          | ✅         | Проверьте информацию об ошибке            |

## Стратегия опроса

```
Начальное ожидание: 5 секунд
Интервал опроса: 10 секунд
Максимальное ожидание: 600 секунд (10 минут)
Типичное время: 1-5 минут
```

### Пример опроса на Python

```python theme={null}
import time
import requests

def poll_video_task(task_id, api_key, max_wait=600):
    """Опрос задачи генерации видео до завершения или таймаута"""
    start_time = time.time()
    interval = 10  # Интервал 10 секунд
    
    # Начальное ожидание 5 секунд
    time.sleep(5)
    
    while time.time() - start_time < max_wait:
        response = requests.get(
            f'https://toapis.com/v1/videos/generations/{task_id}',
            headers={'Authorization': f'Bearer {api_key}'}
        )
        data = response.json()
        
        print(f"Статус: {data['status']}, Прогресс: {data.get('progress', 0)}%")
        
        if data['status'] == 'completed':
            return {
                'url': data['url'],
                'thumbnail': data.get('thumbnail'),
                'duration': data.get('duration')
            }
        elif data['status'] == 'failed':
            raise Exception(f"Генерация не удалась: {data['error']['message']}")
        
        time.sleep(interval)
    
    raise TimeoutError("Таймаут задачи")
```

## Срок действия ресурсов

<Warning>
  URL сгенерированных видео действительны **24 часа**

  * Загрузите и сохраните видео в течение срока действия
  * Поле `expires_at` указывает время истечения видео (Unix timestamp)
  * После истечения срока видео недоступны; для повторного получения отправьте новую задачу
  * Превью истекает одновременно с видео
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

## Советы по производительности

<Note>
  Генерация видео занимает больше времени, рекомендации:

  1. **Используйте Webhook-обратные вызовы**: Если поддерживается, настройте URL обратного вызова, чтобы избежать частых опросов
  2. **Установите разумный интервал опроса**: Рекомендуется 10 секунд, слишком частые запросы тратят квоту
  3. **Установите таймаут**: Длинные видео могут занимать 5-10 минут, установите разумный таймаут
  4. **Загружайте своевременно**: Видео истекают через 24 часа, обязательно сохраните в своё хранилище немедленно
</Note>
