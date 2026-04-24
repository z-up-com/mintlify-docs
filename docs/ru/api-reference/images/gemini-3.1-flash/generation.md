---
title: "Gemini-3.1-Flash Генерация изображений"
---
> Генерация изображений с помощью Google Gemini 3.1 Flash, поддержка экстремальных соотношений сторон и улучшения через Google Search

* Модель генерации изображений Google Gemini 3.1 Flash (Nano banana2)
* Используйте параметр model для выбора `gemini-3.1-flash-image-preview`
* Поддержка текст-в-изображение и изображение-в-изображение, вывод до 4K разрешения
* До 14 референсных изображений для сохранения стиля/консистентности персонажей
* Поддержка экстремальных соотношений сторон (1:4, 4:1, 1:8, 8:1)
* Интегрированное улучшение через Google Search для генерации более реалистичных изображений
* Асинхронное управление задачами, запрос результатов по ID задачи

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API Key](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="gemini-3.1-flash-image-preview" required>
  Название модели генерации изображений

  Пример: `"gemini-3.1-flash-image-preview"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения
</ParamField>

<ParamField body="size" type="string">
  Соотношение сторон изображения

  Поддерживаемые значения:

  | Значение        | Применение                       |
  | --------------- | -------------------------------- |
  | `1:1`           | Квадрат, аватар, социальные сети |
  | `3:2` / `2:3`   | Стандартное фото                 |
  | `4:3` / `3:4`   | Традиционный монитор             |
  | `16:9` / `9:16` | Широкоформатный / вертикальный   |
  | `5:4` / `4:5`   | Instagram                        |
  | `21:9`          | Ультраширокий баннер             |
  | `1:4` / `4:1`   | Длинный постер / баннер          |
  | `1:8` / `8:1`   | Экстремально длинное изображение |
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  **⚠️ Примечание:** Должно быть чистым числом (например `1`), без кавычек, иначе будет ошибка
</ParamField>

<ParamField body="image_urls" type="object[]">
  Список URL референсных изображений для режима изображение-в-изображение

  <Expandable title="Подробное описание полей">
    <ParamField body="url" type="string" required>
      URL изображения

      **⚠️ Только формат URL (base64 больше не поддерживается)**

      * Публично доступный URL изображения (http\:// или https\://)
      * Пример: `https://example.com/image.jpg`
      * Используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для получения URL

      **Ограничения:**

      * Один файл не более 10MB
      * Поддерживаемые форматы: .jpeg, .jpg, .png, .webp
    </ParamField>
  </Expandable>

  **Ограничение:** Максимум 14 изображений (рекомендуется: до 10 объектных + 4 персонажных референса)
</ParamField>

<ParamField body="metadata" type="object">
  Метаданные для передачи дополнительных параметров конфигурации

  <Expandable title="Поддерживаемые поля метаданных">
    <ParamField body="resolution" type="string" default="1K">
      Разрешение выходного изображения

      Поддерживаемые значения:

      * `0.5K` — \~512px, предпросмотр низкого разрешения
      * `1K` — \~1024px, стандартное разрешение (по умолчанию)
      * `2K` — \~2048px, высокое разрешение
      * `4K` — \~4096px, сверхвысокое разрешение

      **Примечание:** Стоимость зависит от разрешения; 4K дороже 1K
    </ParamField>

    <ParamField body="google_search" type="boolean" default="false">
      Включить улучшение через текстовый поиск Google

      * `true`: Модель сначала ищет текстовую информацию в интернете для улучшения генерации
      * `false`: Отключено (по умолчанию)
    </ParamField>

    <ParamField body="google_image_search" type="boolean" default="false">
      Включить улучшение через поиск изображений Google

      * `true`: Помимо текстового поиска, также ищет референсные изображения
      * `false`: Отключено (по умолчанию)

      **Примечание:** Требует `google_search: true`
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
  Используемое название модели
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
  Временная метка создания задачи (Unix timestamp)
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
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "Ночной город в стиле киберпанк с мерцающими неоновыми огнями",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Изображение в изображение) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3.1-flash-image-preview",
      "prompt": "Преобразуй это фото в стиль акварели",
      "size": "1:1",
      "n": 1,
      "image_urls": ["https://example.com/photo.jpg"],
      "metadata": {
        "resolution": "2K"
      }
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
          "model": "gemini-3.1-flash-image-preview",
          "prompt": "Ночной город в стиле киберпанк с мерцающими неоновыми огнями",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "resolution": "2K"
          }
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
      model: 'gemini-3.1-flash-image-preview',
      prompt: 'Ночной город в стиле киберпанк с мерцающими неоновыми огнями',
      size: '16:9',
      n: 1,
      metadata: {
        resolution: '2K'
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
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gemini-3.1-flash-image-preview",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
