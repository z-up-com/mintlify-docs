> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Sora2 Запрос статуса персонажа

> Запрос статуса задачи создания персонажа и результатов

## Обзор

Запрос статуса и результата задач создания персонажей. Получение подробной информации о созданных персонажах (ID персонажа, отображаемое имя, имя пользователя и т.д.) для использования в последующей генерации видео.

<Note>
  **Примечания:**

  * Создание персонажа — асинхронная задача, требуется периодический опрос этого API
  * После завершения задачи возвращаются детали персонажа
  * `username` персонажа можно использовать в промптах в формате `@username`
  * ID задачи персонажа можно использовать через параметр `character_url` в генерации видео
</Note>

## Параметры пути

<ParamField path="task_id" type="string" required>
  ID задачи создания персонажа

  Это ID задачи, возвращённый из API [Создание персонажа](/ru/api-reference/videos/sora2/create-persona)

  Пример: `"task_01KBZ0TZZ7C7M3WS1TJM0PH8J4"`
</ParamField>

## Аутентификация

<ParamField header="Authorization" type="string" required>
  Bearer Token аутентификация

  ```
  Authorization: Bearer YOUR_API_KEY
  ```

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)
</ParamField>

## Поля ответа

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи (ID задачи создания персонажа)
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемая модель
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи:

  * `queued` — в очереди
  * `in_progress` — выполняется
  * `completed` — успешно завершено
  * `failed` — ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Прогресс выполнения (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Время завершения задачи (Unix timestamp, только после завершения)
</ResponseField>

<ResponseField name="expires_at" type="integer">
  Время истечения результата (Unix timestamp, только после завершения)
</ResponseField>

<ResponseField name="result" type="object">
  Результат задачи (только когда status = completed)

  `result.type` = `character`, а `result.data.characters` содержит список персонажей
</ResponseField>

<ResponseField name="error" type="object">
  Ошибка (только когда status = failed)
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request GET \
    --url 'https://toapis.com/v1/characters_tasks/task_01KBZ0TZZ7C7M3WS1TJM0PH8J4' \
    --header 'Authorization: Bearer YOUR_API_KEY'
  ```

  ```python Python theme={null}
  import requests
  import time

  def query_character_task(task_id):
      """Запрос статуса задачи создания персонажа"""
      url = f"https://toapis.com/v1/characters_tasks/{task_id}"
      headers = {"Authorization": "Bearer YOUR_API_KEY"}
      
      response = requests.get(url, headers=headers)
      return response.json()

  def wait_for_character(task_id, max_wait=300, interval=5):
      """Ожидание завершения создания персонажа"""
      start_time = time.time()
      
      while time.time() - start_time < max_wait:
          result = query_character_task(task_id)

          status = result['status']
          progress = result.get('progress', 0)
          print(f"Статус: {status}, Прогресс: {progress}%")
          
          if status == 'completed':
              characters = result['result']['data']['characters']
              for char in characters:
                  print(f"ID персонажа: {char['id']}")
                  print(f"Отображаемое имя: {char['display_name']}")
                  print(f"Имя пользователя: {char['username']}")
              return result
          
          if status == 'failed':
              raise Exception("Ошибка создания персонажа")
          
          time.sleep(interval)
      
      raise Exception("Таймаут ожидания")

  # Пример использования
  task_id = "task_01KBZ0TZZ7C7M3WS1TJM0PH8J4"
  result = wait_for_character(task_id)
  ```

  ```javascript JavaScript theme={null}
  async function queryCharacterTask(taskId) {
    const response = await fetch(
      `https://toapis.com/v1/characters_tasks/${taskId}`,
      {
        method: 'GET',
        headers: {
          'Authorization': 'Bearer YOUR_API_KEY'
        }
      }
    );
    return await response.json();
  }

  async function waitForCharacter(taskId, maxWait = 300000, interval = 5000) {
    const startTime = Date.now();
    
    while (Date.now() - startTime < maxWait) {
      const result = await queryCharacterTask(taskId);

      const { status, progress } = result;
      console.log(`Статус: ${status}, Прогресс: ${progress}%`);
      
      if (status === 'completed') {
        const characters = result.result.data.characters;
        characters.forEach(char => {
          console.log(`ID персонажа: ${char.id}`);
          console.log(`Отображаемое имя: ${char.display_name}`);
          console.log(`Имя пользователя: ${char.username}`);
        });
        return result;
      }
      
      if (status === 'failed') {
        throw new Error('Ошибка создания персонажа');
      }
      
      await new Promise(resolve => setTimeout(resolve, interval));
    }
    
    throw new Error('Таймаут ожидания');
  }

  // Пример использования
  const taskId = 'task_01KBZ0TZZ7C7M3WS1TJM0PH8J4';
  waitForCharacter(taskId).then(result => console.log(result));
  ```

  ```go Go theme={null}
  package main

  import (
      "encoding/json"
      "fmt"
      "io"
      "net/http"
      "time"
  )

  type CharacterResponse struct {
      ID        string `json:"id"`
      Object    string `json:"object"`
      Model     string `json:"model"`
      Status    string `json:"status"`
      Progress  int    `json:"progress"`
      CreatedAt int64  `json:"created_at"`
      Result    struct {
          Type string `json:"type"`
          Data struct {
              Characters []struct {
                  ID                string `json:"id"`
                  DisplayName       string `json:"display_name"`
                  ProfilePictureURL string `json:"profile_picture_url"`
                  Username          string `json:"username"`
              } `json:"characters"`
          } `json:"data"`
      } `json:"result"`
  }

  func queryCharacterTask(taskID string) (*CharacterResponse, error) {
      url := "https://toapis.com/v1/characters_tasks/" + taskID
      
      req, _ := http.NewRequest("GET", url, nil)
      req.Header.Set("Authorization", "Bearer YOUR_API_KEY")
      
      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          return nil, err
      }
      defer resp.Body.Close()
      
      body, _ := io.ReadAll(resp.Body)
      
      var result CharacterResponse
      json.Unmarshal(body, &result)
      return &result, nil
  }

  func main() {
      taskID := "task_01KBZ0TZZ7C7M3WS1TJM0PH8J4"
      
      for i := 0; i < 60; i++ {
          result, err := queryCharacterTask(taskID)
          if err != nil {
              panic(err)
          }
          
          fmt.Printf("Статус: %s, Прогресс: %d%%\n", result.Status, result.Progress)
          
          if result.Status == "completed" {
              for _, char := range result.Result.Data.Characters {
                  fmt.Printf("ID персонажа: %s\n", char.ID)
                  fmt.Printf("Отображаемое имя: %s\n", char.DisplayName)
                  fmt.Printf("Имя пользователя: %s\n", char.Username)
              }
              break
          }
          
          time.Sleep(5 * time.Second)
      }
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 (В процессе) theme={null}
  {
    "id": "task_01KC0JZCMTMQ70D68XTM56Q5D0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "in_progress",
    "progress": 45,
    "created_at": 1765251461,
    "metadata": {}
  }
  ```

  ```json 200 (Завершено) theme={null}
  {
    "id": "task_01KC0JZCMTMQ70D68XTM56Q5D0",
    "object": "generation.task",
    "model": "sora-2",
    "status": "completed",
    "progress": 100,
    "created_at": 1765251461,
    "completed_at": 1765251507,
    "result": {
      "type": "character",
      "data": {
        "characters": [
          {
            "id": "ch_6937998961208191a45ef08447a554df",
            "display_name": "Turbo Whiskers",
            "profile_picture_url": "https://upload.toapis.com/f/image/character_task_xxx.jpg",
            "username": "duksvfkf.turbo_whis"
          }
        ]
      }
    },
    "metadata": {}
  }
  ```

  ```json 400 (Неверный запрос) theme={null}
  {
    "error": {
      "code": 400,
      "message": "Неверные параметры запроса",
      "type": "invalid_request_error"
    }
  }
  ```

  ```json 401 (Ошибка аутентификации) theme={null}
  {
    "error": {
      "code": 401,
      "message": "Ошибка аутентификации, проверьте ваш API ключ",
      "type": "authentication_error"
    }
  }
  ```

  ```json 404 (Задача не найдена) theme={null}
  {
    "error": {
      "code": 404,
      "message": "Задача не найдена",
      "type": "not_found_error"
    }
  }
  ```

  ```json 429 (Ограничение частоты) theme={null}
  {
    "error": {
      "code": 429,
      "message": "Слишком много запросов, попробуйте позже",
      "type": "rate_limit_error"
    }
  }
  ```
</ResponseExample>

## Использование персонажа в генерации видео

После завершения создания персонажа вы можете использовать его в генерации видео. Есть два способа ссылки:

### Способ 1: Использование @username в промпте

```bash theme={null}
curl --request POST \
  --url https://toapis.com/v1/videos/generations \
  --header 'Authorization: Bearer YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "model": "sora-2",
    "prompt": "@duksvfkf.turbo_whis is dancing in the rain"
  }'
```

### Способ 2: Использование параметра character\_url

```bash theme={null}
curl --request POST \
  --url https://toapis.com/v1/videos/generations \
  --header 'Authorization: Bearer YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "model": "sora-2",
    "prompt": "A cute cat is dancing in the rain",
    "character_url": "task_01KC0JZCMTMQ70D68XTM56Q5D0"
  }'
```

<Tip>
  **Совет:** `username` получается из поля `characters[].username` в результатах запроса, `character_url` использует `task_id` задачи создания персонажа.
</Tip>

## Рекомендации по опросу

| Параметр                    | Рекомендуемое значение                              |
| --------------------------- | --------------------------------------------------- |
| Интервал опроса             | 5 секунд                                            |
| Максимальное время ожидания | 5 минут                                             |
| Обработка таймаута          | Повторная отправка задачи или обращение в поддержку |
