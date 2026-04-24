# Grok Imagine 1.0 Генерация изображений

> Асинхронная генерация изображений с помощью grok-imagine-1.0

* Унифицированный endpoint генерации изображений
* Асинхронный режим задач, возвращает ID задачи
* Укажите `model` как `grok-imagine-1.0`

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

  * `grok-imagine-1.0`
</ParamField>

<ParamField body="prompt" type="string" required>
  Промпт для генерации, поддерживаются разные языки
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Размер генерации изображения

  Поддерживаемые форматы:

  * `1:1` - Квадрат (по умолчанию)
  * `16:9` - Горизонтальный широкий
  * `9:16` - Вертикальный высокий
  * `3:2` - Горизонтальный
  * `2:3` - Вертикальный
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  Диапазон: 1-10 (минимум 1, максимум 10)

  ⚠️ Примечание: указывайте только число (например `1`), без кавычек
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
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "grok-imagine-1.0",
      "prompt": "Рыжий кот сидит на солнечном подоконнике, стиль масляной живописи",
      "size": "1:1",
      "n": 1
    }'
  ```

  ```python Python theme={null}
  import requests

  resp = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "grok-imagine-1.0",
          "prompt": "Рыжий кот сидит на солнечном подоконнике, стиль масляной живописи",
          "size": "1:1",
          "n": 1,
      },
  )

  print(resp.status_code)
  print(resp.json())
  ```

  ```javascript JavaScript theme={null}
  const resp = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'grok-imagine-1.0',
      prompt: 'Рыжий кот сидит на солнечном подоконнике, стиль масляной живописи',
      size: '1:1',
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
    "model": "grok-imagine-1.0",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
