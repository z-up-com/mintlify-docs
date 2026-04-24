> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Wan2.6 Генерация видео

> Генерация высококачественных видео с помощью модели Alibaba Cloud Wan2.6 — поддержка текст-в-видео, изображение-в-видео и референсного видео

* Модель генерации видео Alibaba Cloud Wanxiang
* Поддержка **трёх режимов**: текст-в-видео, изображение-в-видео и референсное видео (r2v)
* Сервер автоматически выбирает нужную модель на основе параметров запроса
* Поддержка разрешений 720p/1080p, длительность 5/10/15 секунд
* Не-Flash версия всегда включает аудио в выходное видео

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

## Логика маршрутизации

Сервер автоматически выбирает модель на основе параметров запроса:

| Переданные параметры                  | Режим                     |
| ------------------------------------- | ------------------------- |
| `metadata.reference_urls` (URL видео) | Референсное видео (r2v)   |
| `image_urls` (изображение)            | Изображение-в-видео (i2v) |
| Только `prompt`                       | Текст-в-видео (t2v)       |

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
  Название модели генерации видео, фиксированное значение `wan2.6`
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание содержимого видео

  Обязательно для режима текст-в-видео; опционально для изображение-в-видео и референсного видео (описывает желаемое движение или стиль)

  Пример: `"Милый котенок потягивается на солнце"`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL референсных изображений (режим изображение-в-видео, поддерживает только 1 изображение)

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  Пример: `["https://example.com/image.jpg"]`

  **Примечание:** Нельзя использовать вместе с `metadata.reference_urls`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Соотношение сторон видео (для режимов текст-в-видео и референсного видео)

  Доступные значения:

  * `16:9` - Горизонтальный (по умолчанию)
  * `9:16` - Вертикальный
  * `1:1` - Квадрат
  * `4:3` - Горизонтальный
  * `3:4` - Вертикальный

  По умолчанию: `16:9`

  **Примечание:** Не поддерживается в режиме изображение-в-видео
</ParamField>

<ParamField body="resolution" type="string" default="1080p">
  Разрешение видео

  Доступные значения:

  * `720p` - Стандартное
  * `1080p` - Высокое качество (по умолчанию)

  По умолчанию: `1080p`

  480p не поддерживается. Тарификация посекундная; разные разрешения имеют разную стоимость.
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

  Поддерживается: `5`, `10`, `15`

  По умолчанию: `5`
</ParamField>

<ParamField body="negative_prompt" type="string">
  Негативный промпт — описывает нежелательное содержимое

  Пример: `"размытый, низкое качество, деформированный"`
</ParamField>

<ParamField body="seed" type="integer">
  Случайное зерно для воспроизводимости результатов

  Пример: `12345`
</ParamField>

<ParamField body="prompt_extend" type="boolean" default="true">
  Автоматическое расширение промпта

  При включении система автоматически оптимизирует и обогащает ваш промпт. **Включено по умолчанию** — установите `false` для отключения.
</ParamField>

<ParamField body="audio" type="boolean">
  Включить аудио в видео

  Не-Flash модели Wan2.6 по умолчанию генерируют видео со звуком. Установите `true` для явного включения.
</ParamField>

<ParamField body="shot_type" type="string">
  Тип съёмки (для режимов текст-в-видео и референсного видео)

  Доступные значения:

  * `single` - Один непрерывный план
  * `multi` - Несколько планов (кинематографический монтаж)
</ParamField>

<ParamField body="watermark" type="boolean">
  Добавить водяной знак Alibaba Cloud на видео
</ParamField>

<ParamField body="metadata" type="object">
  Расширенные параметры

  <Expandable title="Показать поля metadata">
    <ParamField body="reference_urls" type="string[]">
      **Режим референсного видео (r2v)** — массив URL референсных видео

      При наличии этого поля сервер направляет запрос к модели референсного видео (wan2.6-r2v). Модель использует эти видео для генерации нового контента с согласованными персонажами или сценами.

      * Каждый элемент должен быть публично доступным URL видео (http\:// или https\://)

      Пример: `["https://cdn.example.com/ref-character.mp4"]`

      **Примечание:** Нельзя использовать вместе с `image_urls`
    </ParamField>
  </Expandable>
</ParamField>

## Комбинации разрешений и соотношений сторон

| Соотношение сторон | Описание                      | Размер 720p | Размер 1080p |
| ------------------ | ----------------------------- | ----------- | ------------ |
| 16:9               | Горизонтальный (по умолчанию) | 1280×720    | 1920×1080    |
| 9:16               | Вертикальный                  | 720×1280    | 1080×1920    |
| 1:1                | Квадрат                       | 960×960     | 1440×1440    |
| 4:3                | Горизонтальный                | 1088×832    | 1632×1248    |
| 3:4                | Вертикальный                  | 832×1088    | 1248×1632    |

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для последующих запросов
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

### Сценарий 1: Текст-в-видео

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "Милый котенок бежит по траве, солнечный день",
  "aspect_ratio": "16:9",
  "resolution": "1080p",
  "duration": 5
}
```

### Сценарий 2: Изображение-в-видео

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "Кот начинает радостно бегать",
  "image_urls": ["https://example.com/cat.jpg"],
  "resolution": "1080p",
  "duration": 10
}
```

### Сценарий 3: Референсное видео (r2v)

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "Персонаж машет рукой и улыбается камере",
  "metadata": {
    "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
  },
  "shot_type": "single",
  "resolution": "1080p",
  "duration": 5
}
```

### Сценарий 4: Текст-в-видео (полные параметры)

```json theme={null}
{
  "model": "wan2.6",
  "prompt": "Золотистый ретривер бежит через поле подсолнухов",
  "negative_prompt": "размытый, низкое качество, деформированный",
  "aspect_ratio": "16:9",
  "resolution": "1080p",
  "duration": 10,
  "seed": 12345,
  "prompt_extend": true,
  "shot_type": "multi",
  "watermark": false
}
```

<Note>
  **Получение результатов задачи**

  Генерация видео — асинхронная задача. После отправки возвращается `task_id`. Используйте интерфейс [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов.
</Note>

<RequestExample>
  ```bash cURL (Текст-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6",
      "prompt": "Милый котенок бежит по траве",
      "aspect_ratio": "16:9",
      "resolution": "1080p",
      "duration": 5
    }'
  ```

  ```bash cURL (Изображение-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6",
      "prompt": "Кот начинает радостно бегать",
      "image_urls": ["https://example.com/cat.jpg"],
      "resolution": "1080p",
      "duration": 10
    }'
  ```

  ```bash cURL (Референсное видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "wan2.6",
      "prompt": "Персонаж машет рукой и улыбается камере",
      "metadata": {
        "reference_urls": ["https://cdn.example.com/ref-character.mp4"]
      },
      "resolution": "1080p",
      "duration": 5
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
          "model": "wan2.6",
          "prompt": "Милый котенок бежит по траве",
          "aspect_ratio": "16:9",
          "resolution": "1080p",
          "duration": 5
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
      model: 'wan2.6',
      prompt: 'Милый котенок бежит по траве',
      aspect_ratio: '16:9',
      resolution: '1080p',
      duration: 5
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "wan2.6",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "aspect_ratio": "16:9"
    }
  }
  ```
</ResponseExample>
