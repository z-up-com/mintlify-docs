---
title: "Генерация видео Kling v3"
---
> Генерация высококачественных видео с помощью модели Kling v3, поддержка текст-в-видео и изображение-в-видео

* Асинхронный режим обработки, возвращает ID задачи для последующих запросов
* Поддержка текст-в-видео, изображение-в-видео (первый кадр / первый-последний кадр)
* Поддержка стандартного режима (720P) и профессионального режима (1080P)
* Текст-в-видео поддерживает длительность 15 секунд

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
  Название модели генерации видео, фиксированное значение `kling-v3`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовый промпт, максимум **2500 символов**

  Подробно описывайте сцены, действия и стиль для лучших результатов

  Пример: `"Золотой кот бежит по залитому солнцем лугу, замедленная съёмка, кинематографическое качество"`
</ParamField>

<ParamField body="mode" type="string" default="std">
  Режим генерации

  Варианты:

  * `std` - Стандартный режим (720P)
  * `pro` - Профессиональный режим (1080P)

  По умолчанию: `std`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

  Варианты: `5`, `10` или `15`

  По умолчанию: `5`

  <Warning>
    **15 секунд** поддерживается только для текст-в-видео. Изображение-в-видео — максимум 10 секунд.
  </Warning>
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Соотношение сторон видео

  Варианты:

  * `16:9` - Альбомная ориентация (по умолчанию)
  * `9:16` - Портретная ориентация
  * `1:1` - Квадрат

  По умолчанию: `16:9`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL изображений для режима изображение-в-видео

  * **1 изображение**: используется как первый кадр
  * **2 изображения**: автоматически назначаются как первый + последний кадр

  Максимум 2 изображения

  Пример: `["https://example.com/first.jpg"]`

  <Warning>
    * Максимум 2 изображения
    * URL изображений должны быть публично доступны
    * При изображение-в-видео `aspect_ratio` может быть переопределён реальным соотношением сторон изображения
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Специальные параметры расширения Kling v3

  <Expandable title="Поля metadata">
    <ParamField body="negative_prompt" type="string">
      Негативный промпт для исключения нежелательного контента

      Пример: `"размытый, низкое качество, искажённый"`
    </ParamField>

    <ParamField body="last_frame_image" type="string">
      URL изображения последнего кадра для изображение-в-видео

      Альтернатива: передать 2 изображения в `image_urls`. Второе будет использовано как последний кадр.
    </ParamField>

    <ParamField body="watermark" type="boolean">
      Добавить водяной знак
    </ParamField>
  </Expandable>
</ParamField>

## Матрица поддержки функций

| Тип                 | Функция        | std 5s | std 10s | std 15s | pro 5s | pro 10s |
| ------------------- | -------------- | ------ | ------- | ------- | ------ | ------- |
| Текст-в-видео       | Генерация      | ✅      | ✅       | ✅       | ✅      | ✅       |
| Изображение-в-видео | Генерация      | ✅      | ✅       | -       | ✅      | ✅       |
| Изображение-в-видео | Первый кадр    | ✅      | ✅       | -       | ✅      | ✅       |
| Изображение-в-видео | Последний кадр | ✅      | ✅       | -       | ✅      | ✅       |

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

### Сценарий 1: Текст-в-видео (стандартный режим)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Золотой кот бежит по залитому солнцем лугу, замедленная съёмка, кинематографическое качество",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### Сценарий 2: Текст-в-видео (профессиональный режим + негативный промпт)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Перекрёсток Сибуя в Токио ночью, неоновые огни отражаются на мокрой земле, люди идут с зонтами",
  "metadata": {
    "negative_prompt": "размытый, низкое качество, искажённый"
  },
  "mode": "pro",
  "duration": 10,
  "aspect_ratio": "16:9"
}
```

### Сценарий 3: Текст-в-видео (15 секунд)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Цветок в саду расцветает в замедленной съёмке",
  "duration": 15,
  "aspect_ratio": "16:9"
}
```

### Сценарий 4: Изображение-в-видео (первый кадр)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Кот медленно идёт вперёд, оглядывается",
  "image_urls": ["https://example.com/cat.jpg"],
  "mode": "std",
  "duration": 5
}
```

### Сценарий 5: Изображение-в-видео (первый + последний кадр)

```json theme={null}
{
  "model": "kling-v3",
  "prompt": "Плавный кинематографический переход",
  "image_urls": [
    "https://example.com/frame-start.jpg",
    "https://example.com/frame-end.jpg"
  ],
  "mode": "std",
  "duration": 5
}
```

<Note>
  **Запрос результатов задачи**

  Генерация видео является асинхронной задачей, после отправки возвращается ID задачи. Используйте конечную точку [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов генерации.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "kling-v3",
      "prompt": "Золотой кот бежит по залитому солнцем лугу, замедленная съёмка",
      "mode": "std",
      "duration": 5,
      "aspect_ratio": "16:9"
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
          "model": "kling-v3",
          "prompt": "Золотой кот бежит по залитому солнцем лугу, замедленная съёмка",
          "mode": "std",
          "duration": 5,
          "aspect_ratio": "16:9"
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
      model: 'kling-v3',
      prompt: 'Золотой кот бежит по залитому солнцем лугу, замедленная съёмка',
      mode: 'std',
      duration: 5,
      aspect_ratio: '16:9'
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "kling-v3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
