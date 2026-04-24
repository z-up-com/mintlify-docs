> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Seedream-5.0 Генерация изображений

> Генерация высококачественных изображений с помощью модели Seedream 5.0 lite с поддержкой 2K/3K и поиском в интернете

* Seedream 5.0 lite — новейшая модель генерации изображений от команды ByteDance Seed
* Используйте параметр model для выбора `doubao-seedream-5-0`
* Поддерживает генерацию по тексту, по изображению и с несколькими референсами
* Новая возможность **поиска в интернете** для генерации с актуальными данными
* Поддерживает разрешение **2K** и **3K**
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

<ParamField body="model" type="string" default="doubao-seedream-5-0" required>
  Название модели для генерации изображений

  Пример: `"doubao-seedream-5-0"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Соотношение сторон изображения

  Поддерживаемые соотношения:

  * `1:1` - Квадрат (по умолчанию)
  * `4:3` - Горизонтальный 4:3
  * `3:4` - Вертикальный 3:4
  * `16:9` - Широкоформатный
  * `9:16` - Вертикальный
  * `3:2` - Горизонтальный 3:2
  * `2:3` - Вертикальный 2:3
  * `21:9` - Ультраширокий
  * `9:21` - Ультравысокий
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  Диапазон: 1-15

  По умолчанию: 1

  **Примечание:**

  * Сумма референсных изображений + генерируемых должна быть ≤ 15
  * **Вводите только число (например `1`), без кавычек**

  Оплата предварительно списывается по количеству
</ParamField>

<ParamField body="image_urls" type="string[]">
  Список URL референсных изображений для генерации по изображению

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных файлов

  **Ограничения:**

  * Максимум 10 изображений
  * Форматы: jpeg, png
  * Соотношение сторон: \[1/3, 3]
  * Ширина и высота (px) > 14
  * Размер: не более 10 МБ
  * Всего пикселей: не более 6000×6000 px
</ParamField>

<ParamField body="metadata" type="object">
  Специфичные параметры для расширенной настройки модели Seedream 5.0

  <Expandable title="Свойства metadata">
    <ParamField body="metadata.resolution" type="string" default="2K">
      Разрешение изображения

      Поддерживаемые разрешения:

      * `2K` - Стандартное разрешение (по умолчанию)
      * `3K` - Высокое разрешение

      > **Примечание:** Seedream 5.0 не поддерживает разрешения 1K и 4K

      **Справочные размеры:**

      | Разрешение | 1:1       | 16:9      |
      | ---------- | --------- | --------- |
      | 2K         | 2048×2048 | 2848×1600 |
      | 3K         | 3072×3072 | 4096×2304 |
    </ParamField>

    <ParamField body="metadata.sequential_image_generation" type="string" default="disabled">
      Режим последовательной генерации (функция Doubao)

      * `disabled`: Отключён, генерируется только 1 изображение (по умолчанию)
      * `auto`: Включён, можно генерировать несколько изображений

      **Использование:**

      * ✅ Установите `n: 3` или используйте `"auto"` + `max_images: 3`
      * ✅ Поддерживает генерацию набора по тексту: до 15 изображений
      * ✅ Поддерживает генерацию по изображению с несколькими вариантами
      * ⚠️ При `n > 1` автоматически устанавливается `auto`
    </ParamField>

    <ParamField body="metadata.sequential_image_generation_options" type="object">
      Настройки последовательной генерации

      Доступно при `sequential_image_generation: "auto"`

      **Свойства:**

      * `max_images` (integer): Количество изображений, диапазон 1-15

      **Пример:**

      ```json theme={null}
      "sequential_image_generation_options": { "max_images": 3 }
      ```
    </ParamField>

    <ParamField body="metadata.watermark" type="boolean" default={false}>
      Добавить водяной знак на изображение

      * `true`: С водяным знаком
      * `false`: Без водяного знака (по умолчанию)
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
  Название используемой модели
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

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedream-5-0",
      "prompt": "Милая панда играет в бамбуковом лесу",
      "size": "1:1",
      "n": 1,
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (По изображению) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedream-5-0",
      "prompt": "Милая панда играет в бамбуковом лесу",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/panda.jpg"
      ],
      "metadata": {
        "resolution": "3K"
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
          "model": "doubao-seedream-5-0",
          "prompt": "Милая панда играет в бамбуковом лесу",
          "size": "1:1",
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
      model: 'doubao-seedream-5-0',
      prompt: 'Милая панда играет в бамбуковом лесу',
      size: '1:1',
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
    "model": "doubao-seedream-5-0",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
