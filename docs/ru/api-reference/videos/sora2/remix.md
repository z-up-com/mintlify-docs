# Sora2 Ремикс видео

> Редактирование и модификация сгенерированных видео

* Функция ремикса видео Sora2
* Ремикс и редактирование существующих сгенерированных видео
* Поддержка вторичного создания на основе существующих видео
* Асинхронный режим обработки, возвращает ID задачи для последующих запросов

## Параметры пути

<ParamField path="video_id" type="string" required>
  ID задачи оригинального видео

  Это ID задачи, возвращенный из предыдущего запроса генерации видео
</ParamField>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="sora-2" required>
  Название модели для ремикса видео

  Поддерживаемые модели:

  * `sora-2` - Стандартная версия
  * `sora-2-pro` - Профессиональная версия, поддерживает большую длительность
  * `sora-2-vip` - VIP версия, более высокий приоритет

  Пример: `"sora-2"` или `"sora-2-pro"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание инструкции ремикса
</ParamField>

<ParamField body="duration" type="integer">
  Длительность видео в секундах

  * `sora-2`: Поддерживает 10 или 15 секунд
  * `sora-2-pro`: Поддерживает 15 секунд (HD) или 25 секунд

  Пример: `15`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Соотношение сторон видео

  Поддерживаемые соотношения:

  * `16:9` - Стандартный широкоэкранный (рекомендуется)
  * `9:16` - Портретный режим
  * `1:1` - Квадрат

  Пример: `"16:9"`
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемое имя модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди на обработку
  * `in_progress` - Обработка
  * `completed` - Успешно завершено
  * `failed` - Не удалось
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Временная метка создания задачи (временная метка Unix)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/task_01K8SGYNNNVBQTXNR4MM964S7K/remix \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Добавьте щенка, играющего в сцене",
      "duration": 15,
      "aspect_ratio": "16:9"
    }'
  ```

  ```python Python theme={null}
  import requests

  video_id = "task_01K8SGYNNNVBQTXNR4MM964S7K"

  response = requests.post(
      f"https://toapis.com/v1/videos/{video_id}/remix",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "prompt": "Добавьте щенка, играющего в сцене",
          "duration": 15,
          "aspect_ratio": "16:9"
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const videoId = 'task_01K8SGYNNNVBQTXNR4MM964S7K';

  const response = await fetch(`https://toapis.com/v1/videos/${videoId}/remix`, {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      prompt: 'Добавьте щенка, играющего в сцене',
      duration: 15,
      aspect_ratio: '16:9'
    })
  });

  const task = await response.json();
  console.log(`ID задачи: ${task.id}`);
  console.log(`Статус: ${task.status}`);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "720x720"
    }
  }
  ```
</ResponseExample>
