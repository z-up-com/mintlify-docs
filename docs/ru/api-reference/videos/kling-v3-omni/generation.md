# Генерация видео Kling v3 Omni

> Унифицированный интерфейс текст-в-видео и изображение-в-видео с синтаксисом `<<<image_N>>>` для ссылки на изображения

- Асинхронный режим обработки, возвращает ID задачи для последующих запросов
- Унифицированный интерфейс текст-в-видео и изображение-в-видео
- Поддержка синтаксиса `<<<image_N>>>` в промпте для ссылки на изображения
- Поддержка стандартного режима (720P) и профессионального режима (1080P)

<Warning>
  **Важное изменение**: Для улучшения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 непосредственно в `image_urls`. Пожалуйста, сначала используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений, а затем используйте полученный URL в этом API.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все эндпоинты требуют аутентификации Bearer Token

Получить API Key: Перейдите на [страницу управления API Key](https://toapis.com/console/token) для получения вашего API Key

Добавьте в заголовки запроса:

```
Authorization: Bearer YOUR_API_KEY
```

</ParamField>

## Параметры запроса

<ParamField body="model" type="string" required>
  Название модели генерации видео, фиксированное значение `kling-v3-omni`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовый промпт

Поддержка синтаксиса `<<<image_N>>>` для ссылки на изображения из `image_urls`, где `N` начинается с 1.

Пример: `"Пусть персонаж на <<<image_1>>> машет рукой в камеру"`

  <Note>
    Если переданы изображения, но в промпте нет ссылки `<<<image_N>>>`, система автоматически добавит `<<<image_1>>>` в начало промпта.
  </Note>
</ParamField>

<ParamField body="mode" type="string" default="std">
  Режим генерации

Варианты:

- `std` - Стандартный режим (720P)
- `pro` - Профессиональный режим (1080P)

По умолчанию: `std`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

Варианты: `5` или `10`

По умолчанию: `5`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Соотношение сторон видео

Варианты:

- `16:9` - Альбомная ориентация (по умолчанию)
- `9:16` - Портретная ориентация
- `1:1` - Квадрат

По умолчанию: `16:9`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL изображений для ссылки в промпте

В промпте используйте `<<<image_N>>>` для ссылки на изображение по индексу (N с 1)

Пример: `["https://example.com/photo.jpg"]`

  <Warning>
    * URL изображений должны быть публично доступны
    * При изображение-в-видео `aspect_ratio` может быть переопределён реальным соотношением сторон изображения
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Специальные параметры расширения

  <Expandable title="Поля metadata">
    <ParamField body="watermark" type="boolean">
      Добавить водяной знак
    </ParamField>
  </Expandable>
</ParamField>

## Синтаксис ссылки на изображения

Модель Omni использует синтаксис `<<<image_N>>>` в промпте для ссылки на изображения:

| Синтаксис       | Описание                                 |
| --------------- | ---------------------------------------- |
| `<<<image_1>>>` | Ссылка на 1-е изображение в `image_urls` |
| `<<<image_2>>>` | Ссылка на 2-е изображение в `image_urls` |

<Note>
  **Автоссылка**: Если переданы `image_urls`, но в промпте нет `<<<image_N>>>`, система автоматически добавит `<<<image_1>>>` в начало промпта.
</Note>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, фиксированное значение `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Название использованной модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

- `queued` - В очереди на обработку
- `in_progress` - Обрабатывается
- `completed` - Успешно завершено
- `failed` - Ошибка

</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Unix-временная метка создания задачи
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

## Примеры использования

### Сценарий 1: Текст-в-видео (стандартный режим)

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "Золотистый ретривер бежит по пляжу на закате, кинематографическое качество",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### Сценарий 2: Ссылка на изображение (одно изображение)

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "Пусть персонаж на <<<image_1>>> машет рукой в камеру",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "pro",
  "duration": 5
}
```

### Сценарий 3: Несколько изображений

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "Персонаж из <<<image_1>>> идёт к сцене из <<<image_2>>>",
  "image_urls": [
    "https://example.com/character.jpg",
    "https://example.com/scene.jpg"
  ],
  "mode": "pro",
  "duration": 5
}
```

### Сценарий 4: Изображение без явной ссылки (автодобавление)

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "Человек медленно поворачивается и улыбается",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

### Сценарий 5: С водяным знаком

```json theme={null}
{
  "model": "kling-v3-omni",
  "prompt": "Море волнуется на закате",
  "mode": "std",
  "duration": 5,
  "metadata": {
    "watermark": true
  }
}
```

<Note>
  **Запрос результатов задачи**

Генерация видео является асинхронной задачей, после отправки возвращается ID задачи. Используйте конечную точку [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов генерации.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "kling-v3-omni",
      "prompt": "Пусть персонаж на <<<image_1>>> машет рукой в камеру",
      "image_urls": ["https://example.com/portrait.jpg"],
      "mode": "std",
      "duration": 5,
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
        "model": "kling-v3-omni",
        "prompt": "Пусть персонаж на <<<image_1>>> машет рукой в камеру",
        "image_urls": ["https://example.com/portrait.jpg"],
        "mode": "std",
        "duration": 5,
        "aspect_ratio": "16:9"
    }
)

task = response.json()
print(f"ID задачи: {task['id']}")
print(f"Статус: {task['status']}")
```

```javascript JavaScript theme={null}
const response = await fetch("https://toapis.com/v1/videos/generations", {
  method: "POST",
  headers: {
    Authorization: "Bearer your-ToAPIs-key",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    model: "kling-v3-omni",
    prompt: "Пусть персонаж на <<<image_1>>> машет рукой в камеру",
    image_urls: ["https://example.com/portrait.jpg"],
    mode: "std",
    duration: 5,
    aspect_ratio: "16:9",
  }),
});

const task = await response.json();
console.log(`ID задачи: ${task.id}`);
console.log(`Статус: ${task.status}`);
```

</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "kling-v3-omni",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
