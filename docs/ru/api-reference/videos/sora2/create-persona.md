# Sora2 Создание персонажа

> Извлечение персонажей из видео для последующей генерации

## Обзор

Функция создания персонажей Sora2 позволяет извлекать персонажей из существующих видео. После создания персонаж можно повторно использовать в последующих генерациях видео для сохранения консистентности.

<Note>
  **Важно:**

  * Видео должно содержать **звук** и **узнаваемого персонажа**
  * Ограничение временного диапазона: **минимум 1 секунда, максимум 3 секунды**
  * Требуется один из параметров: `url` или `from_task`
  * Параметр `prompt` **не требуется** в этом режиме
  * После создания ID задачи персонажа можно использовать для генерации видео
</Note>

## Аутентификация

<ParamField header="Authorization" type="string" required>
  Bearer Token аутентификация

  ```
  Authorization: Bearer YOUR_API_KEY
  ```

  Получите API Key на [странице управления ключами](https://toapis.com/console/token)
</ParamField>

## Параметры запроса

<ParamField body="model" type="string" default="sora-2" required>
  Название модели генерации видео

  Поддерживаемые модели:

  * `sora-2` - Стандартная версия
  * `sora-2-pro` - Профессиональная версия (более высокое качество)
  * `sora-2-vip` - VIP версия, более высокий приоритет
</ParamField>

<ParamField body="timestamps" type="string" required>
  Временной диапазон появления персонажа

  Единица измерения — секунды, формат: `"начало,конец"`

  **Ограничения:**

  * Минимальная разница: **1 секунда**
  * Максимальная разница: **3 секунды**

  Пример: `"1,3"` означает персонажа с 1-й по 3-ю секунду видео
</ParamField>

<ParamField body="url" type="string">
  URL видео с персонажем для извлечения

  **Требования:**

  * Видео должно содержать звук
  * Видео должно содержать узнаваемого персонажа

  **Примечание:** Требуется `url` или `from_task`

  Пример: `"https://example.com/my-video.mp4"`
</ParamField>

<ParamField body="from_task" type="string">
  ID ранее созданной задачи генерации видео

  Создание персонажа из существующей задачи

  **Примечание:** Требуется `url` или `from_task`

  Пример: `"task_01KBYT59JDHB4A3KDDR9N9JVWP"`
</ParamField>

## Поля ответа

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса создания персонажа

  После завершения этот ID можно использовать в генерации видео через параметр `character_url`
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Использованная модель
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи:

  * `queued` - В очереди на обработку
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Прогресс выполнения задачи в процентах (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="completed_at" type="integer">
  Время завершения задачи (Unix timestamp, только после завершения)
</ResponseField>

<ResponseField name="result" type="object">
  Результат создания персонажа (только после завершения)

  Содержит информацию о персонаже: ID, имя, изображение профиля и т.д.
</ResponseField>

<RequestExample>
  ```bash cURL (Из URL видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer YOUR_API_KEY' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "url": "https://example.com/character-video.mp4",
      "timestamps": "1,3"
    }'
  ```

  ```bash cURL (Из существующей задачи) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer YOUR_API_KEY' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "from_task": "task_01KBYT59JDHB4A3KDDR9N9JVWP",
      "timestamps": "1,3"
    }'
  ```

  ```python Python theme={null}
  import requests

  # Создание персонажа из URL видео
  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer YOUR_API_KEY",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "url": "https://example.com/character-video.mp4",
          "timestamps": "1,3"
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")

  # Создание персонажа из существующей задачи
  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer YOUR_API_KEY",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "from_task": "task_01KBYT59JDHB4A3KDDR9N9JVWP",
          "timestamps": "1,3"
      }
  )
  ```

  ```javascript JavaScript theme={null}
  // Создание персонажа из URL видео
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer YOUR_API_KEY',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      url: 'https://example.com/character-video.mp4',
      timestamps: '1,3'
    })
  });

  const task = await response.json();
  console.log(`ID задачи: ${task.id}`);
  console.log(`Статус: ${task.status}`);
  ```

  ```go Go theme={null}
  package main

  import (
      "bytes"
      "encoding/json"
      "fmt"
      "io"
      "net/http"
  )

  func main() {
      url := "https://toapis.com/v1/videos/generations"

      payload := map[string]interface{}{
          "model":      "sora-2",
          "url":        "https://example.com/character-video.mp4",
          "timestamps": "1,3",
      }

      jsonData, _ := json.Marshal(payload)

      req, _ := http.NewRequest("POST", url, bytes.NewBuffer(jsonData))
      req.Header.Set("Authorization", "Bearer YOUR_API_KEY")
      req.Header.Set("Content-Type", "application/json")

      client := &http.Client{}
      resp, err := client.Do(req)
      if err != nil {
          panic(err)
      }
      defer resp.Body.Close()

      body, _ := io.ReadAll(resp.Body)
      fmt.Println(string(body))
  }
  ```
</RequestExample>

<ResponseExample>
  ```json 200 (Отправлено) theme={null}
  {
    "id": "task_01KBYT59JDHB4A3KDDR9N9JVWP",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```

  ```json 200 (Завершено - результат запроса) theme={null}
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
      "message": "Неверный запрос: диапазон timestamps должен быть от 1 до 3 секунд",
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

  ```json 402 (Недостаточно средств) theme={null}
  {
    "error": {
      "code": 402,
      "message": "Недостаточно средств на счёте, пополните баланс",
      "type": "payment_required"
    }
  }
  ```
</ResponseExample>

## Порядок использования

<Steps>
  <Step title="Отправка запроса на создание персонажа">
    Вызовите API с URL видео или ID существующей задачи, содержащей персонажа, а также временным диапазоном
  </Step>

  <Step title="Получение ID задачи">
    API возвращает ID задачи со статусом `queued` или `in_progress`
  </Step>

  <Step title="Запрос статуса задачи">
    Используйте [Запрос статуса видео-задачи](/ru/api-reference/tasks/video-status) для отслеживания прогресса
  </Step>

  <Step title="Использование персонажа в генерации видео">
    После завершения создания персонажа используйте параметр `character_url` для ссылки на ID задачи персонажа
  </Step>
</Steps>

## Лучшие практики

1. **Выбирайте чёткие сегменты с персонажем**: Выбирайте 1-3 секундные сегменты, где черты персонажа наиболее различимы
2. **Обеспечьте качество видео**: Видео высокого разрешения позволяет лучше извлекать характеристики персонажа
3. **Включайте звук**: Видео должно содержать звуковую дорожку
4. **Избегайте нескольких персонажей**: В выбранном временном диапазоне должен быть только один основной персонаж
