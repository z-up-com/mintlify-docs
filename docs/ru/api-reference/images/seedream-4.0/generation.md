---
title: "Seedream-4.0 Генерация изображений"
---
> Генерация высококачественных изображений с помощью модели Seedream 4.0

* Модель генерации изображений Seedream 4.0
* Используйте параметр model для выбора `doubao-seedream-4-0`
* Поддержка текст-в-изображение
* Асинхронное управление задачами

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="doubao-seedream-4-0" required>
  Название модели: `"doubao-seedream-4-0"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения

  Максимум 1000 символов
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Соотношение сторон изображения

  Поддерживаемые соотношения:

  * `1:1` - Квадрат (по умолчанию)
  * `4:3` - Горизонтальный 4:3
  * `3:4` - Вертикальный 3:4
  * `16:9` - Горизонтальный широкоэкранный
  * `9:16` - Вертикальный длинный
  * `3:2` - Горизонтальный 3:2
  * `2:3` - Вертикальный 2:3
  * `21:9` - Ультраширокий
  * `9:21` - Ультравысокий
  * `auto` - Автоматическое соответствие соотношению эталонного изображения (требуется `image_urls`)
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  Диапазон: 1-15 (минимум 1, максимум 15)

  По умолчанию: 1

  **Примечание:**

  * Фактическое количество изображений зависит от этого параметра и количества входных эталонных изображений. Общее количество эталонных изображений + генерируемых изображений должно быть ≤ 15
  * **Необходимо вводить простое число (например, `1`), не используйте кавычки, иначе возникнет ошибка**

  Оплата будет предварительно вычтена на основе количества
</ParamField>

<ParamField body="image_urls" type="string[]">
  Список URL-адресов эталонных изображений для изображения-в-изображение или редактирования

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL-адрес изображения (http\:// или https\://)
  * Пример: `https://example.com/image.jpg`
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  **Ограничения:**

  * Максимум 10 изображений
  * Форматы изображений: jpeg, png
  * Диапазон соотношения сторон (ширина/высота): \[1/3, 3]
  * Ширина и высота (px) > 14
  * Размер одного изображения: не более 10 МБ
  * Общее количество пикселей: не более 6000×6000 px
</ParamField>

<ParamField body="metadata" type="object">
  Специфичные для канала параметры для расширенной конфигурации модели Seedream 4.0

  <Expandable title="Свойства metadata">
    <ParamField body="metadata.resolution" type="string" default="2K">
      Разрешение изображения

      Поддерживаемые разрешения:

      * `1K` - Базовое разрешение
      * `2K` - Стандартное разрешение (по умолчанию)
      * `4K` - Высокое разрешение

      **Справочные размеры разрешений:**

      | Разрешение | Размер 1:1 | Размер 16:9 |
      | ---------- | ---------- | ----------- |
      | 1K         | 1024x1024  | 1280x720    |
      | 2K         | 2048x2048  | 2560x1440   |
      | 4K         | 4096x4096  | 5404x3040   |
    </ParamField>

    <ParamField body="metadata.optimize_prompt_options" type="object">
      Параметры оптимизации промпта

      **Свойства:**

      * `mode` (строка): Режим оптимизации
        * `standard`: Стандартный режим, генерирует контент более высокого качества с большим временем обработки (по умолчанию)
        * `fast`: Быстрый режим, генерирует контент за более короткое время с обычным качеством

      **Пример:**

      ```json theme={null}
      "optimize_prompt_options": { "mode": "standard" }
      ```
    </ParamField>

    <ParamField body="metadata.sequential_image_generation" type="string" default="disabled">
      Режим последовательной генерации изображений (специфическая функция Doubao)

      Контролирует, следует ли генерировать несколько изображений:

      * `disabled`: Отключить последовательный режим, генерирует только 1 изображение (по умолчанию)
      * `auto`: Включить последовательный режим, может генерировать несколько изображений

      **Примечания по использованию:**

      * ✅ Установите `n: 3` или используйте `sequential_image_generation: "auto"` + `max_images: 3`
      * ✅ Поддерживает генерацию по тексту: до 15 изображений только из текстового промпта
      * ✅ Поддерживает генерацию по изображению: предоставьте `image_urls` для нескольких вариаций
      * ⚠️ Когда `n > 1`, автоматически устанавливается в `auto`

      **Примечание:**

      * Общее количество эталонных + генерируемых изображений ≤ 15
    </ParamField>

    <ParamField body="metadata.sequential_image_generation_options" type="object">
      Параметры последовательной генерации изображений

      Доступно, когда `sequential_image_generation` установлен в `auto`

      **Свойства:**

      * `max_images` (целое число): Укажите количество изображений для генерации, Диапазон: 1-15

      **Пример:**

      ```json theme={null}
      "sequential_image_generation_options": { "max_images": 3 }
      ```
    </ParamField>

    <ParamField body="metadata.watermark" type="boolean" default={true}>
      Добавлять ли водяной знак к сгенерированному изображению

      * `true`: Добавить водяной знак (по умолчанию)
      * `false`: Без водяного знака
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
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedream-4-0",
      "prompt": "Милая панда играет в бамбуковом лесу",
      "size": "1:1",
      "n": 1,
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Изображение-в-изображение) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedream-4-0",
      "prompt": "Милая панда играет в бамбуковом лесу",
      "size": "1:1",
      "n": 1,
      "image_urls": [
        "https://example.com/panda.jpg"
      ],
      "metadata": {
        "resolution": "2K"
      }
    }'
  ```

  ```bash cURL (Расширенные функции) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedream-4-0",
      "prompt": "Милая панда играет в бамбуковом лесу",
      "size": "1:1",
      "n": 3,
      "image_urls": [
        "https://example.com/panda.jpg"
      ],
      "metadata": {
        "resolution": "4K",
        "optimize_prompt_options": "standard",
        "sequential_image_generation": "auto",
        "sequential_image_generation_options": { "max_images": 3 },
        "watermark": false
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
          "model": "doubao-seedream-4-0",
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

  ```python Python (Изображение-в-изображение) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "doubao-seedream-4-0",
          "prompt": "Милая панда играет в бамбуковом лесу",
          "size": "1:1",
          "n": 1,
          "image_urls": ["https://example.com/panda.jpg"],
          "metadata": {
              "resolution": "2K"
          }
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```python Python (Расширенные функции) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "doubao-seedream-4-0",
          "prompt": "Милая панда играет в бамбуковом лесу",
          "size": "1:1",
          "n": 3,
          "image_urls": ["https://example.com/panda.jpg"],
          "metadata": {
              "resolution": "4K",
              "optimize_prompt_options": "standard",
              "sequential_image_generation": "auto",
              "sequential_image_generation_options": {"max_images": 3},
              "watermark": False
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
      model: 'doubao-seedream-4-0',
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

  ```javascript JavaScript (Изображение-в-изображение) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'doubao-seedream-4-0',
      prompt: 'Милая панда играет в бамбуковом лесу',
      size: '1:1',
      n: 1,
      image_urls: ['https://example.com/panda.jpg'],
      metadata: {
        resolution: '2K'
      }
    })
  });

  const task = await response.json();
  console.log(`ID задачи: ${task.id}`);
  console.log(`Статус: ${task.status}`);
  ```

  ```javascript JavaScript (Расширенные функции) theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'doubao-seedream-4-0',
      prompt: 'Милая панда играет в бамбуковом лесу',
      size: '1:1',
      n: 3,
      image_urls: ['https://example.com/panda.jpg'],
      metadata: {
        resolution: '4K',
        optimize_prompt_options: 'standard',
        sequential_image_generation: 'auto',
        sequential_image_generation_options: { max_images: 3 },
        watermark: false
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
    "model": "doubao-seedream-4-0",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
