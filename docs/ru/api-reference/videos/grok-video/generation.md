> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Grok Генерация видео

> Генерация высококачественных видео с помощью модели Grok Video

* Модель генерации видео Grok Video
* Выбор модели `grok-video-3` через параметр model
* Поддержка генерации видео из текста и из изображений
* Асинхронное управление задачами, запрос результатов по ID задачи

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 в `images`. Пожалуйста, сначала загрузите изображения через [API загрузки изображений](/docs/ru/api-reference/uploads/images), а затем вызовите данный API с полученным URL.
</Warning>

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
  Название модели генерации видео

  Поддерживаемые модели:

  * `grok-video-3` - Модель генерации видео Grok

  Пример: `"grok-video-3"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации видео
</ParamField>

<ParamField body="duration" type="integer" default="10">
  Продолжительность видео (в секундах)

  Поддерживаемые значения:

  * `10` (по умолчанию)
  * `15`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Соотношение сторон видео

  Поддерживаемые форматы:

  * `16:9` (горизонтальное)
  * `9:16` (вертикальное)
</ParamField>

<ParamField body="images" type="string[]">
  Массив URL-адресов изображений для генерации видео из изображений

  **⚠️ Поддерживается только формат URL (base64 больше не поддерживается)**

  * Общедоступные URL-адреса изображений (http\:// или https\://)
  * Используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений
</ParamField>

<ParamField body="metadata" type="object">
  Метаданные задачи

  <Expandable title="Свойства metadata">
    <ParamField body="resolution" type="string">
      Разрешение видео

      Поддерживаемые значения:

      * `720P`
      * `1080P`
    </ParamField>
  </Expandable>
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
  ```bash cURL (Текст в видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "Золотистый ретривер бежит по зеленому полю в солнечную погоду",
      "duration": 10,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "1080P"
      }
    }'
  ```

  ```bash cURL (Изображение в видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-video-3",
      "prompt": "Оживите эту собаку, пусть она бежит по полю",
      "images": ["https://example.com/dog.jpg"],
      "duration": 10,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720P"
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "grok-video-3",
          "prompt": "Золотистый ретривер бежит по зеленому полю в солнечную погоду",
          "duration": 10,
          "aspect_ratio": "16:9",
          "metadata": {
              "resolution": "1080P"
          }
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'grok-video-3',
      prompt: 'Золотистый ретривер бежит по зеленому полю в солнечную погоду',
      duration: 10,
      aspect_ratio: '16:9',
      metadata: {
        resolution: '1080P'
      }
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
    "id": "video_abc123def456",
    "object": "generation.task",
    "model": "grok-video-3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
