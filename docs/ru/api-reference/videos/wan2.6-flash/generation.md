---
title: "Wan2.6 Flash Генерация видео"
---
> Генерация видео с помощью модели Alibaba Cloud Wan2.6 Flash — быстрый режим изображение-в-видео и референсного видео

* Ускоренная модель генерации видео Alibaba Cloud Wanxiang Flash
* Поддержка **двух режимов**: изображение-в-видео и референсное видео (r2v)
* **Генерация только из текста не поддерживается** — необходимо предоставить изображение или URL референсного видео
* Поддержка разрешений 720p/1080p; наличие/отсутствие аудио влияет на тарификацию
* Более высокая скорость генерации — идеально для быстрого просмотра и итераций

<Warning>
  **Изображение или URL референсного видео обязательны**: Необходимо предоставить `image_urls` (изображение-в-видео) или `metadata.reference_urls` (референсное видео). Запрос только с промптом вернёт ошибку.
</Warning>

## Логика маршрутизации

Сервер автоматически выбирает модель на основе параметров запроса:

| Переданные параметры                  | Режим                                 |
| ------------------------------------- | ------------------------------------- |
| `metadata.reference_urls` (URL видео) | Референсное видео Flash (r2v-flash)   |
| `image_urls` (изображение)            | Изображение-в-видео Flash (i2v-flash) |
| Только `prompt`                       | ❌ Возвращает ошибку                   |

## Аутентификация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Параметры запроса

<ParamField body="model" type="string" required>
  Название модели генерации видео, фиксированное значение `wan2.6-flash`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL референсных изображений (режим изображение-в-видео, только 1 изображение)

  **Обязательно, если не указан `metadata.reference_urls`**

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений

  Пример: `["https://example.com/image.jpg"]`
</ParamField>

<ParamField body="prompt" type="string">
  Описание содержимого видео (опционально)

  Описывает ожидаемое движение или стиль для направления модели

  Пример: `"Человек оживает и улыбается камере"`
</ParamField>

<ParamField body="resolution" type="string" default="1080p">
  Разрешение видео

  Доступные значения:

  * `720p` - Стандартное
  * `1080p` - Высокое качество (по умолчанию)

  По умолчанию: `1080p`

  Разные разрешения имеют разную стоимость.
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

  Поддерживается: `5`, `10`, `15`

  По умолчанию: `5`
</ParamField>

<ParamField body="audio" type="boolean" default="true">
  Генерировать видео со звуком

  * `true` - Видео со звуком (по умолчанию)
  * `false` - Беззвучное видео

  **⚠️ Видео со звуком и без тарифицируются по-разному — выбирайте осознанно**

  По умолчанию: `true`
</ParamField>

<ParamField body="watermark" type="boolean">
  Добавить водяной знак Alibaba Cloud на видео
</ParamField>

<ParamField body="metadata" type="object">
  Расширенные параметры

  <Expandable title="Показать поля metadata">
    <ParamField body="reference_urls" type="string[]">
      **Режим референсного видео (r2v)** — массив URL референсных видео

      При наличии этого поля сервер направляет запрос к ускоренной модели референсного видео (wan2.6-r2v-flash). Модель использует эти видео для генерации нового контента с согласованными персонажами.

      * Каждый элемент должен быть публично доступным URL видео (http\:// или https\://)

      Пример: `["https://cdn.example.com/ref-character.mp4"]`

      **Примечание:** Нельзя использовать вместе с `image_urls`
    </ParamField>
  </Expandable>
</ParamField>

## Тарификация: со звуком vs без звука

| Режим                      | Цена за 720p | Цена за 1080p |
| -------------------------- | ------------ | ------------- |
| Со звуком (`audio: true`)  | ¥0.3 / сек   | ¥0.5 / сек    |
| Без звука (`audio: false`) | ¥0.15 / сек  | ¥0.25 / сек   |

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Использованное название модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершена
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

## Сценарии использования

### Сценарий 1: Изображение-в-видео (со звуком)

```json theme={null}
{
  "model": "wan2.6-flash",
  "image_urls": ["https://example.com/photo.jpg"],
  "prompt": "Человек оживает и улыбается",
  "resolution": "1080p",
  "duration": 5,
  "audio": true
}
```

### Сценарий 2: Изображение-в-видео (без звука, экономия)

```json theme={null}
{
  "model": "wan2.6-flash",
  "image_urls": ["https://example.com/photo.jpg"],
  "resolution": "720p",
  "duration": 5,
  "audio": false
}
```

### Сценарий 3: Референсное видео (r2v-flash)

```json theme={null}
{
  "model": "wan2.6-flash",
  "prompt": "Персонаж смотрит фильм на диване",
  "metadata": {
    "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
  },
  "resolution": "1080p",
  "duration": 5,
  "audio": true
}
```

<Note>
  **Получение результатов задачи**

  Генерация видео — асинхронная задача. После отправки возвращается `task_id`. Используйте интерфейс [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов.
</Note>

<RequestExample>
  ```bash cURL (Изображение-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6-flash",
      "image_urls": ["https://example.com/photo.jpg"],
      "prompt": "Человек оживает и улыбается",
      "resolution": "1080p",
      "duration": 5,
      "audio": true
    }'
  ```

  ```bash cURL (Референсное видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6-flash",
      "prompt": "Персонаж смотрит фильм на диване",
      "metadata": {
        "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
      },
      "resolution": "1080p",
      "duration": 5,
      "audio": true
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
          "model": "wan2.6-flash",
          "image_urls": ["https://example.com/photo.jpg"],
          "prompt": "Человек оживает и улыбается",
          "resolution": "1080p",
          "duration": 5,
          "audio": True
      }
  )

  task = response.json()
  print(f"Task ID: {task['id']}")
  print(f"Status: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'wan2.6-flash',
      image_urls: ['https://example.com/photo.jpg'],
      prompt: 'Человек оживает и улыбается',
      resolution: '1080p',
      duration: 5,
      audio: true
    })
  });

  const task = await response.json();
  console.log(`Task ID: ${task.id}`);
  console.log(`Status: ${task.status}`);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_01K2ABJPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "wan2.6-flash",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
