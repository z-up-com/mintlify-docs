> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Gemini-3-Pro Генерация изображений

> Генерация изображений с помощью модели Google Gemini 3 Pro

* Модель генерации изображений Google Gemini 3 Pro (Nano banana2)
* Используйте параметр model для выбора `gemini-3-pro-image-preview`
* Поддержка текст-в-изображение
* Асинхронное управление задачами, запрос результатов по ID задачи

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="gemini-3-pro-image-preview" required>
  Название модели

  Псевдоним: `nano-banana-pro`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации
</ParamField>

<ParamField body="size" type="string">
  Соотношение сторон изображения

  Поддерживаемые форматы:

  * Пропорции: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  Фиксировано на 1

  **⚠️ Примечание:** Должно быть чистым числом (например, `1`), не добавляйте кавычки, иначе возникнет ошибка
</ParamField>

<ParamField body="metadata" type="object">
  Параметры метаданных для передачи дополнительных параметров конфигурации

  <Expandable title="Поддерживаемые поля метаданных">
    <ParamField body="resolution" type="string" default="1K">
      Разрешение выходного изображения

      Поддерживаемые значения:

      * `1K` - разрешение 1K (по умолчанию)
      * `2K` - разрешение 2K
      * `4K` - разрешение 4K
    </ParamField>

    <ParamField body="orientation" type="string">
      Ориентация изображения

      Поддерживаемые значения:

      * `landscape` - Альбомная
      * `portrait` - Портретная
    </ParamField>
  </Expandable>
</ParamField>

<ParamField body="image_urls" type="object[]">
  Список URL-адресов эталонных изображений для изображения-в-изображение или редактирования изображений

  <Expandable title="Подробное описание поля">
    <ParamField body="url" type="string" required>
      URL-адрес изображения

      **⚠️ Только формат URL (base64 больше не поддерживается)**

      * Публично доступный URL изображения (http\:// или https\://)
      * Пример: `https://example.com/image.jpg`
      * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

      **Ограничения:**

      * Одно изображение не должно превышать 10 МБ
      * Поддерживаемые форматы: .jpeg, .jpg, .png, .webp
    </ParamField>
  </Expandable>

  **Ограничение:** Максимум 14 изображений
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
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3-pro-image-preview",
      "prompt": "Футуристический городской пейзаж с неоновыми огнями",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "1K",
        "orientation": "landscape"
      }
    }'
  ```

  ```bash cURL (Изображение-в-изображение) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3-pro-image-preview",
      "prompt": "Улучшить цвета и детали этой фотографии города",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "2K"
      },
      "image_urls": ["https://example.com/city.jpg"]
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
          "model": "gemini-3-pro-image-preview",
          "prompt": "Футуристический городской пейзаж с неоновыми огнями",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "resolution": "1K",
              "orientation": "landscape"
          }
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
          "model": "gemini-3-pro-image-preview",
          "prompt": "Улучшить цвета и детали этой фотографии города",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "resolution": "2K"
          },
          "image_urls": ["https://example.com/city.jpg"]
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
      model: 'gemini-3-pro-image-preview',
      prompt: 'Футуристический городской пейзаж с неоновыми огнями',
      size: '16:9',
      n: 1,
      metadata: {
        resolution: '1K',
        orientation: 'landscape'
      }
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
      model: 'gemini-3-pro-image-preview',
      prompt: 'Улучшить цвета и детали этой фотографии города',
      size: '16:9',
      n: 1,
      metadata: {
        resolution: '2K'
      },
      image_urls: ['https://example.com/city.jpg']
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
    "model": "gemini-3-pro-image-preview",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
