# Grok Imagine 1.0 Редактирование изображений

> Асинхронное редактирование изображений с grok-imagine-1.0-edit на основе референсов

* Унифицированный endpoint редактирования изображений
* Асинхронный режим задач, возвращает ID задачи
* Укажите `model` как `grok-imagine-1.0-edit`

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все endpoint требуют аутентификации Bearer Token

  Получите API Key в [управлении API ключами](https://toapis.com/console/token)

  Пример заголовка:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" required>
  Название модели:

  * `grok-imagine-1.0-edit`
</ParamField>

<ParamField body="prompt" type="string" required>
  Промпт для редактирования, поддерживаются разные языки
</ParamField>

<ParamField body="image_urls" type="string[]" required>
  Массив URL исходных изображений, минимум 1 изображение

  Пример:

  * `["https://example.com/original.png"]`

  При необходимости сначала загрузите изображение через [API загрузки изображений](/docs/ru/api-reference/uploads/images)
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество изображений, рекомендуемый диапазон 1-10
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
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/edits \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0-edit",
      "prompt": "Измени фон на звездное небо, сохрани главный объект",
      "image_urls": ["https://example.com/original.png"],
      "n": 1
    }'
  ```

  ```python Python theme={null}
  import requests

  resp = requests.post(
      "https://toapis.com/v1/images/edits",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0-edit",
          "prompt": "Измени фон на звездное небо, сохрани главный объект",
          "image_urls": ["https://example.com/original.png"],
          "n": 1,
      },
  )

  print(resp.status_code)
  print(resp.json())
  ```

  ```javascript JavaScript theme={null}
  const resp = await fetch('https://toapis.com/v1/images/edits', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0-edit',
      prompt: 'Измени фон на звездное небо, сохрани главный объект',
      image_urls: ['https://example.com/original.png'],
      n: 1,
    }),
  });

  console.log(resp.status);
  console.log(await resp.json());
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_01JNXXXXXXXXXXXXXXXXXX",
    "object": "generation.task",
    "model": "grok-imagine-1.0-edit",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
