# Генерация видео Azure Sora 2

> Генерация высококачественного видео с использованием модели Azure Sora 2

* Модель генерации видео Azure Sora 2 (официальная конечная точка Azure OpenAI)
* Используйте параметр model для выбора `sora-2-official`
* Поддерживает текст-в-видео, изображение-в-видео и remix
* Асинхронное управление задачами, запрос результатов по ID задачи

## Authorizations

<ParamField header="Authorization" type="string" required>
  Все конечные точки требуют аутентификации Bearer Token

  Получите API Key на [странице управления API Key](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField body="model" type="string" required>
  Название модели генерации видео

  Поддерживаемые модели:

  * `sora-2-official` - Официальная модель Azure Sora 2

  Пример: `"sora-2-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание на естественном языке для генерации видео

  Включите тип кадра, объект, действие, обстановку, освещение и движение камеры для уменьшения неоднозначности. Сохраняйте единую цель для лучшего результата.

  Пример: `"Золотистый ретривер бежит по траве, солнечный день"`
</ParamField>

<ParamField body="duration" type="integer" default="4">
  Продолжительность видео в секундах

  Поддерживаемые значения:

  * `4` - 4 секунды
  * `8` - 8 секунд
  * `12` - 12 секунд

  По умолчанию: `4`

  Пример: `12`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="9:16">
  Соотношение сторон видео

  Поддерживаемые форматы:

  * `16:9` (Горизонтальный, 1280×720)
  * `9:16` (Вертикальный, 720×1280)

  По умолчанию: `9:16`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL-адресов эталонных изображений для генерации изображение-в-видео

  **⚠️ Только формат URL**

  * Публично доступный URL изображения (http\:// или https\://)
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL
  * Используется только первое изображение в качестве эталона
  * Поддерживаемые форматы: JPEG, PNG, WebP
  * **Размер изображения должен быть 1280×720 (16:9) или 720×1280 (9:16)**
</ParamField>

## Response

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемое название модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди на обработку
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Временная метка создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи, включая размер и продолжительность
</ResponseField>

<RequestExample>
  ```bash cURL (Текст-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "Золотистый ретривер бежит по траве, солнечный день",
      "duration": 12,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL (Изображение-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "Оживите эту собаку, бегущую по траве",
      "image_urls": ["https://example.com/dog.jpg"],
      "duration": 8,
      "aspect_ratio": "16:9"
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
          "model": "sora-2-official",
          "prompt": "Золотистый ретривер бежит по траве, солнечный день",
          "duration": 12,
          "aspect_ratio": "16:9"
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
      model: 'sora-2-official',
      prompt: 'Золотистый ретривер бежит по траве, солнечный день',
      duration: 12,
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
    "model": "sora-2-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "1280x720",
      "seconds": "12"
    }
  }
  ```
</ResponseExample>
