> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# GPT-4o Генерация изображений

> Генерация изображений с помощью модели GPT-4o, поддержка текст-в-изображение и редактирования

* Унифицированный API генерации изображений
* Используйте параметр model для выбора модели `gpt-4o-image`
* Поддержка текст-в-изображение, изображение-в-изображение и редактирования
* Асинхронное управление задачами, запрос результатов по ID задачи

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

<ParamField body="model" type="string" default="gpt-4o-image" required>
  Название модели генерации изображений

  Пример: `"gpt-4o-image"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения

  Максимум 1000 символов
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Соотношение сторон изображения

  Поддерживаемые форматы:

  * `1:1` - Квадрат (по умолчанию)
  * `2:3` - Портрет
  * `3:2` - Пейзаж
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  Поддерживает 1, 2, 4. Оплата будет предварительно вычтена на основе количества

  По умолчанию: 1

  **⚠️ Примечание:** Необходимо вводить простое число (например, `1`), не используйте кавычки, иначе возникнет ошибка
</ParamField>

<ParamField body="image_urls" type="string[]">
  Список URL-адресов эталонных изображений для изображения-в-изображение или редактирования

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  **Ограничения:**

  * Максимум 5 изображений
  * Одно изображение до 10 МБ
  * Поддерживаемые форматы: jpeg, jpg, png, webp
</ParamField>

<ParamField body="mask_url" type="string">
  URL-адрес маски изображения (для редактирования)

  * Должен быть в формате PNG
  * Размер должен соответствовать эталонному изображению
  * Максимум 4 МБ
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запросов статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемая модель
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Прогресс задачи в процентах (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Временная метка создания (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-4o-image",
      "prompt": "Древний замок под звездным небом",
      "size": "1:1",
      "n": 1
    }'
  ```

  ```bash cURL (Изображение-в-изображение) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-4o-image",
      "prompt": "Преобразуйте это изображение в древний замок под звездным небом",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/castle.jpg"
      ]
    }'
  ```

  ```bash cURL (Редактирование изображения) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-4o-image",
      "prompt": "Добавьте фейерверк над замком",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/castle.jpg"
      ],
      "mask_url": "https://example.com/mask.png"
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gpt-4o-image",
          "prompt": "Древний замок под звездным небом",
          "size": "1:1",
          "n": 1
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```python Python (Изображение-в-изображение) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gpt-4o-image",
          "prompt": "Преобразуйте это изображение в древний замок под звездным небом",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/castle.jpg"]
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gpt-4o-image',
      prompt: 'Древний замок под звездным небом',
      size: '1:1',
      n: 1
    })
  });

  const task = await response.json();
  console.log(`ID задачи: ${task.id}`);
  console.log(`Статус: ${task.status}`);
  ```

  ```javascript JavaScript (Изображение-в-изображение) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gpt-4o-image',
      prompt: 'Преобразуйте это изображение в древний замок под звездным небом',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/castle.jpg']
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
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gpt-4o-image",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```

  ```json 400 theme={null}
  {
    "error": {
      "code": "invalid_request",
      "message": "Поле 'prompt' обязательно.",
      "param": "prompt",
      "type": "invalid_request_error"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": "unauthorized",
      "message": "Неверный API ключ",
      "type": "authentication_error"
    }
  }
  ```
</ResponseExample>
