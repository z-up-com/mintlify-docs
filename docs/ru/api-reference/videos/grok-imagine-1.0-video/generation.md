# Grok Imagine 1.0 Генерация видео

> Асинхронная генерация видео с grok-imagine-1.0-video

* Унифицированный endpoint генерации видео
* Асинхронный режим задач, возвращает ID задачи
* Поддерживает text-to-video и image-to-video

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все endpoint требуют Bearer Token

  Получите API Key в [управлении API ключами](https://toapis.com/console/token)

  Пример заголовка:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="grok-imagine-1.0-video" required>
  Название модели:

  * `grok-imagine-1.0-video`
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание видео, поддерживаются разные языки
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Соотношение сторон видео

  Поддерживаемые форматы:

  * `16:9` - Горизонтальный (по умолчанию)
  * `9:16` - Вертикальный
  * `1:1` - Квадрат
  * `3:2` - Горизонтальный
  * `2:3` - Вертикальный
</ParamField>

<ParamField body="duration" type="integer" default={6}>
  Длительность видео в секундах

  Диапазон: 6-30 (минимум 6 секунд, максимум 30 секунд)

  ⚠️ Примечание: указывайте число без кавычек (например `6`), иначе возникнет ошибка
</ParamField>

<ParamField body="quality" type="string" default="480p">
  Качество видео

  Поддерживаемые значения:

  * `480p` (по умолчанию)
  * `720p`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL референс-изображений (для image-to-video)

  Ограничения:

  * До 7 изображений
  * Только публичные URL
  * Base64 не поддерживается
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный ID задачи
</ResponseField>

<ResponseField name="object" type="string">
  Всегда `generation.task`
</ResponseField>

<ResponseField name="status" type="string">
  Статус: `queued` / `in_progress` / `completed` / `failed`
</ResponseField>

<RequestExample>
  ```bash cURL (Текст в видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0-video",
      "prompt": "Собака бежит по пляжу, солнечная погода, slow motion",
      "aspect_ratio": "16:9",
      "duration": 6,
      "quality": "720p"
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0-video",
          "prompt": "Собака бежит по пляжу, солнечная погода, slow motion",
          "aspect_ratio": "16:9",
          "duration": 6,
          "quality": "720p",
      },
  )

  print(response.status_code)
  print(response.json())
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0-video',
      prompt: 'Собака бежит по пляжу, солнечная погода, slow motion',
      aspect_ratio: '16:9',
      duration: 6,
      quality: '720p',
    }),
  });

  console.log(response.status);
  console.log(await response.json());
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_01JNXXXXXXXXXXXXXXXXXX",
    "object": "generation.task",
    "model": "grok-imagine-1.0-video",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
