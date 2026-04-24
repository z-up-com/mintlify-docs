# Генерация изображений Flux Kontext

> Генерация и редактирование изображений с помощью модели Flux Kontext, поддержка текст-в-изображение и редактирование изображений

* Модель Flux Kontext для генерации и редактирования изображений
* Выбор модели через параметр `model`: `flux-kontext-pro` (Pro) или `flux-kontext-max` (Max, более высокое качество)
* Поддержка текст-в-изображение и редактирование изображений
* С эталонным изображением — режим редактирования, без эталонного — текст-в-изображение
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

  * `flux-kontext-pro` - Pro-версия
  * `flux-kontext-max` - Max-версия, более высокое качество
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения
</ParamField>

<ParamField body="size" type="string">
  Соотношение сторон изображения

  По умолчанию:

  * С эталонным изображением: `match_input_image` (соответствие входному изображению)
  * Без эталонного: `16:9`

  Поддерживаемые соотношения:

  | Значение            | Описание                                                      |
  | ------------------- | ------------------------------------------------------------- |
  | `match_input_image` | Соответствие входному изображению (только при редактировании) |
  | `1:1`               | Квадрат                                                       |
  | `4:3` / `3:4`       | Классические пропорции                                        |
  | `16:9` / `9:16`     | Широкоэкранный / вертикальный                                 |
  | `3:2` / `2:3`       | Стандартные фото                                              |
  | `21:9` / `9:21`     | Ультраширокий / ультраузкий                                   |
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL эталонных изображений для редактирования

  **⚠️ Поддерживается только формат URL (base64 не поддерживается)**

  * Публично доступные URL изображений (http\:// или https\://)
  * Используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  **Ограничения:**

  * Максимум 1 изображение
  * При передаче изображения — режим редактирования, без передачи — режим текст-в-изображение
</ParamField>

<ParamField body="metadata" type="object">
  Метаданные для передачи дополнительных настроек

  <Expandable title="Поля metadata">
    <ParamField body="response_format" type="string" default="png">
      Формат выходного изображения

      Варианты: `png`, `jpg`
    </ParamField>

    <ParamField body="safety_tolerance" type="integer" default={2}>
      Допуск фильтра безопасности

      Диапазон: `0` - `6`, чем выше значение — тем выше допуск
    </ParamField>

    <ParamField body="prompt_upsampling" type="boolean" default={false}>
      Включить усиление промпта

      * `true`: автоматическая оптимизация/расширение промпта
      * `false`: использовать исходный промпт (по умолчанию)
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

### Сценарий 1: Текст-в-изображение

```json theme={null}
{
  "model": "flux-kontext-pro",
  "prompt": "Киберпанк-город ночью, неоновые огни",
  "size": "16:9",
  "metadata": {
    "response_format": "png",
    "safety_tolerance": 2
  }
}
```

### Сценарий 2: Редактирование изображения

```json theme={null}
{
  "model": "flux-kontext-max",
  "prompt": "Преобразовать это фото в акварельный стиль",
  "size": "match_input_image",
  "image_urls": ["https://example.com/photo.jpg"],
  "metadata": {
    "response_format": "jpg",
    "prompt_upsampling": true
  }
}
```

### Сценарий 3: Высококачественная генерация (модель Max)

```json theme={null}
{
  "model": "flux-kontext-max",
  "prompt": "Милый рыжий кот на подоконнике на солнце, реалистичный стиль",
  "size": "1:1",
  "metadata": {
    "response_format": "png",
    "safety_tolerance": 3
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
      "model": "flux-kontext-pro",
      "prompt": "Киберпанк-город ночью, неоновые огни",
      "size": "16:9",
      "metadata": {
        "response_format": "png"
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
          "model": "flux-kontext-pro",
          "prompt": "Киберпанк-город ночью, неоновые огни",
          "size": "16:9",
          "metadata": {
              "response_format": "png"
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
      model: 'flux-kontext-pro',
      prompt: 'Киберпанк-город ночью, неоновые огни',
      size: '16:9',
      metadata: {
        response_format: 'png'
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
    "model": "flux-kontext-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
