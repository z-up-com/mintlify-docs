---
title: "MiniMax-Hailuo-02 Генерация видео"
---
> Генерация видео с помощью модели MiniMax Hailuo 02

* Режим асинхронной обработки, возвращает ID задачи для последующих запросов
* Поддержка текст-в-видео, изображение-в-видео (первый/последний кадр)
* Поддержка длительности 5 и 10 секунд, доступны различные разрешения
* Поддержка автоматической оптимизации промпта и управления водяным знаком

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`, `first_frame_image` и `last_frame_image`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

## Аутентификация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Параметры запроса

<ParamField body="model" type="string" required>
  Название модели генерации видео

  Фиксированное значение: `MiniMax-Hailuo-02`
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание содержимого видео

  Рекомендуется детально описать сцену, действие, стиль для лучших результатов генерации

  Пример: `"Милый котенок бежит по траве"`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

  Доступные значения:

  * `5` - 5-секундное видео
  * `10` - 10-секундное видео

  По умолчанию: `5`

  **Ограничение 1080p**: При использовании разрешения 1080p поддерживается только длительность 5 секунд
</ParamField>

<ParamField body="metadata" type="object">
  Расширенные параметры конфигурации для генерации видео

  <Expandable title="Показать описание полей metadata">
    <ParamField body="metadata.resolution" type="string" default="768p">
      Разрешение видео

      Доступные значения:

      * `512p` - Стандартное разрешение
      * `768p` - Высокое разрешение
      * `1080p` - Полное HD (поддерживает только 5-секундную длительность)

      По умолчанию: `768p`
    </ParamField>

    <ParamField body="metadata.prompt_optimizer" type="boolean" default="true">
      Автоматическая оптимизация промпта

      При включении система автоматически оптимизирует ваш промпт для лучших результатов генерации

      По умолчанию: `true`
    </ParamField>

    <ParamField body="metadata.fast_pretreatment" type="boolean" default="false">
      Сокращение времени оптимизации промпта

      При включении ускоряет обработку, но может незначительно повлиять на качество оптимизации

      По умолчанию: `false`
    </ParamField>

    <ParamField body="metadata.watermark" type="boolean" default="false">
      Добавить водяной знак

      По умолчанию: `false`
    </ParamField>

    <ParamField body="metadata.first_frame_image" type="string">
      Изображение начального кадра видео

      **⚠️ Только формат URL (base64 больше не поддерживается)**

      * Публично доступный URL изображения (http\:// или https\://)
      * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

      Используется для указания начального кадра видео
    </ParamField>

    <ParamField body="metadata.last_frame_image" type="string">
      Изображение конечного кадра видео

      **⚠️ Только формат URL (base64 больше не поддерживается)**

      * Публично доступный URL изображения (http\:// или https\://)
      * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

      Используется для указания конечного кадра видео
    </ParamField>
  </Expandable>
</ParamField>

## Ограничения параметров

| Ограничение        | Описание                                            |
| ------------------ | --------------------------------------------------- |
| Длительность       | Поддерживает только 5 или 10 секунд                 |
| Разрешение 1080p   | Поддерживает только 5-секундную длительность        |
| Формат изображения | Только формат URL (base64 больше не поддерживается) |

## Комбинации разрешения и длительности

| Разрешение | Поддерживаемые длительности | Примечание            |
| ---------- | --------------------------- | --------------------- |
| 512p       | 5с, 10с                     | Все поддерживается    |
| 768p       | 5с, 10с                     | Все поддерживается    |
| 1080p      | 5с                          | 10с не поддерживается |

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

## Сценарии использования

### Сценарий 1: Быстрая генерация текст-в-видео

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Милый котенок бежит по траве, солнечный день"
}
```

### Сценарий 2: Генерация высококачественного видео 1080p

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Ночная городская сцена, мерцающие неоновые огни, поток транспорта",
  "duration": 5,
  "metadata": {
    "resolution": "1080p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### Сценарий 3: Генерация видео на основе первого кадра

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Человек медленно поворачивается, улыбается",
  "duration": 5,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/portrait.jpg"
  }
}
```

### Сценарий 4: Переходное видео с контролем первого и последнего кадра

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Сцена постепенно переходит от дня к ночи, градиент неба",
  "duration": 10,
  "metadata": {
    "resolution": "768p",
    "first_frame_image": "https://example.com/day.jpg",
    "last_frame_image": "https://example.com/night.jpg",
    "prompt_optimizer": true
  }
}
```

### Сценарий 5: Режим быстрой предварительной обработки

```json theme={null}
{
  "model": "MiniMax-Hailuo-02",
  "prompt": "Волны бьются о пляж, время заката",
  "duration": 5,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "fast_pretreatment": true
  }
}
```

<Note>
  **Запрос результатов задачи**

  Генерация видео - асинхронная задача. После отправки возвращается `task_id`. Используйте интерфейс [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса генерации и результатов.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "MiniMax-Hailuo-02",
      "prompt": "Милый котенок бежит по траве",
      "duration": 5,
      "metadata": {
        "resolution": "768p",
        "prompt_optimizer": true,
        "fast_pretreatment": false,
        "watermark": false
      }
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
          "model": "MiniMax-Hailuo-02",
          "prompt": "Милый котенок бежит по траве",
          "duration": 5,
          "metadata": {
              "resolution": "768p",
              "prompt_optimizer": True,
              "fast_pretreatment": False,
              "watermark": False
          }
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'MiniMax-Hailuo-02',
      prompt: 'Милый котенок бежит по траве',
      duration: 5,
      metadata: {
        resolution: '768p',
        prompt_optimizer: true,
        fast_pretreatment: false,
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
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "MiniMax-Hailuo-02",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "resolution": "768p"
    }
  }
  ```
</ResponseExample>
