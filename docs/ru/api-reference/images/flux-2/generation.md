> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Генерация изображений Flux 2.0

> Генерация высококачественных изображений с помощью модели Flux 2.0, поддержка текст-в-изображение и изображение-в-изображение

* Модель Flux 2.0 для генерации изображений
* Выбор модели через параметр `model`: `flux-2-flex` (быстрее, для быстрых итераций) или `flux-2-pro` (выше качество, лучше детализация)
* Поддержка текст-в-изображение и изображение-в-изображение, до 8 эталонных изображений
* Тарификация по разрешению (1K/2K)
* Асинхронный режим, возвращает `task_id` для запроса результата

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
  Название модели генерации изображений

  Варианты:

  * `flux-2-flex` - Быстрее, подходит для быстрых итераций
  * `flux-2-pro` - Выше качество, лучше детализация
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения
</ParamField>

<ParamField body="size" type="string" default="1:1">
  Соотношение сторон изображения

  Поддерживаемые соотношения:

  * `1:1` - Квадрат (по умолчанию)
  * `4:3` / `3:4` - Классические пропорции
  * `16:9` / `9:16` - Широкоэкранный / вертикальный
  * `3:2` / `2:3` - Стандартные фото
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL эталонных изображений для режима изображение-в-изображение

  **⚠️ Поддерживается только формат URL (base64 не поддерживается)**

  * Публично доступные URL изображений (http\:// или https\://)
  * Используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  **Ограничения:**

  * Максимум 8 эталонных изображений
</ParamField>

<ParamField body="metadata" type="object">
  Метаданные для передачи дополнительных настроек

  <Expandable title="Поля metadata">
    <ParamField body="resolution" type="string" default="1K">
      Разрешение выходного изображения

      Варианты:

      * `1K` - Стандартное разрешение (по умолчанию)
      * `2K` - Высокое разрешение

      **Соответствие разрешения и соотношения сторон:**

      | Соотношение | 1K        | 2K        |
      | ----------- | --------- | --------- |
      | 1:1         | 1024×1024 | 2048×2048 |
      | 4:3         | 1365×1024 | 2730×2048 |
      | 3:4         | 1024×1365 | 2048×2730 |
      | 16:9        | 1820×1024 | 3640×2048 |
      | 9:16        | 1024×1820 | 2048×3640 |
      | 3:2         | 1536×1024 | 3072×2048 |
      | 2:3         | 1024×1536 | 2048×3072 |

      **Примечание:** 2K тарифицируется выше, чем 1K
    </ParamField>
  </Expandable>
</ParamField>

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

  * `queued` - В очереди на обработку
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
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

### Сценарий 1: Текст-в-изображение (быстрая итерация)

```json theme={null}
{
  "model": "flux-2-flex",
  "prompt": "Милый рыжий кот на подоконнике на солнце",
  "size": "1:1",
  "metadata": {
    "resolution": "1K"
  }
}
```

### Сценарий 2: Текст-в-изображение (высокое качество)

```json theme={null}
{
  "model": "flux-2-pro",
  "prompt": "Городской горизонт будущего, неоновые огни, киберпанк стиль, кинематографическое качество",
  "size": "16:9",
  "metadata": {
    "resolution": "2K"
  }
}
```

### Сценарий 3: Изображение-в-изображение (несколько эталонов)

```json theme={null}
{
  "model": "flux-2-pro",
  "prompt": "Сохранить стиль персонажа, сцена бега в лесу",
  "size": "1:1",
  "image_urls": [
    "https://example.com/ref1.jpg",
    "https://example.com/ref2.jpg"
  ],
  "metadata": {
    "resolution": "2K"
  }
}
```

### Сценарий 4: Вертикальный постер

```json theme={null}
{
  "model": "flux-2-flex",
  "prompt": "Постер концерта, минималистичный стиль, тёмный фон",
  "size": "9:16",
  "metadata": {
    "resolution": "1K"
  }
}
```

<Note>
  **Запрос результатов задачи**

  Генерация изображений является асинхронной задачей, после отправки возвращается ID задачи. Используйте конечную точку [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов генерации.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "flux-2-flex",
      "prompt": "Милый рыжий кот на подоконнике на солнце",
      "size": "1:1",
      "metadata": {
        "resolution": "1K"
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
          "model": "flux-2-flex",
          "prompt": "Милый рыжий кот на подоконнике на солнце",
          "size": "1:1",
          "metadata": {
              "resolution": "1K"
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
      model: 'flux-2-flex',
      prompt: 'Милый рыжий кот на подоконнике на солнце',
      size: '1:1',
      metadata: {
        resolution: '1K'
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
    "model": "flux-2-flex",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
