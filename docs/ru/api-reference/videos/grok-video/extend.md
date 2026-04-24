> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Grok Продление видео

> Продление сгенерированных видео Grok

* Функция продления видео Grok
* Увеличение продолжительности существующих видео
* Асинхронное управление задачами, запрос результатов по ID задачи

## Параметры пути

<ParamField path="video_id" type="string" required>
  ID исходной видео-задачи

  Это ID задачи, возвращённый из предыдущего запроса на генерацию видео
</ParamField>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API-запросы требуют аутентификации с помощью Bearer Token

  Получение API Key: Перейдите на [страницу управления API Key](https://toapis.com/console/token) для получения вашего API Key

  Добавьте в заголовки запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="grok-video-3" required>
  Название модели продления видео

  Пример: `"grok-video-3"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для продления видео
</ParamField>

<ParamField body="size" type="string">
  Размер видео
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Название используемой модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди на обработку
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Прогресс задачи в процентах (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Временная метка создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/video_abc123def456/extend \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "Продолжите эту сцену, пусть собака бежит к горизонту"
    }'
  ```

  ```python Python theme={null}
  import requests

  video_id = "video_abc123def456"

  response = requests.post(
      f"https://toapis.com/v1/videos/{video_id}/extend",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "grok-video-3",
          "prompt": "Продолжите эту сцену, пусть собака бежит к горизонту"
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const videoId = 'video_abc123def456';

  const response = await fetch(`https://toapis.com/v1/videos/${videoId}/extend`, {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'grok-video-3',
      prompt: 'Продолжите эту сцену, пусть собака бежит к горизонту'
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
    "id": "video_extend_abc123",
    "object": "generation.task",
    "model": "grok-video-3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
