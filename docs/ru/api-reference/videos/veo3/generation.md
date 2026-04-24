# Veo3 Генерация видео

> Генерация высококачественного видео с помощью модели Google Veo3

* Асинхронный режим обработки, возвращает ID задачи для последующих запросов
* Поддержка нескольких режимов генерации, включая текст-в-видео и изображение-в-видео
* Ссылки на сгенерированные видео действительны в течение 24 часов, сохраните их своевременно

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

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

<ParamField body="model" type="string" default="veo3.1-fast" required>
  Название модели генерации видео

  Доступные модели:

  * `veo3.1-fast` - Модель быстрой генерации, подходит для быстрого предпросмотра и итераций
  * `veo3.1-quality` - Модель высококачественной генерации, подходит для финальной продукции
  * `veo3.1-lite` - Облегчённая модель генерации, подходит для более дешёвой быстрой генерации

  Пример: `"veo3.1-fast"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации видео
</ParamField>

<ParamField body="duration" type="integer">
  Длительность видео (секунды)

  Фиксированное значение: `8` (VEO3 поддерживает только 8-секундную длительность)
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Разрешение видео

  Поддерживаемые форматы:

  * `16:9` (горизонтальный)
  * `9:16` (вертикальный)
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL референсных изображений для генерации изображение-в-видео

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  **Ограничения:**

  * Максимальный размер файла: 10MB
  * Поддерживаемые форматы: .jpeg, .jpg, .png, .webp
</ParamField>

<ParamField body="metadata" type="object">
  Расширенные параметры, специфичные для VEO3

  <Expandable title="Показать поля metadata">
    <ParamField body="generation_type" type="string">
      Тип генерации видео

      Поддерживаемые типы:

      * `frame` - Кадр-в-видео (FL режим)
      * `reference` - Референсное изображение-в-видео

      Если не указано, автоматически определяется по количеству изображений: 2 изображения для режима frame, 3 для режима reference

      **Примечание: модель `veo3.1-quality` не поддерживает режим `reference`**
    </ParamField>

    <ParamField body="resolution" type="string">
      Разрешение видео

      Поддерживаемые значения:

      * `720p` (по умолчанию)
      * `1080p`
      * `4k`
    </ParamField>

    <ParamField body="enable_gif" type="boolean">
      Включить формат GIF. По умолчанию: `false`

      Примечание: GIF нельзя использовать с разрешением 1080p или 4k
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
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "veo3.1-fast",
      "prompt": "Дельфины прыгают в голубом океане",
      "duration": 8,
      "aspect_ratio": "16:9",
      "image_urls": ["https://example.com/start-frame.jpg", "https://example.com/end-frame.jpg"],
      "metadata": {
        "generation_type": "frame",
        "resolution": "1080p",
        "enable_gif": false
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
          "model": "veo3.1-fast",
          "prompt": "Дельфины прыгают в голубом океане",
          "duration": 8,
          "aspect_ratio": "16:9",
          "image_urls": ["https://example.com/start-frame.jpg", "https://example.com/end-frame.jpg"],
          "metadata": {
              "generation_type": "frame",
              "resolution": "1080p",
              "enable_gif": False
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
      model: 'veo3.1-fast',
      prompt: 'Дельфины прыгают в голубом океане',
      duration: 8,
      aspect_ratio: '16:9',
      image_urls: ['https://example.com/start-frame.jpg', 'https://example.com/end-frame.jpg'],
      metadata: {
        generation_type: 'frame',
        resolution: '1080p',
        enable_gif: false
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
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "veo3.1-fast",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "aspect_ratio": "16:9"
    }
  }
  ```
</ResponseExample>
